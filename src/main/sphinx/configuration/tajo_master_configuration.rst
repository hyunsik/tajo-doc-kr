**************************
Tajo Master Configuration
**************************

================================================
  타조 루트 디렉토리 (Rootdir)
================================================

타조는 HDFS를 주 저장소 계층으로 사용하기 때문에, 타조 클러스터는 하나의 타조 루트 디렉토리(rootdir)를 가집니다. 다음과 같이 루트 디렉토리를 지정합니다:

.. code-block:: xml

  <property>
    <name>tajo.rootdir</name>
    <value>hdfs://namenode_hostname:port/path</value>
  </property>

타조 루트 디렉토리는 반드시 ``scheme://hostname:port/path`` 와 같은 형식으로 지정합니다. 현재 지원되는 형식은 ``hdfs://`` 와 ``file://`` 입니다. 기본 값은 ``file:///tmp/tajo-${user.name}/`` 입니다.

================================================
타조 마스터 힙 메모리 크기
================================================

conf/tajo-env.sh 파일 내용 중에 TAJO_MASTER_HEAPSIZE 환경 변수에 타조 마스터의 힙 메모리 크기를 지정할 수 있습니다.

힙 메모리 크기를 변경하려면, 다음과 같이 ``conf/tajo-env.sh`` 파일 내용 중 ``TAJO_MASTER_HEAPSIZE`` 변수에 적절한 값을 지정합니다:

.. code-block:: sh

  TAJO_MASTER_HEAPSIZE=2000

기본 값은 1000 (1GB) 입니다. 