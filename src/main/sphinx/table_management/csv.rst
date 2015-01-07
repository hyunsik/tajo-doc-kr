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

특정한 파일 포맷을 테이블에 지정하기 위해서는 ``CREATE TABLE`` 절에 ``USING``선언을 사용하면 됩니다.
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
* ``text.null``: NULL 문자에 대한 속성입니다.  NULL 문자의 기본값은 공백 문자열  ``''`` 입니다.  참고로, 아파치 하이브의 기본 NULL 문자는 ``'\\N'``입니다.
* ``compression.codec``: 압축 코덱에 대한 속성입니다. 압축에 대해 설정한 후 특정한 압축 알고리즘을 설정할 수 있습니다. 압축 알고리즘은 파일을 압축하는데 사용됩니다.  압축 코덱의 이름은 정확한 상속 정보 (`org.apache.hadoop.io.compress.CompressionCodec <https://hadoop.apache.org/docs/current/api/org/apache/hadoop/io/compress/CompressionCodec.html>`_) 를 포함한 클래스 이름을 지정해주어야 합니다. 
기본적으로 압축은 사용되지 않도록 설정되어 있습니다.
* ``csvfile.serde`` (deprecated): 커스텀 (De)serializer 클래스에 대한 속성 입니다. ``org.apache.tajo.storage.TextSerializerDeserializer`` 가 기본 (De)serializer 클래스로 설정 되어 있습니다.
* ``timezone``: 테이블에 데이터를 쓸 때 사용되는 타임존에 대한 속성입니다. 테이블 로우들을 읽거나 쓸 때, ```timestamp``` 와 ```time``` 컬럼 값들은 타임존이 설정 되어 있을 경우 설정된 타임존에 맞게 값을 변경합니다. 타임존은 'PST' 혹은 'DST'와 같은 형태로 축약 될 수 이습니다. 또한 오프셋 기반의 형태 (UTC+9) 혹은 지역기반의 형태 (Asia/Seoul) 로 설정될 수 있습니다. 
* ``text.error-tolerance.max-num``: 허용되는 최대 파싱 오류 횟수에 대한 속성입니다. 해당 속성값은 반드시 정수여야 하며, 기본값은 0으로 설정되어 있습니다. 설정값에 따라서 파싱 오류는 다르게 처리될 수 있습니다.
  * If ``text.error-tolerance.max-num < 0``, 모든 파싱 오류를 무시합니다.
  * If ``text.error-tolerance.max-num == 0``, 어떤 파싱 오류도 허용되지 않습니다. 만일 파싱 에러가 발생한다면, 처리중인 질의는 실패합니다. (기본값)
  * If ``text.error-tolerance.max-num > 0``, 지정된 횟수 만큼 오류가 허용됩니다.

아래의 예제는 필드 구분자, NULL 문자, 그리고 압축 코덱을 설정하는 방법을 보여줍니다.

.. code-block:: sql

 CREATE TABLE table1 (
  id int,
  name text,
  score float,
  type text
 ) USING CSV WITH('text.delimiter'='\u0001',
                  'text.null'='\\N',
                  'compression.codec'='org.apache.hadoop.io.compress.SnappyCodec');

.. 주의하세요::

  ``\n`` 을 구분자로 사용할 때 주의하셔야 합니다. 이는 CSV 파일 포맷이 ``\n`` 를 레코드의 라인 구분자로 사용하기 때문입니다.
  이럴 경우 타조는 라인 구분자를 명시하는 방법을 제공하지 않습니다.

=========================================
직접 만든 (역)직렬화 클래스 사용하기
=========================================

CSV 저장 포맷은 여러분이 작성한 (역)직렬화 클래스를 적용할 수 있어 CSV 데이터를 읽고 쓰기 위한 인터페이스를 제공하는것 뿐만 아니라, 일반 텍스트 파일 포맷에 대한 처리 방법도 제공할 수 있습니다.
예를 들어, (역)직렬화 클래스를 새로 작성해 타조를 통해 JSON 파일 포맷 뿐만 아니라 특별한 텍스트 파일 또한 처리 할 수 있습니다.

여러분이 작성한 (역)직렬화 클래스는  ``csvfile.serde`` 속성에 지정해 주시면 됩니다. 이 속성의 값으로 여러분이 작성하신 (역)직렬화 클래스의 전체 이름을 지정하시면 됩니다.

예:

.. code-block:: sql

 CREATE TABLE table1 (
  id int,
  name text,
  score float,
  type text
 ) USING CSV WITH ('csvfile.serde'='org.my.storage.CustomSerializerDeserializer')


=========================================
Null 값 다루기
=========================================

기본적으로, CSV 파일 내의  NULL 값은 공백 문자열입니다. (``''``).
즉, 타조에서 공백 필드는 NULL 값으로 인식됩니다. 
하지만 필드의 도메인이 ``TEXT``일 경우, 공백 필드는 NULL 값 대신 공백 문자열 ``''`` 로 인식됩니다.
더불어, ``text.null`` 속성을 통해 NULL 문자로 인식하고 싶은 특별한 문자를 지정해 줄 수 있습니다.

=========================================
아파치 하이브와의 호환 이슈
=========================================

타조를 통해 만들어진 CSV 파일은 아파치 하이브에서 추가적인 처리 없이 바로 사용 가능합니다.
이 섹션은 아파치 하이브와 타조와의 몇가지 호환 이슈를 다룹니다.

만약 여러분이 임의의 필드 구분자를 사용하실 경우엔, CSV 테이블은 하이브에서 바로 처리 될 수 없습니다.
하이브에서 임의의 필드 구분자를 지정하시려면, 하이브에서 ``CREATE TABLE`` 절 안에서 ``ROW FORMAT DELIMITED FIELDS TERMINATED BY`` 절을 아래와 같이 사용하시면 됩니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name string, score float, type string)
 ROW FORMAT DELIMITED FIELDS TERMINATED BY '|'
 STORED AS TEXT

현재까지 하이브에서 NULL 문자를 특별하게 지정해 줄 수 있는 방법은 없습니다.
