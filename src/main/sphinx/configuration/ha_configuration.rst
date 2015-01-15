*********************************
타조 마스터 고 가용성
*********************************

타조 마스터는 타조 시스템의 전체 컴포넌트들을 중앙에서 통제하는 역할을 하기 때문에 타조 클러스터에는 단일장애점(a Single Point of Failure)이 됩니다. 타조 마스터에 장애가 발생하면, 타조 워커들은 자신들의 통계 정보를 CatalogStore에 적용할 수 없기 때문에, 클라이언트들이 클러스터에 새로운 질의를 수행할 수 없게되고, 결과적으로 insert overwrite 질의도 수행할 수 없게 됩니다. 따라서, 타조 마스터의 고 가용성(HA)은 타조 전반에 대한 가용성 측면에서 아주 중요하다고 할 수 있습니다.

현재 타조는 타조 마스터 고 가용성 측면에서 다음과 같은 요소들을 제공합니다:

* 타조 마스터의 자동 failover: 액티브 타조 마스터가 멈추면, 스텐바이 타조 마스터가 동작하게 됩니다.
* 클러스터에서 처리 중인 질의 수행: 액티브 타조 마스터가 멈추더라도, 클러스터는 처리중인 질의을 완료합니다.


================================================
  용어
================================================

* 액티브 마스터: 타조 클라이언트와 타조 워커로 부터의 모든 요청을 처리하는 활성화된 타조 마스터입니다.
* 백업 마스터: 액티브 마스터가 죽거나 동작할 수없는 상태가 되면 활성화되는 타조 마스터입니다. 사용자는 여러 백업 마스터를 설정할수 있고, 이 서버들은 액티브 마스터의 상태를 모니터링하고 있다가 필요시 활성화됩니다.


================================================
  설정 파일 구성
================================================

타조 마스터 고 가용성 모드를 사용하려면, 다음과 같이 ``tajo.master.ha.enable`` 속성 값을 설정합니다:

.. code-block:: xml

  <property>
    <name>tajo.master.ha.enable</name>
    <value>true</value>
  </property>

고 가용성 모드를 사용하면, 모든 백업 마스터들은 5초마다 액티브 마스터의 상태를 모니터링합니다. 이 주기를 변경하려면, ``tajo.master.ha.monitor.interval`` 속성 값을 적절히 설정합니다:

.. code-block:: xml

  <property>
    <name>tajo.master.ha.monitor.interval</name>
    <value>monitor interval</value>
  </property>


================================================
  백업 마스터 설정 구성
================================================

``start-tajo.sh`` 로 마스터를 실행하는 경우, ``conf/masters`` 에 마스터 목록을 명시합니다. 이 파일에 모든 마스터들의 호스트 명을 한 줄에 하나씩 저장해서 목록을 만듭니다. 최초에는, ``localhost`` 하나만 등록되어 있습니다. 즐겨 사용하는 텍스트 편집기를 사용해서 쉽게 호스트 명들을 추가할 수 있습니다.

예: ::

  $ cat > conf/masters
  host1.domain.com
  host2.domain.com
  ....

  <ctrl + d>

다음 단계는, 백업 마스터 서버들에 tarball을 이용해서 타조를 설치하고, 설정 파일을 구성합니다. .

.. note::

  동일한 호스트에 액티브 마스터와 백업 마스터를 같이 실행하는 경우, 타조 마스터 포트 충돌이 발생할 수 있습니다. 이를 피하기 위해서는, 다음과 같이 ``tajo-site.xml`` 에 백업 마스터의 포트를 다르게 변경해야 합니다:

  .. code-block:: xml

    <property>
      <name>tajo.master.umbilical-rpc.address</name>
      <value>localhost:36001</value>
      <description>The default port is 26001.</description>
    </property>

    <property>
      <name>tajo.master.client-rpc.address</name>
      <value>localhost:36002</value>
      <description>The default port is 26002.</description>
    </property>

    <property>
      <name>tajo.resource-tracker.rpc.address</name>
      <value>localhost:36003</value>
      <description>The default port is 26003.</description>
      </property>

    <property>
      <name>tajo.catalog.client-rpc.address</name>
      <value>localhost:36005</value>
      <description>The default port is 26005.</description>
    </property>

    <property>
      <name>tajo.master.info-http.address</name>
      <value>0.0.0.0:36080</value>
      <description>The default port is 26080.</description>
    </property>


  그리고 ``tajo-env.sh`` 내용 중 ``TAJO_PID_DIR`` 변수 값을 다른 디렉토리로 변경합니다.


