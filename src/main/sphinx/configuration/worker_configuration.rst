*********************
워커(Worker) 설정
*********************

========================
워커 힙 메모리 크기
========================

``conf/tajo-env.sh`` 파일 내용 중에 ``TAJO_WORKER_HEAPSIZE`` 환경 변수 값에 타조 워커가 사용할 힙 메모리 크기를 지정합니다.

힙 메모리 크기를 변경하려면, ``conf/tajo-env.sh`` 파일에 ``TAJO_WORKER_HEAPSIZE`` 변수 값을 적절한 값으로 지정합니다:

.. code-block:: bash

  TAJO_WORKER_HEAPSIZE=8000

기본 값은 1000 (1GB) 입니다.

========================
임시 데이터 디렉토리
========================

out-of-core 알고리즘에 따라 타조 워커는 임시 데이터를 로컬 파일 시스템에 저장합니다. 임시 데이터 저장소로 하나 이상의 데이터 디렉토리를 지정할 수 있습니다.

``tajo-site.xml``

.. code-block:: xml

  <property>
    <name>tajo.worker.tmpdir.locations</name>
    <value>/disk1/tmpdir,/disk2/tmpdir,/disk3/tmpdir</value>
  </property>
  

==========================================================
각 워커가 수행할 수 있는 최대 병렬 작업 수
==========================================================

타조는 가용 자원과 처리 중인 질의의 작업량에 따라 병렬로 실행할 작업 수를 결정합니다. 이 값을 명시하려면,  [워커 자원(Worker Resources)] (#ResourceConfiguration) 절을 참조합니다.

==========================================================
워커 자원
==========================================================

하나의 워커는 여러 작업들을 동시에 수행할 수 있습니다.
타조는, 사용자가 각 워커가 사용할 총 메모리 크기와 디스크 갯수를 지정할 수 있습니다. 가용한 자원에 따라 동시에 수행할 수 있는 작업의 수가 결정됩니다.

각 워커의 리소스 량을 지정하려면, ``tajo-site.xml`` 파일에 다음 값을을 설정합니다:

=================================  ==========================  ===================   =========================
  속성 명                     설명                값 타입            기본 값            
=================================  ==========================  ===================   =========================
  tajo.worker.resource.cpu-cores    CPU 코어 수    integer               1                        
  tajo.worker.resource.memory-mb    메모리 크기 (MB)           integer               1024                     
  tajo.worker.resource.disks        디스크 수        integer               1                        
=================================  ==========================  ===================   =========================

.. note:: 
  
  Currently, QueryMaster requests 512MB memory and 0.5 disk per task for the backward compatibility.

.. note::

  If ``tajo.worker.resource.dfs-dir-aware`` is set to ``true`` in ``tajo-site.xml``, the worker will aware of and use the number of HDFS datanode's data dirs in the node.
  In other words, ``tajo.worker.resource.disks`` is ignored.

------------
 Example
------------

Assume that you want to give 5120 MB memory, 4 disks, and 24 cores on each worker. The example configuration is as follows:

``tajo-site.xml``

.. code-block:: xml

  <property>
    <name>tajo.worker.resource.tajo.worker.resource.cpu-cores</name>
    <value>24</value>
  </property>
  
   <property>
    <name>tajo.worker.resource.memory-mb</name>
    <value>5120</value>
  </property>
  
  <property>
    <name>tajo.worker.resource.tajo.worker.resource.disks</name>
    <value>4.0</value>
  </property>  

--------------------
 Dedicated Mode
--------------------
Tajo provides a dedicated mode that allows each worker in a Tajo cluster to use whole available system resources including cpu-cores, memory, and disks. For this mode, a user should add the following config to ``tajo-site.xml`` : 

.. code-block:: xml

  <property>
    <name>tajo.worker.resource.dedicated</name>
    <value>true</value>
  </property>

In addition, it can limit the memory capacity used for Tajo worker as follows:

===============================================  ================================================   ===================   =======================
  property name                                  description                                        value type            default value           
===============================================  ================================================   ===================   =======================
  tajo.worker.resource.dedicated-memory-ratio    how much memory to be used in whole memory         float                 0.8                     
===============================================  ================================================   ===================   =======================