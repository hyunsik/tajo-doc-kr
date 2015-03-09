*******************************************
클러스터 구성
*******************************************

완전 분산 모드
==========================================
완전 분산 모드는 타조 인스턴스를 `Hadoop Distributed File System (HDFS) <http://wiki.apache.org/hadoop/HDFS>`_ 위에서 동작하도록 해줍니다. 이 모드에서는, HDFS 데이터 노드들이 돌아가고 있는 여러 물리 노드들 위에서 여러 타조 워커(worker)들이 같이 동작하게 됩니다.


이 절에서는, 해당 모드로 클러스터를 구성하는 방법에 대해 설명합니다. 


설정
--------------------------------------------------------

tajo-site.xml 파일에 다음과 같이 설정합니다:

.. code-block:: xml

  <property>
    <name>tajo.rootdir</name>
    <value>hdfs://hostname:port/tajo</value>
  </property>

  <property>
    <name>tajo.master.umbilical-rpc.address</name>
    <value>hostname:26001</value>
  </property>

  <property>
    <name>tajo.master.client-rpc.address</name>
    <value>hostname:26002</value>
  </property>

  <property>
    <name>tajo.resource-tracker.rpc.address</name>
    <value>hostname:26003</value>
  </property>

  <property>
    <name>tajo.catalog.client-rpc.address</name>
    <value>hostname:26005</value>
  </property>

워커들(Workers)
--------------------------------------------------------

``conf/workers`` 파일에 모든 워커들의 호스트 명을 한 줄에 하나씩 명시합니다.
기본 값으로, 이 파일 내용에는 ``localhost`` 하나만 들어가 있습니다.
평소에 잘 쓰는 아무 텍스트 편집기를 이용해서, 쉽게 워커의 호스트명을 추가할 수 있습니다.

예: ::

  $ cat > conf/workers
  host1.domain.com
  host2.domain.com
  ....

  <ctrl + d>

HDFS에 기본 디렉토리 생성 및 접근 권한 설정
--------------------------------------------------------

타조 설정에 대해 좀 더 자세한 내용을 원한다면, 설정 페이지를 참조합니다.
타조 실행에 앞서, 다음과 같이 HDFS에 타조 root 디렉토리 생성과 접근 권한을 설정합니다: ::

  $ $HADOOP_HOME/bin/hadoop fs -mkdir       /tajo
  $ $HADOOP_HOME/bin/hadoop fs -chmod g+w   /tajo


타조 클러스터 실행
--------------------------------------------------------

그리고, ``start-tajo.sh`` 를 실행합니다::

  $ $TAJO_HOME/bin/start-tajo.sh

.. note::

  기본적으로, 각 워커에는 아주 적은 량의 자원이 할당되어 있습니다. 병렬 작업 수을 늘리는 방법이 궁금하다면, 
  :doc:`/configuration/worker_configuration` 를 참고합니다.

.. note::

  기본적으로, 타조 마스터는 127.0.0.1 주소에 대해 클라이언트 접속을 허용하도록 되어 있습니다. 타조 마스터로 원격에서 클라이언트 접속을 허용하도록 설정하려면, tajo-site.xml 파일 내용 중에 tajo.master.client-rpc.address 에 설정 값을 명시합니다. 접속 포트를 변경하는 방법은, :doc:`/configuration/service_config_defaults` 를 참고합니다.

