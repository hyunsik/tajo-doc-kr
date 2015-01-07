*************************************
RCFile
*************************************

RCFile은 로우 컬럼 파일 (Record Columnar File) 의 줄임말로 바이너리 키/값 쌍이 일렬로 늘어져 있는 구조의 파일입니다.
이는 SequenceFile과 비슷한 형태입니다.

=============================================================================
Tajo에서 어떻게 RCFile 저장 포맷을 사용한 테이블을 만드나요?
=============================================================================

아직 CREATE TABLE 선언문에 익숙하지 않으시다면,  데이터 정의 언어 (DDL, Data Definition Language) :doc:`/sql_language/ddl` 을 참고해 주세요.

여러분의 테이블에 특정한 타일 포맷을 지정하시고 싶으시다면. ``CREATE TABLE`` 선언에 ``USING`` 절을 사용하시면 됩니다.
아래의 예제는 RCFile 포맷으로 테이블을 작성하는 방법을 설명합니다.

.. code-block:: sql

  CREATE TABLE table1 (
    id int,
    name text,
    score float,
    type text
  ) USING RCFILE;

=========================================
물리적 속성들
=========================================

몇몇 테이블 저장 포맷은 테이블의 물리적 속성을 조정할 수 있는 기능을 허용하거나 허용하지 않도록 설정할 수 있는 파라메터를 제공합니다.
``CREATE TABLE`` 선언문에  ``WITH`` 절을 사용하여 여러 파라메터들을 설정할 수 있습니다.

아래에 RCFile 저장 포맷의 여러가지 물리적 속성과 자세한 설명을 기술하였습니다.

* ``rcfile.serde`` : 임의의 (역)직렬화 클래스를 지정하는 속성입니다. ``org.apache.tajo.storage.BinarySerializerDeserializer`` 가 기본 (역)직렬화 클래스입니다.
* ``rcfile.null`` : NULL 문자를 지정하는 속성입니다.  하지만  ``org.apache.tajo.storage.TextSerializerDeserializer`` 를 (역)직렬화 클래스로 사용하실 경우에만 NULL문자를 이 속성을 통해 지정하실수 있습니다. 기본으로 지정된 NULL 문자는 공백 문자열 ``''`` 입니다. 하이브의 경우 기본 NULL 문자로 ``'\\N'`` 가 사용됩니다.
* ``compression.codec`` : 압축 코덱에 대한 속성입니다. 이를 통해 여러분이 원하는 압축 알고리즘을 지정하실 수 있습니다. 압축 알고리즘은 파일을 압축하는데 사용되며  `org.apache.hadoop.io.compress.CompressionCodec <https://hadoop.apache.org/docs/current/api/org/apache/hadoop/io/compress/CompressionCodec.html>`_ 을 상속하는 클래스를 지정하셔야 합니다. 기본적으로 Tajo에서는 압축기능을 사용하지 않습니다.

아래의 예제는 압축을 사용하는 RCFile 포맷으로 테이블을 만드는 방법을 설명합니다.

.. code-block:: sql

  CREATE TABLE table1 (
    id int,
    name text,
    score float,
    type text
  ) USING RCFILE WITH ('compression.codec'='org.apache.hadoop.io.compress.SnappyCodec');

=========================================
RCFile의 (역)직렬화 
=========================================

Tajo는  RCFile의 (역)직렬화를 위해 아래처럼 두가지 클래스를 기본으로 제공합니다.

* ``org.apache.tajo.storage.TextSerializerDeserializer``: 컬럼 값을 일반 텍스트 형태로 저장합니다.
* ``org.apache.tajo.storage.BinarySerializerDeserializer``: 컬럼 값을 바이너리 파일 형태로 저장합니다.

RCFile 포맷은 헤더 부분에 몇몇 메타데이터를 저장할 수 있습니다. Tajo의 경우 RCFile이 만들어질때 (역)직렬화 클래스의 이름을 메타데이터로 저장합니다.

