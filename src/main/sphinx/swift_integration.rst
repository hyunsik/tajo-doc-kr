*************************************
OpenStack Swift 통합
*************************************

Tajo는 OpenStack Swift를 기본 저장소 형식 중 하나로 지원합니다.
Tajo에서, Swift 객체는 Hadoop과 동일한 URI 형식으로 구분되고 표현됩니다.

Swift에서 Tajo를 실행하기 위해 Hadoop을 실행할 필요는 없지만, 이것을 설정할 필요는 있습니다.
Swift와 Tajo도 설정할 필요가 있을겁니다.

자세한 사항은 아래 섹션을 참고해 주십시오.

======================
Swift 설정
======================


이 단계는 의무는 아닙니다만, 더 나은 성능을 위해서 ``list_endpoints`` 를 통해 Swift의 프록시 서버를 설정할 것을 강력히 추천합니다.
자세한 정보는 여기를 참고하십시오 `here <http://docs.openstack.org/developer/swift/middleware.html#module-swift.common.middleware.list_endpoints>`_.

======================
Hadoop 설정
======================

Swift 객체에 접근하는 방법을 특정하기 위한 Hadoop 설정이 필요합니다.
아래는 ``${HADOOP_HOME}/etc/hadoop/core-site.xml`` 의 예 입니다.

-----------------------
일반 설정
-----------------------

.. code-block:: xml

  <property>
    <name>fs.swift.impl</name>
    <value>org.apache.hadoop.fs.swift.snative.SwiftNativeFileSystem</value>
    <description>File system implementation for Swift</description>
  </property>
  <property>
    <name>fs.swift.blocksize</name>
    <value>131072</value>
    <description>Split size in KB</description>
  </property>

----------------------------
제공자 별 설정
----------------------------

.. code-block:: xml

  <property>
    <name>fs.swift.service.${PROVIDER}.auth.url</name>
    <value>http://127.0.0.1/v2.0/tokens</value>
    <description>Keystone authenticaiton URL</description>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.auth.endpoint.prefix</name>
    <value>/endpoints/AUTH_</value>
    <description>Keystone endpoints prefix</description>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.http.port</name>
    <value>8080</value>
    <description>HTTP port</description>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.region</name>
    <value>regionOne</value>
    <description>Region name</description>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.tenant</name>
    <value>demo</value>
    <description>Tenant name</description>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.username</name>
    <value>tajo</value>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.password</name>
    <value>tajo_password</value>
  </property>
  <property>
    <name>fs.swift.service.${PROVIDER}.location-aware</name>
    <value>true</value>
    <description>Flag to enable the location-aware computing</description>
  </property>

======================
Tajo 설정
======================

아래 서술된 사항을 ``${TAJO_HOME}/conf/tajo-evn.sh`` 에 작성하여 Tajo의 클래스패스를 설정해야 합니다.

.. code-block:: sh

  export TAJO_CLASSPATH=$HADOOP_HOME/share/hadoop/tools/lib/hadoop-openstack-x.x.x.jar

======================
Swift에서의 질의
======================

제공자 이름을 *tajo*, Swift 컨테이너 이름을 *demo* 라 가정했을 때, 아래와 같은 방법으로 Swift 상의 데이터를 이용해 Tajo 테이블을 생성할 수 있습니다.

.. code-block:: sql

  default> create external table swift_table (id int32, name text, score float, type text) using text with ('text.delimiter'='|') location 'swift://demo.tajo/test.tbl';

한 번 테이블이 생성되면, HDFS에 저장되어 있는 다른 테이블처럼 어떠한 SQL 질의도 실행할 수 있습니다.
질의 실행에 대한 자세한 사항은 다음을 참고하십시오. :doc:`sql_language`