*************************************
HCatalog 통합
*************************************

아파치 Tajo 카탈로그는 아파치 Hive와 통합을 위해 HCatalogStore 드라이버를 지원합니다. 
이러한 통합기능은 Tajo가 아파치 Hive에 사용된 모든 테이블에 액세스 할 수 있습니다. 
사용자의 목적에 따라 아파치 Hive에서 관리되는 테이블에 대해  SQL 쿼리 나 HiveQL 쿼리를 실행할 수 있습니다.

이 기능을 사용하려면, 사용자는 메이븐 프로파일을 지정하여 Tajo를 빌드한 다음 conf/tajo-env.sh 파일과  conf/catalog-site.xml 파일에 일부 설정을 추가해야 합니다. 
이 섹션에서는 HCatalog의 통합 설치 방법에 대해 설명합니다. 이 과정을 수행하는데 10분이 넘지 않을 것입니다.

첫째, hcatalog 프로파일을 사용하여 소스 코드를 컴파일 해야합니다. 
현재 Tajo는 hcatalog-0.11.0 및 hcatalog-0.12.0 프로파일을 지원합니다. 
Hive 0.11.0를 사용하기 위해서는 ``-Phcatalog-0.11.0`` 처럼 메이븐 프로파일을 지정해야 합니다. ::

  $ mvn clean package -DskipTests -Pdist -Dtar -Phcatalog-0.11.0

또는 Hive 0.12.0를 사용하기 위해서는 ``-Phcatalog-0.12.0`` 처럼 maven 프로파일을 지정해야 합니다. ::

  $ mvn clean package -DskipTests -Pdist -Dtar -Phcatalog-0.12.0

다음으로 Hive 홈 디렉토리를 설정하기 위해 conf/tajo-env.sh 파일에 ``HIVE_HOME`` 환경변수를 다음처럼 설정해야 합니다.: ::

  export HIVE_HOME=/path/to/your/hive/directory

HiveMetaStore에 jdbc 연결이 필요하면 MySQL JDBC 드라이버를 준비해야 합니다.
다음 JDBC 드라이버 jar 파일 경로를 설정하기 위해 conf/tajo-env.sh 파일에 ``HIVE_JDBC_DRIVER_DIR`` 환경변수를 다음처럼 설정해야 합니다.: ::

  export HIVE_JDBC_DRIVER_DIR==/path/to/your/mysql_jdbc_driver/mysql-connector-java-x.x.x-bin.jar

마지막으로 HCatalogStore를 Tajo 카탈로그 드라이버 클래스로 ``conf/catalog-site.xml`` 파일에 다음처럼 등록해야 합니다.: ::

  <property>
    <name>tajo.catalog.store.class</name>
    <value>org.apache.tajo.catalog.store.HCatalogStore</value>
  </property>

.. note::

  Hive는 각각의 테이블에 대한 파티션 목록을 저장합니다. 만일 새로운 파티션이 HDFS에 직접 추가된다면 HiveMetastore는 사용자가 새로운 파티션이 
  추가될 때마다 ``ALTER TABLE table_name ADD PARTITION`` 또는 `MSCK  REPAIR TABLE  table_name`` 명령이 실행되지 않는 한
  파티션이 추가된것을 알수 있는 방법이 없습니다.
  
  그러나 현재 Tajo는 `ADD PARTITION`` 명령을 제공하지 않고 Hive 또한  ``MSCK REPAIR TABLE`` 명령에 대한 응답을 위한 api를 제공하지 않습니다.
  그래서 Tajo를 통해 테이블 파티션이 업데이트된 것을 검색 하기를 원하거나 파티션 테이블에 데이터를 insert 하기를 원한다면 Hive에서 다음과 같은 명령어를 실행해야 합니다.::
  
  예)$ MSCK REPAIR TABLE [table_name];
