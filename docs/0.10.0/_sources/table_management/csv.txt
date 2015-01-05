*************************************
CSV (TextFile)
*************************************

Character-separated values (CSV) 파일은 로우와 컬럼으로 구성된 표로 나타내어진 데이터 셋입니다.
각각의 로우들은 일반 텍스트 라인입니다. 하나의 라인은 ``\n`` 혹은 ``\r`` 로 나누어 집니다.
라인피드 ``\n`` 는 타조의 기본 구분자 입니다. 각각의 레코드는 여러개의 필드를 가지고 있으며, 여러가지 문자열 혹은 ``|``, 컴마 ``,`` 혹은 탭 ``\t`` 으로 분리됩니다.
타조는 ``|`` 를 기본 필드 구분자로 사용합니다.

=========================================
어떻게 CSV 테이블을 만드나요?
=========================================

아직 CREATE TABLE 선언문에 익숙하지 않다면,  데이터 정의 언어 (DDL, Data Definition Language) :doc:`/sql_language/ddl` 을 참고해 주세요.

특정한 파일 포맷을 테이블에 지정하기 위해서는 ``CREATE TABLE`` 절에 ``USING`` 선언을 사용하면 됩니다.
아래는 csv 파일을 사용해 테이블을 만드는 방법에 대한 예제입니다. 

.. code-block:: sql

 CREATE TABLE
  table1 (
    id int,
    name text,
    score float,
    type text
  ) USING CSV;

=========================================
물리적 속성들
=========================================

몇몇 테이블 저장 포맷은 테이블의 물리적 속성을 조정할 수 있는 기능을 허용하거나 허용하지 않도록 설정할 수 있는 파라메터를 제공합니다.
``CREATE TABLE`` 선언문에  ``WITH`` 절을 사용하여 여러 파라메터들을 설정할 수 있습니다.

아래의 여러 설정값들을 통해 CSV 저장 포맷에 대한 여러가지 물리적 속성들을 변경할 수 있습니다.

* ``text.delimiter``: 구분자에 대한 속성 입니다.  보통 ``|`` 혹은 ``\u0001`` 가 사용되고, 기본 구분자로는 ``|`` 가 사용됩니다.
* ``text.null``: NULL 문자에 대한 속성입니다.  NULL 문자의 기본값은 공백 문자열  ``''`` 입니다.  참고로, 아파치 하이브의 기본 NULL 문자는 ``'\\N'`` 입니다.
* ``compression.codec``: 압축 코덱에 대한 속성입니다. 압축에 대해 설정한 후 특정한 압축 알고리즘을 설정할 수 있습니다. 압축 알고리즘은 파일을 압축하는데 사용됩니다.  압축 코덱의 이름은 정확한 상속 정보 (`org.apache.hadoop.io.compress.CompressionCodec <https://hadoop.apache.org/docs/current/api/org/apache/hadoop/io/compress/CompressionCodec.html>`_) 를 포함한 클래스 이름을 지정해주어야 합니다. 기본적으로 압축은 사용되지 않도록 설정되어 있습니다.
* ``csvfile.serde`` (deprecated): 커스텀 (De)serializer 클래스에 대한 속성 입니다. ``org.apache.tajo.storage.TextSerializerDeserializer`` 가 기본 (De)serializer 클래스로 설정 되어 있습니다.
* ``timezone``: 테이블에 데이터를 쓸 때 사용되는 타임존에 대한 속성입니다. 테이블 로우들을 읽거나 쓸 때, ```timestamp``` 와 ```time``` 컬럼 값들은 타임존이 설정 되어 있을 경우 설정된 타임존에 맞게 값을 변경합니다. 타임존은 'PST' 혹은 'DST'와 같은 형태로 축약 될 수 이습니다. 또한 오프셋 기반의 형태 (UTC+9) 혹은 지역기반의 형태 (Asia/Seoul) 로 설정될 수 있습니다. 
* ``text.error-tolerance.max-num``: the maximum number of permissible parsing errors. This value should be an integer value. By default, ``text.error-tolerance.max-num`` is ``0``. According to the value, parsing errors will be handled in different ways.
  * If ``text.error-tolerance.max-num < 0``, all parsing errors are ignored.
  * If ``text.error-tolerance.max-num == 0``, any parsing error is not allowed. If any error occurs, the query will be failed. (default)
  * If ``text.error-tolerance.max-num > 0``, the given number of parsing errors in each task will be pemissible.

The following example is to set a custom field delimiter, NULL character, and compression codec:

.. code-block:: sql

 CREATE TABLE table1 (
  id int,
  name text,
  score float,
  type text
 ) USING CSV WITH('text.delimiter'='\u0001',
                  'text.null'='\\N',
                  'compression.codec'='org.apache.hadoop.io.compress.SnappyCodec');

.. warning::

  Be careful when using ``\n`` as the field delimiter because CSV uses ``\n`` as the line delimiter.
  At the moment, Tajo does not provide a way to specify the line delimiter.

=========================================
Custom (De)serializer
=========================================

The CSV storage format not only provides reading and writing interfaces for CSV data but also allows users to process custom
plan-text file formats with user-defined (De)serializer classes.
For example, with custom (de)serializers, Tajo can process JSON file formats or any specialized plan-text file formats.

In order to specify a custom (De)serializer, set a physical property ``csvfile.serde``.
The property value should be a fully qualified class name.

For example:

.. code-block:: sql

 CREATE TABLE table1 (
  id int,
  name text,
  score float,
  type text
 ) USING CSV WITH ('csvfile.serde'='org.my.storage.CustomSerializerDeserializer')


=========================================
Null Value Handling Issues
=========================================
In default, NULL character in CSV files is an empty string ``''``.
In other words, an empty field is basically recognized as a NULL value in Tajo.
If a field domain is ``TEXT``, an empty field is recognized as a string value ``''`` instead of NULL value.
Besides, You can also use your own NULL character by specifying a physical property ``text.null``.

=========================================
Compatibility Issues with Apache Hive™
=========================================

CSV files generated in Tajo can be processed directly by Apache Hive™ without further processing.
In this section, we explain some compatibility issue for users who use both Hive and Tajo.

If you set a custom field delimiter, the CSV tables cannot be directly used in Hive.
In order to specify the custom field delimiter in Hive, you need to use ``ROW FORMAT DELIMITED FIELDS TERMINATED BY``
clause in a Hive's ``CREATE TABLE`` statement as follows:

.. code-block:: sql

 CREATE TABLE table1 (id int, name string, score float, type string)
 ROW FORMAT DELIMITED FIELDS TERMINATED BY '|'
 STORED AS TEXT

To the best of our knowledge, there is not way to specify a custom NULL character in Hive.
