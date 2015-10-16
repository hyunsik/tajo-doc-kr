*************************************
HBase 통합
*************************************

Apache Tajo 저장소는 Apache HBase와의 통합을 지원합니다.
이 통합은 Tajo가 Apache HBase에서 사용되는 모든 테이블에 접근하는 것을 허용합니다.

이 기능을 이용하기 위해서, ``conf/tajo-env.sh`` 에 설정을 추가하고 테이블 생성문에 속성을 더해야 합니다.

이 장에서는 HBase 통합을 하는 방법을 서술합니다.

먼저, 아래와 같이 ``conf/tajo-env.sh`` 에 환경변수 ``HBASE_HOME`` 를 HBase 홈 디렉토리로 설정합니다: ::

  export HBASE_HOME=/path/to/your/hbase/directory

디렉토리를 설정하면, Tajo는 HBase 라이브러리 파일을 클래스패스에 추가할 것입니다.



========================
테이블 생성
========================

*개요*

.. code-block:: sql

  CREATE [EXTERNAL] TABLE [IF NOT EXISTS] <table_name> [(<column_name> <data_type>, ... )]
  USING hbase
  WITH ('table'='<hbase_table_name>'
  , 'columns'=':key,<column_family_name>:<qualifier_name>, ...'
  , 'hbase.zookeeper.quorum'='<zookeeper_address>'
  , 'hbase.zookeeper.property.clientPort'='<zookeeper_client_port>'
  )

선택사항

* ``table`` : hbase 원본 테이블의 이름을 설정합니다. 만약 외부 테이블을 생성하고싶다면, 테이블은 반드시 HBase에 존재해야 합니다. 반면에, 만약 관리 테이블을 생성하고 싶다면, 테이블은 반드시 HBase에 존재하지 않아야 합니다.
* ``columns`` : 키 는 HBase의 열 키를 뜻합니다. 열 명부의 갯수는 Tajo 테이블 열의 갯수와 같아야 합니다.
* ``hbase.zookeeper.quorum`` : zookeeper 정족수 주소를 설정합니다. 당신은 동일한 Tajo 데이터베이스를 사용하는 다른 zookeeper 클러스터를 사용할 수 있습니다. 만약 zookeeper 주소를 설정하지 않는다면, Tajo는 ``hbase-site.xml`` 파일의 속성을 따를 것입니다.
* ``hbase.zookeeper.property.clientPort`` : zookeeper 클라이언트 포트를 설정합니다. 만약 포트를 설정하지 않는다면, Tajo는 ``hbase-site.xml`` 파일의 속성을 따를 것입니다.

``IF NOT EXISTS`` 는 테이블이 존재하지 않을 때 발생하는 에러를 피하기 위해 ``CREATE [EXTERNAL] TABLE`` 문을 허용합니다.



========================
 테이블 삭제
========================

*Synopsis*

.. code-block:: sql

  DROP TABLE [IF EXISTS] <table_name> [PURGE]

``IF EXISTS`` 는 테이블이 존재하지 않을 때 발생하는 에러를 피하기 위해 ``DROP TABLE`` 문을 허용합니다. ``DROP TABLE`` 문은 Tajo 카탈로그에서 테이블을 제거하지만, HBase 클러스터에 있는 내용은 제거하지 않습니다. 만약 ``PURGE`` 옵션이 주어진다면, ``DROP TABLE`` 문은 HBase 클러스터의 내용 뿐만 아니라 카탈로그의 엔트리까지 제거할 것입니다.


========================
 삽입(덮어쓰기)
========================

``INSERT OVERWRITE`` 문은 현존하는 테이블의 테이블 데이터를 덮어씁니다. Tajo의 ``INSERT OVERWRITE`` 문은 SQL의 ``INSERT INTO SELECT`` 문을 따릅니다. 아래는 예시입니다:

.. code-block:: sql

  -- 목표 테이블의 도식과 출력 테이블의 도식이 동일할 때
  INSERT OVERWRITE INTO t1 SELECT l_orderkey, l_partkey, l_quantity FROM lineitem;
  -- 또는
  INSERT OVERWRITE INTO t1 SELECT * FROM lineitem;

  -- 출력 테이블의 도식이 목표 테이블의 도식보다 작을 때
  INSERT OVERWRITE INTO t1 SELECT l_orderkey FROM lineitem;

  -- 특정 목표 열을 지정하고 싶을 때
  INSERT OVERWRITE INTO t1 (col1, col3) SELECT l_orderkey, l_quantity FROM lineitem;