.. 알림::

  ``org.apache.tajo.storage.BinarySerializerDeserializer`` 은 RCFile의 기본 (역)직렬화 클래스 입니다.

=========================================
아파치 하이브와의 호환성 이슈들
=========================================

아파치 하이브 혹은 Tajo를 통해 만들어진 RCFile은 두 시스템 모두에서 사용될 수 있습니다.
즉, Tajo는 하이브가 만든 RCFile을 처리할 수도 있고, 그 반대의 경우도 가능합니다.

하이브의 경우 RCFile에 아무런 메타데이터가 저장되어 있지 않기때문에, 물리적 속성을 사용해 (역)직렬화 클래스의 이름을 설정해주어야 합니다.

하이브에서는 두가지 (역)직렬화 클래스를 제공하는데, 이는 Tajo의 (역)직렬화 클래스와 동일하게 사용하실 수 있습니다.

* ``org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe``: Tajo의 ``TextSerializerDeserializer`` 클래스와 동일합니다.
* ``org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe``: Tajo의 ``BinarySerializerDeserializer`` 클래스와 동일합니다.

호환성과 관련된 이슈는 주로 존재하는 테이블을 가리키는 외부 테이블을 만들때 발생합니다.
아래의 섹션은 1) Tajo가 하이브로부터 만들어진 RCFile을 읽을 경우 와 2) 하이브에서 Tajo를 사용해 만들어진 RCFile을 읽을 때의 경우에 대해 설명합니다.

-----------------------------------------------------------------------------
1) Tajo에서 하이브를 사용해 만들어진 RCFile을 읽을 경우
-----------------------------------------------------------------------------

여러분이 하이브에서 ``ColumnarSerDe`` 클래스를 사용하여 만든 RCFile을 Tajo에서 사용하고 싶으실 경우
Tajo의 ``rcfile.serde`` 속성을 아래와 같이 설정해 주시면 됩니다.

.. code-block:: sql

  CREATE EXTERNAL TABLE table1 (
    id int,
    name text,
    score float,
    type text
  ) USING RCFILE with ( 'rcfile.serde'='org.apache.tajo.storage.TextSerializerDeserializer', 'rcfile.null'='\\N' )
  LOCATION '....';

이와 비슷하게, ``LazyBinaryColumnarSerDe`` 클래스를 사용해 하이브에서 만들어진 RCFile을 Tajo에서 사용하실 경우, 
아래의 방법을 이용하시면 됩니다.

.. code-block:: sql

  CREATE EXTERNAL TABLE table1 (
    id int,
    name text,
    score float,
    type text
  ) USING RCFILE WITH ('rcfile.serde' = 'org.apache.tajo.storage.BinarySerializerDeserializer')
  LOCATION '....';

.. 알림::

  ``BinarySerializerDeserializer`` 클래스는 RCFile의 기본 (역)직렬화 클래스 입니다.
    따라서 위의 클래스를 사용하셨을 경우, Tajo의 ``rcfile.serde`` 속성을 지정하실 필요가 없습니다.

-----------------------------------------------------------------------------
2) Tajo에서 하이브를 사용해 만들어진 RCFile을 읽을 경우
-----------------------------------------------------------------------------

여러분이 하이브에서 Tajo의 ``TextSerializerDeserializer`` 클래스를 사용해 만든 RCFile을 사용하려고 하신다면,
아래와 같이 ``SERDE`` 속성을 설정해 주세요.

.. code-block:: sql

  CREATE TABLE table1 (
    id int,
    name string,
    score float,
    type string
  ) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe' STORED AS RCFILE
  LOCATION '<hdfs_location>';

또한, 하이브에서 Tajo의 ``BinarySerializerDeserializer`` 클래스를 사용해 만든 RCFile을 사용하려고 하신다면,
아래와 같이 ``SERDE`` 속성을 설정해 주세요.

.. code-block:: sql

  CREATE TABLE table1 (
    id int,
    name string,
    score float,
    type string
  ) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe' STORED AS RCFILE
  LOCATION '<hdfs_location>';
