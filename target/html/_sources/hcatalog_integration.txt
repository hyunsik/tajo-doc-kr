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
Hive 0.11.0를 사용하기 위해서는 ``-Phcatalog-0.11.0`` 처럼 maven 프로파일을 지정해야 합니다. ::

  $ mvn clean package -DskipTests -Pdist -Dtar -Phcatalog-0.11.0

또는 Hive 0.12.0를 사용하기 위해서는 ``-Phcatalog-0.12.0`` 처럼 maven 프로파일을 지정해야 합니다. ::

  $ mvn clean package -DskipTests -Pdist -Dtar -Phcatalog-0.12.0

다음으로 Hive 홈 디렉토리를 설정하기 위해 conf/tajo-env.sh 파일에 ``HIVE_HOME`` 환경변수를 다음처럼 설정해야 합니다.: ::

  export HIVE_HOME=/path/to/your/hive/directory

연동하려는 Hive 메타스토어가 MySQL을 활용한 JDBC 기반으로 동작한다면 MySQL JDBC 드라이버를 준비하고 Tajo에도 해당 JDBC driver를 인식할 수 있도록 설정해야 합니다
HiveMetaStore에 jdbc 연결이 필요하면 conf/tajo-env.sh 파일에 ``HIVE_JDBC_DRIVER_DIR`` 환경변수를 다음처럼 설정해야 합니다.: ::

  export HIVE_JDBC_DRIVER_DIR==/path/to/your/mysql_jdbc_driver/mysql-connector-java-x.x.x-bin.jar

마지막으로 HCatalogStore를 Tajo 카탈로그 드라이버 클래스로 ``conf/catalog-site.xml`` 파일에 다음처럼 등록해야 합니다.: ::

  <property>
    <name>tajo.catalog.store.class</name>
    <value>org.apache.tajo.catalog.store.HCatalogStore</value>
  </property>

.. note::

  Hive는 각각의 테이블에 대한 파티션 목록을 메타스토어에 유지합니다. Hive에서 새로운 파티션이 추가 될 때 Hive가 인식할 수 있도록 사용자는  ``ALTER TABLE table_name ADD PARTITION``  명령 이용해 추가된 파티션 정보를 입력하거나  ``MSCK REPAIR TABLE table_name``  명령을 이용해서 전체 파티션 목록을 갱신해야 합니다.

  그러나 현재 Tajo에서는 위와 같은 기능을 지원하지 않습니다. 따라서 Tajo를 통해 추가된 테이블 파티션을 Hive 메타스토어에 반영하기 위해서는 다음과 같은 명령을 실행해야 합니다. ::

  $ MSCK REPAIR TABLE [table_name];
