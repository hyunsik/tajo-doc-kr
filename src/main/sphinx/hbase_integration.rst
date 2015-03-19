*************************************
HBase 통합
*************************************


아파치 타조 저장소는 아파치 HBase™ 와 통합을 지원합니다.
이것은 타조가 아파치 HBase의 모든 테이블을 사용하는것을 허용합니다. 

이 기능을 이용하기 위해서, ``conf/tajo-env.sh`` 에 몇가지 설정을 추가하고 테이블 생성문에 속성값을 더해야 합니다.

이번 절은 Hbase 통합을 어떻게 설정하는지 설명합니다. 

첫번째, 아래처럼 conf/tajo-env.sh 에서 Hbase 홈 디렉토리 환경변수 ``HBASE_HOME`` 를 설정합니다.

  export HBASE_HOME=/path/to/your/hbase/directory

디렉토리를 설정하면, 타조는 HBase 라이브러리 파일을 클래스패스에 추가할 것입니다.


========================
테이블 생성하기
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

옵션

* ``table`` : hbase 의 원본 테이블명을 입력합니다. 만약 외부 테이블 생성을 원한다면, 그 테이블은 반드시 HBase 상에 존재해야 합니다. 반면에,  managed table를 원한다면 HBase 상에 꼭 존재하지 않아도 됩니다.
* ``columns`` : :키는 HBase 로우 키를 의미합니다. 칼럼 목록의 갯수는 타조 테이블 칼럼의 수와 동일해야 합니다.
* ``hbase.zookeeper.quorum`` : 주키퍼 쿼럼 주소를 입력합니다. 같은 타조 데이터베이스에서 다른 주키퍼 클러스터를 사용할 수 있습니다. 만약 주키퍼 주소를 입력하지 않으면, 타조는 hbase-site.xml 파일의 속성을 인용합니다.
* ``hbase.zookeeper.property.clientPort`` : 주키퍼 클라이언트 포트를 입력합니다. 포트를 입력하지 않으면, 타조는 hbase-site.xml 파일의 속성을 인용합니다.

``IF NOT EXISTS`` allows ``CREATE [EXTERNAL] TABLE`` statement to avoid an error which occurs when the table does not exist.

``IF NOT EXISTS`` 는 ``CREATE [EXTERNAL] TABLE`` 문에서 테이블이 존재하지 않아서 발생하는 문제를 없애줍니다.




========================
  테이블 삭제
========================

*개요*

.. code-block:: sql

  DROP TABLE [IF EXISTS] <table_name> [PURGE]

``IF EXISTS`` allows ``DROP TABLE`` statement to avoid an error which occurs when the table does not exist. ``DROP TABLE`` statement removes a table from Tajo catalog, but it does not remove the contents on HBase cluster. If ``PURGE`` option is given, ``DROP TABLE`` statement will eliminate the entry in the catalog as well as the contents on HBase cluster.
``IF EXISTS`` 는 ``DROP TABLE`` 문에서 테이블이 존재하지 않아서 발생하는 문제를 없애줍니다. ``DROP TABLE`` 문은 타조 카탈로그에서 테이블은 삭제하지만, HBase 클러스터에 있는 내용은 삭제하지 않습니다. ``PURGE`` 옵션을 주면, ``DROP TABLE`` 문은  HBase 클러스터에 있는 내용과 더불어 카탈로그의 목록를 제거 할 수 있습니다.  



========================
 데이터 입력 (덮어쓰기)
========================

INSERT OVERWRITE 문은 이미 존재하는 테이블의 데이터를 덮어씁니다. 타조의 INSERT OVERWRITE 문은 ``INSERT INTO SELECT`` SQL 문이 뒤따릅니다. 예제는 다음과 같습니다 :

.. code-block:: sql

  -- 목적 테이블 스키마와 출력 스키마가 서로 동일할 때
  INSERT OVERWRITE INTO t1 SELECT l_orderkey, l_partkey, l_quantity FROM lineitem;
  -- 또는
  INSERT OVERWRITE INTO t1 SELECT * FROM lineitem;

  -- 출력 스키마가 목적 스키마보다 작을때
  INSERT OVERWRITE INTO t1 SELECT l_orderkey FROM lineitem;

  -- 명확한 목적 칼럼을 명시하길 원할때
  INSERT OVERWRITE INTO t1 (col1, col3) SELECT l_orderkey, l_quantity FROM lineitem;


.. 메모::

  로우 키 옵션을 설정하지 않으면, 테이블 데이터를 절대 사용할 수 없습니다. 왜냐하면, 타조는 결과 데이터를 생성하기전 정렬을 위해 키 칼럼을 가져야 합니다.






========================
사용법
========================

타조에 의해 관리되는 새로운 HBase 테이블을 만들기 위해, CREATE TABLE 에서 USING 문을 사용합니다.

.. code-block:: sql

  CREATE EXTERNAL TABLE blog (rowkey text, author text, register_date text, title text)
  USING hbase WITH (
    'table'='blog'
    , 'columns'=':key,info:author,info:date,content:title');

위의 명령어 실행후에, HBase 쉘에서 새 테이블이 출력되는지 확인해야 합니다.:

.. code-block:: sql

  $ hbase shell
  create 'blog', {NAME=>'info'}, {NAME=>'content'}
  put 'blog', 'hyunsik-02', 'content:title', 'Getting started with Tajo on your desktop'
  put 'blog', 'hyunsik-02', 'info:author', 'Hyunsik Choi'
  put 'blog', 'hyunsik-02', 'info:date', '2014-12-03'
  put 'blog', 'blrunner-01', 'content:title', 'Apache Tajo: A Big Data Warehouse System on Hadoop'
  put 'blog', 'blrunner-01', 'info:author', 'Jaehwa Jung'
  put 'blog', 'blrunner-01', 'info:date', '2014-10-31'
  put 'blog', 'jhkim-01', 'content:title', 'APACHE TAJO™ v0.9 HAS ARRIVED!'
  put 'blog', 'jhkim-01', 'info:author', 'Jinho Kim'
  put 'blog', 'jhkim-01', 'info:date', '2014-10-22'

테이블 생성하고나서 ``\d`` 옵션으로 테이블 메타 데이터를 질의 합니다.

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


그리고 테이블에 질의는 아래와 같습니다.

.. code-block:: sql

  default> SELECT * FROM blog;
  rowkey,  author,  register_date,  title
  -------------------------------
  blrunner-01,  Jaehwa Jung,  2014-10-31,  Apache Tajo: A Big Data Warehouse System on Hadoop
  hyunsik-02,  Hyunsik Choi,  2014-12-03,  Getting started with Tajo on your desktop
  jhkim-01,  Jinho Kim,  2014-10-22,  APACHE TAJO™ v0.9 HAS ARRIVED!

  default> SELECT * FROM blog WHERE rowkey = 'blrunner-01';
  Progress: 100%, response time: 2.043 sec
  rowkey,  author,  register_date,  title
  -------------------------------
  blrunner-01,  Jaehwa Jung,  2014-10-31,  Apache Tajo: A Big Data Warehouse System on Hadoop


Hbase 테이블에 데이터를 입력하는 방법입니다.

.. code-block:: sql

  CREATE TABLE blog_backup(rowkey text, author text, register_date text, title text)
  USING hbase WITH (
    'table'='blog_backup'
    , 'columns'=':key,info:author,info:date,content:title');
  INSERT OVERWRITE INTO blog_backup SELECT * FROM blog;


HBase 쉘을 이용해서 데이터가 실제로 적재되었는지 확인합니다.

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