================================================
  타조 클러스터 실행
================================================

그런 다음, ``start-tajo.sh`` 를 실행합니다::

  $ $TAJO_HOME/bin/start-tajo.sh

.. note::

  DerbyStore에 대해서는 고 가용성 모드가 적용되지 않습니다. 현재는, 하나의 타조 마스터만 derby를 호출합니다. 다른 마스터가 이를 호출하려고 하면, 동작하지 않도록 되어 있습니다. 또한, 백업 마스터를 위해 또 다른 카탈로그 URI를 설정하하는 경우도, 결국 다르기 때문에 잘못된 설정에 해당합니다.

================================================
  고 가용성(HA) 상태 관리
================================================

강제로 백업 마스터를 액티브 마스터로 활성화시키려면, ``tajo hadmin -transitionToActive`` 명령을 실행합니다 ::

  $ $TAJO_HOME/bin/tajo haadmin -transitionToActive <target tajo.master.umbilical-rpc.address>

강제로 액티브 마스터를 백업 마스터로 바꾸려면, ``tajo hadmin -transitionToBackup`` 명령을 실행합니다 ::

  $ $TAJO_HOME/bin/tajo haadmin -transitionToBackup <target tajo.master.umbilical-rpc.address>

마스터 상태 정보는, ``tajo hadmin -getState`` 명령을 실행합니다 ::

  $ $TAJO_HOME/bin/tajo haadmin -getState <target tajo.master.umbilical-rpc.address>

고 가용성 관련 정보를 초기화하려면, ``tajo haadmin -formatHA`` 명령을 실행합니다 ::

  $ $TAJO_HOME/bin/tajo haadmin -formatHA

.. note::

  고 가용성 정보를 초기화 하기 전에, 먼전 타조 클러스터를 중지해야 합니다.


================================================
  자동 failover 점검 방법
================================================

타조 마스터의 자동 failover 동작을 확인하려면, 먼저, 타조 클러스터를 배포할 때, 타조 마스터 고 가용성 모드를 활성화시킵니다. 그리고, 타조 웹 UI를 통해, 어느 노드가 액티브 마스터인지 확인합니다.

액티브 마스터 노드를 찾았다면, 이제 해당 노드에 장애 상황을 발생시킵니다. 예를 들면, JVM 동작 중단 상황을 강제로 만들기 위해 kill -9 <pid of TajoMaster> 명령을 실행합니다. 또는 서버 장비를 끄거나 네트워크 연결을 끊습니다. 그런 후, 백업 마스터가 5초 안에 액티브 상태로 활성화되는지 확인하면 됩니다. 장애 감지와 failover 작동관련 시간 주기는 ``tajo.master.ha.monitor.interval`` 변수의 설정 값을 따릅니다. 실행 중인 질의가 있었다면, 타조 클라이언트는 타조 워커로 부터 직접 결과를 얻기 때문에 마스터 상태와 상관없이 성공적으로 완료될 것입니다. 그러나, 원래 마스터의 메모리에 가지고 있었던 과거 질의 내역은 다른 마스터에서는 접근할 수 없기 때문에 잃게 됩니다. 이제 실행 중인 질의가 없다면, 자동 failover는 성공적으로 동작할 것입니다.

.. note::

  타조 마스터 고 가용성은 타조 워커 장애에 대한 부분까지 고려되어 있지는 않습니다. 고 가용성은 타조 리소스메니저와 쿼리 마스터 양쪽 모두에 대해서만 보장됩니다.