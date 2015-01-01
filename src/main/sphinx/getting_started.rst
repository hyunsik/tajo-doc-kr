***************
시작하기
***************

이 절에서는 로컬 파일 시스템 기반으로 동작하는 standalone 타조 인스턴스 셋업 방법을 설명합니다. 참고로, 다음에 이어지 절에서는, 아파치 하둡의 HDFS 위에서 타조 클러스터를 실행하는 방법이 설명되어 있습니다. 이 절에서 다루는 내용은, 타조 클러스터 구동, 테이블 생성, 타조 쉘을 이용한 SQL 질의 수행, 타조 클러스터 중지 방법입니다. 이 과정들을 수행하는데, 10분이 넘지 않을 것입니다.

===============
준비 사항
===============

 * Hadoop 2.3.0 or higher (up to 2.5.1)
 * Java 1.6 or 1.7
 * Protocol buffer 2.5.0 

=====================================
다운로드 및 소스코드 풀기
=====================================

타조 소스코드 릴리즈를 다운로드 받거나 Git에서 개발중인 코드베이스를 체크아웃합니다.


최신 소스코드 릴리즈 다운로드
-------------------------

`Apache Download Mirrors <http://www.apache.org/dyn/closer.cgi/tajo>`_ 페이지 목록에서 다운로드 사이트를 선택합니다.
타조 릴리즈 미러 링크 중에 하나를 클릭해서 접속합니다.
.tar.gz 파일을 로컬 파일시스템에 다운로드합니다. 예) tajo-x.y.z-src.tar.gz

다운로드된 파일의 압축과 tar를 풀고 해당 디렉토리로 이동합니다. ::

  tar xzvf tajo-x.y.z-src.tar.gz


Git을 통해 소스코드 체크아웃
--------------------------

개발중인 코드베이스는 `the Apache git repository <https://git-wip-us.apache.org/repos/asf/tajo.git>`_ 에서 다음과 같이 다운로드 받을 수 있습니다.: ::

  git clone https://git-wip-us.apache.org/repos/asf/tajo.git

미러링된 읽기 전용 git 저장소는 `Github <https://github.com/apache/tajo>`_입니다.
 

===========
소스코드 빌드
===========

준비 사항들과 소스가 준비되었다면, 이제 소크코드를 빌드할 수 있습니다.

설치 과정 첫 단계로, 자신의 시스템에 맞게 소스트리를 설정하고 원하는 옵션들을 선택합니다. 이 과정은 설정 스크립트를 실행해서 수행합니다. 기본 설치는 단순히 enter를 치면 됩니다.

소스코드를 컴파일하고 바이너리 아카이브 파일 생성을 위해 다음과 같이 명령어들을 실행합니다:

.. code-block:: bash

  $ cd tajo-x.y.z
  $ mvn clean install -DskipTests -Pdist -Dtar -Dhadoop.version=2.X.X
  $ ls tajo-dist/target/tajo-x.y.z-SNAPSHOT.tar.gz

.. note::

  하둡 버전을 지정하지 않으면, 타조 클러스터가 동작하지 않을 수 있기 때문에, maven build 명령을 통해 반드시 하둡 버전을 지정할 것을 권장합니다.

  예:

    $ mvn clean install -DskipTests -Pdist -Dtar -Dhadoop.version=2.5.1

다음은, 생성된 tar.gz 파일을 적절한 디렉토리에 옮긴 후, 압축을 풀기 위해 아래 명령들을 실행합니다:

.. code-block:: bash

  $ cd [a directory to be parent of tajo binary]
  $ tar xzvf ${TAJO_SRC}/tajo-dist/target/tajo-x.y.z-SNAPSHOT.tar.gz
  
==================================
로컬 클러스터 셋업
==================================

아파치 타조™는 로컬 모드와 분산 모드, 두가지 실행 모드를 지원합니다. 여기서는 타조 인스턴스가 로컬 파일 시스템에서 동작하는 로컬 모드만을 설명합니다. 로컬 모드의 타조 인스턴스는 아주 단순한 설정만으로도 동작 가능합니다.

우선, 환경 변수들을 conf/tajo-env.sh에 추가해야 합니다.