.. note::

  만약 열 키를 지정하지 않는다면, 테이블 데이터를 전혀 사용할 수 없습니다. 왜냐하면 Tajo는 결과 데이터를 생성하기 이전에 정렬하기 위하여 몇몇의 키 컬럼을 필요로 하기 때문입니다.



========================
용례
========================

Tajo에 의해 관리되는 새 HBase 테이블을 생성하기 위해서, ``CREATE TABLE`` 에 ``USING`` 절을 사용합니다:

.. code-block:: sql

  CREATE EXTERNAL TABLE blog (rowkey text, author text, register_date text, title text)
  USING hbase WITH (
    'table'='blog'
    , 'columns'=':key,info:author,info:date,content:title');

위 명령을 실행하면, 당신은 HBase 쉘에서 새 테이블을 볼 수 있습니다:

.. code-block:: sql

  $ hbase shell
  create 'blog', {NAME=>'info'}, {NAME=>'content'}
  put 'blog', 'hyunsik-02', 'content:title', 'Getting started with Tajo on your desktop'
  put 'blog', 'hyunsik-02', 'info:author', 'Hyunsik Choi'
  put 'blog', 'hyunsik-02', 'info:date', '2014-12-03'
  put 'blog', 'blrunner-01', 'content:title', 'Apache Tajo: A Big Data Warehouse System on Hadoop'
  put 'blog', 'blrunner-01', 'info:author', 'Jaehwa Jung'
  put 'blog', 'blrunner-01', 'info:date', '2014-10-31'
  put 'blog', 'jhkim-01', 'content:title', 'APACHE TAJO�� v0.9 HAS ARRIVED!'
  put 'blog', 'jhkim-01', 'info:author', 'Jinho Kim'
  put 'blog', 'jhkim-01', 'info:date', '2014-10-22'

이에 따라 테이블이 생성되고, ``\d`` 옵션으로 테이블의 메타 데이터를 질의합니다:

.. code-block:: sql

  default> \d blog;

  table name: default.blog
  table path:
  store type: HBASE
  number of rows: unknown
  volume: 0 B
  Options:
          'columns'=':key,info:author,info:date,content:title'
          'table'='blog'

  schema:
  rowkey  TEXT
  author  TEXT
  register_date   TEXT
  title   TEXT


이어서 아래와 같이 테이블을 질의합니다:

.. code-block:: sql

  default> SELECT * FROM blog;
  rowkey,  author,  register_date,  title
  -------------------------------
  blrunner-01,  Jaehwa Jung,  2014-10-31,  Apache Tajo: A Big Data Warehouse System on Hadoop
  hyunsik-02,  Hyunsik Choi,  2014-12-03,  Getting started with Tajo on your desktop
  jhkim-01,  Jinho Kim,  2014-10-22,  APACHE TAJO�� v0.9 HAS ARRIVED!

  default> SELECT * FROM blog WHERE rowkey = 'blrunner-01';
  Progress: 100%, response time: 2.043 sec
  rowkey,  author,  register_date,  title
  -------------------------------
  blrunner-01,  Jaehwa Jung,  2014-10-31,  Apache Tajo: A Big Data Warehouse System on Hadoop


이것은 HBase 테이블에 데이터를 삽입하는 방법입니다:

.. code-block:: sql

  CREATE TABLE blog_backup(rowkey text, author text, register_date text, title text)
  USING hbase WITH (
    'table'='blog_backup'
    , 'columns'=':key,info:author,info:date,content:title');
  INSERT OVERWRITE INTO blog_backup SELECT * FROM blog;


HBase 쉘을 사용하여 데이터가 잘 삽입되었는지 검증합니다:

.. code-block:: sql

  hbase(main):004:0> scan 'blog_backup'
   ROW          COLUMN+CELL
   blrunner-01  column=content:title, timestamp=1421227531054, value=Apache Tajo: A Big Data Warehouse System on Hadoop
   blrunner-01  column=info:author, timestamp=1421227531054, value=Jaehwa Jung
   blrunner-01  column=info:date, timestamp=1421227531054, value=2014-10-31
   hyunsik-02   column=content:title, timestamp=1421227531054, value=Getting started with Tajo on your desktop
   hyunsik-02   column=info:author, timestamp=1421227531054, value=Hyunsik Choi
   hyunsik-02   column=info:date, timestamp=1421227531054, value=2014-12-03
   jhkim-01     column=content:title, timestamp=1421227531054, value=APACHE TAJO\xE2\x84\xA2 v0.9 HAS ARRIVED!
   jhkim-01     column=info:author, timestamp=1421227531054, value=Jinho Kim
   jhkim-01     column=info:date, timestamp=1421227531054, value=2014-10-22
  3 row(s) in 0.0470 seconds


