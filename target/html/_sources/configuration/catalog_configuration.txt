******************************
카탈로그 설정
******************************

카탈로그 서비스 설정을 수정하려면, ``$TAJO_HOME/conf/catalog-site.xml.template`` 파일을 ``catalog-site.xml`` 로 복사하고 catalog-site.xml 파일 내용에 다음을 추가합니다. 대부분의 경우, 기본값 설정만으로도 충분히 타조 클러스터를 실행할 수 있다는 것을 기억해 주세요.

* tajo.catalog.master.addr - 분산 모드로 타조를 실행하려면, 이 주소를 반드시 명시해야합니다. 더 자세한 정보를 원하시면, [Default Ports](#DefaultPorts) 를 참고합니다.
* tajo.catalog.store.class - 카탈로그 서버의 영구 저장소를 변경하려면, 클래스 명을 명시합니다. 참고로, 기본값은 tajo.catalog.store.DerbyStore 입니다. 현재 버전에서는 타조는 아래 세가지 영구 저장소를 지원합니다:

+-----------------------------------+------------------------------------------------+
| 드라이버 클래스                   | 설명                                           |
+===================================+================================================+
| tajo.catalog.store.DerbyStore     | 아파치 더비(Derby)를 사용합니다.               |
+-----------------------------------+------------------------------------------------+
| tajo.catalog.store.MySQLStore     | MySQL을 사용합니다.                            |
+-----------------------------------+------------------------------------------------+
| tajo.catalog.store.MariaDBStore   | MariaDB를 사용합니다.                          |
+-----------------------------------+------------------------------------------------+
| tajo.catalog.store.MemStore       | 메모리 저장소를 사용합니다.                    | 
|                                   |                                                |
|                                   | 이 저장소는 유닛 테스트 시간을                 |
|                                   |                                                |
|                                   | 단축하기 위한 용도로만 사용됩니다.             |
+-----------------------------------+------------------------------------------------+
| tajo.catalog.store.HCatalogStore  | 저장소 클래스로 HiveMetaStore를 사용합니다.    |
+-----------------------------------+------------------------------------------------+

=========================
더비(Derby) 설정
=========================

타조는 기본 설정으로 `Apache Derby <http://db.apache.org/derby/>`_ 를 테이블 메타 데이터를 관리하기 위한 영구 저장소로 사용하기 때문에, 다른 설정이 없다면, 카탈로스 스토어로 더비(Derby)를 사용할 수 있습니다.

또는, ``conf/catalog-site.xml`` 에 다음과 같이 직접 설정할 수도 있습니다:

.. code-block:: xml

  <property>
    <name>tajo.catalog.store.class</name>
    <value>org.apache.tajo.catalog.store.DerbyStore</value>
  </property>

  <property>
    <name>tajo.catalog.uri</name>
    <value>jdbc:derby:<absolute directory>;create=true</value>
  </property>

더비는 파일 기반의 임베디드 데이터베이스이기 때문에 특정 디렉토리에 데이터를 저장합니다. 따라서, 호스트 명과 포트를 지정하는 일반적인 JDBC URI 형태 대신 데이터 파일을 저장하기 위한 디렉토리를 지정해야 합니다. 예를 들어, 더비 저장소 디렉토리로 '/var/data/tajo-catalog'를 사용한다면, 다음과 같이 설정합니다:

.. code-block:: xml
  
  <property>
    <name>tajo.catalog.uri</name>
    <value>jdbc:derby:/var/data/tajo-catalog;create=true</value>
  </property>

.. warning::

  기본적으로, *카탈로그 서버* 는 ``/tmp/tajo-catalog-${username}`` 디렉토리에 카탈로그 데이터를 저장합니다. 그러나, 일부 운영 체제에서는 재시작될 때 ``/tmp`` 에 내용을 모두 삭제하기 때문에, 안전한 카탈로그 데이터 저장소 확보를 위해서 적절한 더비(derby) 디렉토리를 지정해야 합니다.

=========================
MySQLStore 설정
=========================

MySQLStore를 사용하려면, MySQL에 타조용 데이터베이스와 사용자를 생성합니다.

.. code-block:: sh
  
  mysql> create user 'tajo'@'localhost' identified by 'xxxxxx';
  Query OK, 0 rows affected (0.00 sec)

  mysql> create database tajo;
  Query OK, 1 row affected (0.00 sec)  

  mysql> grant all on tajo.* to 'tajo'@'localhost';
  Query OK, 0 rows affected (0.01 sec)


그리고, 타조 마스터 실행 서버에 MySQL JDBC 드라이버가 필요한데, ``conf/tajo-env.sh`` 파일에 ``TAJO_CLASSPATH`` 변수 값을 다음과 같이 설정합니다:

.. code-block:: sh

  export TAJO_CLASSPATH=/usr/local/mysql/lib/mysql-connector-java-x.x.x.jar

또는 JDBC 드라이버를 ``$TAJO_HOME/lib`` 로 복사합니다.

마지막으로, 다음 설정들을 `conf/catalog-site.xml` 에 추가합니다:

.. code-block:: xml

  <property>
    <name>tajo.catalog.store.class</name>
    <value>org.apache.tajo.catalog.store.MySQLStore</value>
  </property>
  <property>
    <name>tajo.catalog.jdbc.connection.id</name>
    <value><mysql user name></value>
  </property>
  <property>
    <name>tajo.catalog.jdbc.connection.password</name>
    <value><mysql user password></value>
  </property>
  <property>
    <name>tajo.catalog.jdbc.uri</name>
    <value>jdbc:mysql://<mysql host name>:<mysql port>/<database name for tajo>?createDatabaseIfNotExist=true</value>
  </property>


===========================
MariaDBStore 설정
===========================

MariaDBStore 사용을 위한 모든 설정은 아래 내용을 제외하곤 MySQLStore 설정과 동일합니다:

.. code-block:: sh

  export TAJO_CLASSPATH=/usr/local/mariadb/lib/mariadb-java-client-x.x.x.jar

.. code-block:: xml

  <property>
    <name>tajo.catalog.store.class</name>
    <value>org.apache.tajo.catalog.store.MariaDBStore</value>
  </property>
  <property>
    <name>tajo.catalog.jdbc.uri</name>
    <value>jdbc:mariadb://<mariadb host name>:<mariadb port>/<database name for tajo>?createDatabaseIfNotExist=true</value>
  </property>


==================================
  HCatalogStore 설정
==================================

타조는 Hive 연동을 위해 HCatalogStore를 지원합니다. HCatalogStore를 사용하려면 아래 과정을 따릅니다.

먼저, 아래 과정을 통해 소스코드를 컴파일하고 바이너리 아카이브를 생성합니다:

.. code-block:: sh

  $ git clone https://git-wip-us.apache.org/repos/asf/tajo.git tajo
  $ mvn clean install -DskipTests -Pdist -Dtar -Phcatalog-0.1x.0
  $ ls tajo-dist/target/tajo-x.y.z-SNAPSHOT.tar.gz

현재 타조는 Hive 0.12.0, Hive 0.13.0, Hive 0.13.1을 지원합니다. HCatalogStore 사용을 활성화 하려면, 메이븐(Maven) 프로파일에 ``-Phcatalog-0.12.0`` 과 같이 설정합니다.

그 다음, ``conf/tajo-env.sh`` 파일 내용 중에, HIVE_HOME 변수에 다음과 같이 Hive 홈 디렉토리 경로를 지정합니다:

.. code-block:: sh

  export HIVE_HOME=/path/to/your/hive/directory

세번째 단계로, HiveMetaStore 접속을 위해 JDBC를 사용하는 경우, 타조 마스터 실행 서버에 MySQL JDBC 드라이버가 필요합니다. 이 경우, 다음과 같이 ``conf/tajo-env.sh`` 파일 내용 중 ``HIVE_JDBC_DRIVER_DIR`` 변수 값으로 JDBC 드라이버 파일 경로를 지정합니다:

.. code-block:: sh

  export HIVE_JDBC_DRIVER_DIR=/path/to/your/mysql_jdbc_driver/mysql-connector-java-x.x.x-bin.jar


마지막으로, ``conf/catalog-site.xml`` 파일 내용에 다음 설정을 추가합니다:

.. code-block:: xml

  <property>
    <name>tajo.catalog.store.class</name>
    <value>org.apache.tajo.catalog.store.HCatalogStore</value>
  </property>