.. code-block:: bash

  # Hadoop home. Required
  export HADOOP_HOME= ...

  # The java implementation to use.  Required.
  export JAVA_HOME= ...

타조 마스터를 시작하기 위해, start-tajo.sh를 실행합니다.

.. code-block:: bash

  $ $TAJO_HOME/bin/start-tajo.sh

.. note::

  분산 모드 셋업에 대해 궁금하다면, :doc:`/configuration/cluster_setup`를 참조합니다.

.. warning::

  기본적으로, 테이블 메타 데이터를 관리하는 *카탈로그 서버*는 영구 정장소로 `Apache Derby <http://db.apache.org/derby/>`_를 사용한고, 더비(Derby)는 데이터를 ``/tmp/tajo-catalog-${username}`` 디렉토리에 저장합니다. 주의할 사항은, 일부 운영 체제에서는 재시작될 때 ``/tmp``에 내용을 모두 삭제하기 때문에, 안전한 카탈로그 데이터 저장소 확보를 위해서 별도의 더비(derby) 디렉토리를 지정하는 것이 좋습니다. 카탈로그 설정에 대한 정보가 필요하다면, :doc:`/configuration/catalog_configuration`를 참고합니다.

========================
첫 질의 실행
========================

질의 실행에 앞서 먼저 테이블을 생성해야 합니다. 예로 다음과 같이 텍스트 기반의 단순한 테이블을 하나 만듭니다: 

.. code-block:: bash

  $ mkdir /home/x/table1
  $ cd /home/x/table1
  $ cat > data.csv
  1|abc|1.1|a
  2|def|2.3|b
  3|ghi|3.4|c
  4|jkl|4.5|d
  5|mno|5.6|e
  <CTRL + D>


아파치 타조™는 사용자들이 SQL 질의들을 실행하고 바로 결과를 볼 수 있는 SQL 쉘을 제공합니다. 쉘을 사용하기 위해서는, 단순히 ``bin/tsql``을 실행합니다. ::

  $ $TAJO_HOME/bin/tsql
  tajo>

위에 생성한 테이블을 읽어들이기 위해, 테이블의 스키마를 정의해야 합니다. 
여기서는, (int, text, float, text)로 가정합니다. ::

  $ $TAJO_HOME/bin/tsql
  tajo> create external table table1 (
        id int,
        name text, 
        score float, 
        type text) 
        using csv with ('text.delimiter'='|') location 'file:/home/x/table1';

외부 테이블을 읽기 위해서, ‘create external table’ 선언문을 사용합니다. 
적절한 스키마와 함께, location 구문에 절대 경로 디렉토리를 지정합니다. 
테이블이 HDFS 상에 있다면, ‘file’ 대신 ‘hdfs’를 사용해야 합니다.

DDL 선언문들에 대해 좀 더 자세히 알고 싶다면, 쿼리 언어(Query Language)를 참조합니다. ::

  tajo> \d
  table1

``\d`` 명령은 테이블 목록을 보여 줍니다. ::

  tajo> \d table1

  table name: table1
  table path: file:/home/x/table1
  store type: CSV
  number of rows: 0
  volume (bytes): 78 B
  schema:
  id      INT
  name    TEXT
  score   FLOAT
  type    TEXT

``\d [table name]`` 명령은 해당 테이블에 대한 설명을 보여 줍니다.

또한, 다음과 같은 SQL 질의도 가능합니다: ::

  tajo> select * from table1 where id > 2;
  final state: QUERY_SUCCEEDED, init time: 0.069 sec, response time: 0.397 sec
  result: file:/tmp/tajo-hadoop/staging/q_1363768615503_0001_000001/RESULT, 3 rows ( 35B)

  id,  name,  score,  type
  - - - - - - - - - -  - - -
  3,  ghi,  3.4,  c
  4,  jkl,  4.5,  d
  5,  mno,  5.6,  e

  tajo> \q
  bye

이제, SQL 표준을 따르는 타조을 맘껏 즐겨보시죠. 
타조가 지원하는 SQL에 대한 좀 더 자세한 설명이 필요하다면, :doc:`/sql_language`를 참조합니다.

  
