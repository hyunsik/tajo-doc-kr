*************************************
SequenceFile
*************************************

-----------------------------------------
소개
-----------------------------------------

SequenceFiles 은 바이너리 키/값 쌍이 일렬로 구성되어 있는 저장 포맷입니다.
이는 하둡에서 제공하는 기본적인 파일 포맷이고, 하이브 또한 SequenceFile을 사용한 테이블 생성 기능을 지원합니다.

``USING sequencefile`` 키워드는 SequecneFile 을 사용할 수 있도록 해줍니다. 
아래에 ``SequecneFile``을 사용한 테이블 생성 예제가 있습니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name text, score float, type text)
 USING sequencefile;

또한 Tajo는 하이브의 SequenceFile과의 호환성 또한 제공합니다. 위의 예제는 하이브에서 아래와 같이 사용될 수 있습니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name string, score float, type string)
 STORED AS sequencefile;

-----------------------------------------
SerializerDeserializer (SerDe)
-----------------------------------------

아래에 SequenceFile 에 대한 두가지 (역)직렬화 클래스가 기술되어 있습니다.

 + TextSerializerDeserializer: 일반 텍스트 파일 포맷의 읽기/쓰기를 지원하는 클래스 입니다.
 + BinarySerializerDeserializer: 바이너리 파일 포맷의 읽기/쓰기를 지원하는 클래스 입니다.

기본으로 SerDe를 위해 설정되어 있는 클래스는 ``TextSerializerDeserializer`` 클래스 입니다.
아래의 예제는 ``TextSerializerDeserializer`` 를 사용해 테이블을 만드는 예제 입니다.
여러분이 ``BinarySerializerDeserializer`` 클래스를 사용하고 싶으시다면, ``sequencefile.serde`` 키워드에 명시해 주시면 됩니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name text, score float, type text)
 USING sequencefile with ('sequencefile.serde'='org.apache.tajo.storage.BinarySerializerDeserializer')

하이브에서, 위의 예제는 아래처럼 사용될 수 있습니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name string, score float, type string)
 ROW FORMAT SERDE
  'org.apache.hadoop.hive.serde2.lazybinary.LazyBinarySerDe'
 STORED AS sequencefile;

-----------------------------------------
Writer
-----------------------------------------

키/값 쌍들의 압축을 위해 사용되는 ``SequenceFile.CompressionType`` 값에 따라 세가지 SequenceFile Writer가 제공됩니다.

 + Writer : 압축되지 않은 레코드를 위한 Writer.
 + RecordCompressWriter : 레코드 단위로 압축된 파일을 위한 Writer. 참고로 값들에 대해서만 압축을 수행합니다.
 + BlockCompressWriter : 블록 단위로 압축된 파일을 위한 Writer. 참고로 여러개의 키/값 쌍 들이 `블록` 으로 구성되어 함께 압축됩니다. 참고로 `블록`의 크기는 조절 가능합니다.

Tajo의 기본 Writer는 압축되지 않은 레코드를 위한 ``Writer``로 설정되어 있습니다. 
여러분이 ``RecordCompressWriter`` 를 사용하고자 하신다면, ``compression.type`` 키워드와 ``compression.codec`` 키워드를 통해 지정해주시면 됩니다.
아래는 이와 관련된 예제입니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name text, score float, type text)
 USING sequencefile with ('compression.type'='RECORD','compression.codec'='org.apache.hadoop.io.compress.SnappyCodec')

하이브에서는 아래와 같은 방법으로 설정할 수 있습니다.

.. code-block:: sql

 hive> SET hive.exec.compress.output = true;
 hive> SET mapred.output.compression.type = RECORD;
 hive> SET mapred.output.compression.codec = org.apache.hadoop.io.compress.SnappyCodec;
 hive> CREATE TABLE table1 (id int, name string, score float, type string) STORED AS sequencefile;;

여러분이 ``BlockCompressWriter`` 를 사용하고자 하신다면, ``compression.type`` 키워드와 ``compression.codec`` 키워드를 통해 지정해주시면 됩니다.
아래는 이와 관련된 예제입니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name text, score float, type text)
 USING sequencefile with ('compression.type'='BLOCK','compression.codec'='org.apache.hadoop.io.compress.SnappyCodec')

하이브에서는 아래와 같은 방법으로 설정할 수 있습니다.

.. code-block:: sql

 hive> SET hive.exec.compress.output = true;
 hive> SET mapred.output.compression.type = BLOCK;
 hive> SET mapred.output.compression.codec = org.apache.hadoop.io.compress.SnappyCodec;
 hive> CREATE TABLE table1 (id int, name string, score float, type string) STORED AS sequencefile;;

참고로, ``TextSerDe`` 혹은 ``BinarySerDe`` 를 압축 키워드들과 함께 사용할 수 있습니다.
아래는 이에 대한 예제입니다.

.. code-block:: sql

 CREATE TABLE table1 (id int, name text, score float, type text)
 USING sequencefile with ('sequencefile.serde'='org.apache.tajo.storage.BinarySerializerDeserializer', 'compression.type'='BLOCK','compression.codec'='org.apache.hadoop.io.compress.SnappyCodec')

하이브에서는 아래와 같은 방법으로 설정할 수 있습니다.

.. code-block:: sql

 hive> SET hive.exec.compress.output = true;
 hive> SET mapred.output.compression.type = BLOCK;
 hive> SET mapred.output.compression.codec = org.apache.hadoop.io.compress.SnappyCodec;
 hive> CREATE TABLE table1 (id int, name string, score float, type string)
       ROW FORMAT SERDE
         'org.apache.hadoop.hive.serde2.lazybinary.LazyBinarySerDe'
       STORED AS sequencefile;;
