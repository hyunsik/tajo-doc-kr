******************
타임존(Time Zone)
******************

타임존(time zone)은 ``Timestamp`` 또는 ``Time`` 과 같은 데이터 타입이나 일부 날짜 시간 관련된 연산자에 영향을 미칩니다. 또한 각 테이블은 사용할 데이터에 대해 기본 타임존을 부여할 수 있습니다. 이러한 다수의 타임존에 영향을 받는 테이블과 연산들에 대해 보다 효율적이고 일관된 처리를 위해 
Tajo는 내부적으로 모든 날짜 시간 관련된 내용은 UTC (Coordinated Universal Time)로 유지하게 됩니다.

Tajo에서는 타임존을 제어할 수 있는 있는 여러 가지 방법이 제공됩니다. 이제부터 그 내용을 설명합니다.

==================================================
서버 클러스터 타임존 (Server Cluster Time Zone)
==================================================

각 Tajo 클러스터는 하나의 시스템 타임존을 공유합니다. 이 타임존 설정은 클러스터를 구성하는 모든 노드에서 공유됩니다. 이 설정은 명시적으로 타임존을 설정하지 않은 테이블에 모두 자동으로 적용됩니다.

이 설정은 *conf/tajo-site.xml* 에서 아래와 같이 할 수 있습니다.

**tajo-site.xml**

.. code-block:: xml  

  <name>tajo.timezone</name>
  <property>GMT+9</property>


================================
테이블 타임존 (Table Time Zone)
================================

각 테이블은 ``timezone`` 속성을 가질 수 있습니다. 이 속성을 이용해 사용자는 각 테이블에 물리적으로 저장될 또는 이미 저장되어 있는 데이터가 특정 타임존을 따르도록 명시적이고 수동적으로 설정할 수 있습니다. 예를 들어 '2014-09-07 18:01:00' 이라는 ``timestamp`` 값은 실제로 어떤 타임존을 따르냐에 UTC 기준으로한 절대 시간은 상이하게 다를 수 있습니다.

다음과 같이 테이블의 타임존을 명시적으로 지정할 수 있습니다. 설정 값으로 ``ASIA/Seoul`` 이 사용되었는데요. 이 값으로 어떤 것이 쓰일 수 있는지는 아래에서 `Time Zone ID`_ 섹션에서 다시 설명합니다.

.. code-block:: sql

   CREATE EXTERNAL TABLE table1 (
    t_timestamp  TIMESTAMP,
    t_date    DATE
   ) USING TEXT WITH('timezone'='ASIA/Seoul') LOCATION '/path-to-table/'
 

그외 테이블 속성에 대해 배우고 싶으신 분은 :doc:`/table_management/table_overview` 를 참고하세요.

====================================
클라이언트 타임존 (Client Time Zone)
====================================

각 클라이언트는 각 세션 연결 별로 개별적인 타임존 설정을 가질 수 있으며 클라이언트 타임존은 최종적으로 결과를 사용할 측의 타임존에 맞게 날짜/시간 데이터를 변환하게 됩니다. 클라이언트 타임존을 설정하기 위해서는 세션 변수인 ``TIMEZONE`` 을 설정하면 됩니다. 설정은 두 가지 방법이 있습니다. 

``tsql`` 쉘을 사용할 경우 메타 코멘드인 ``\set timezone`` 을 아래와 같이 사용합니다.

**tsql**

.. code-block:: sh

  default> \set timezone GMT+9


또한 SQL 구문을 이용한 방법이 있습니다. 이 방법은 JDBC 드라이버를 사용하거나 ``tsql`` 에서 모두 활용 가능합니다.

**SQL**

.. code-block:: sql

  SET TIME ZONE 'GMT+9';

  or

  SET SESSION TIMEZONE TO 'GMT+9';

============
Time Zone ID
============

타임존 ID는 여러 가지 형태로 존재할 수 있습니다. 짧게 줄여써서 'PST', 'DST', 또는 'KST' 와 같이 쓸 수 있습니다. 또한 GMT나 UTC 기준의 오프셋(offset)을 이용해 'GMT+9'나 'UTC+9'와 같이 쓸 수 도 있습니다. 마지막으로 지역에 기반한 형태로 'Asia/Seoul' 과 같이 쓸 수 있습니다. Tajo 0.9.0 이후 버전 부터는 위 세가지 형태의 타임존을 모두 지원합니다.

.. note::

  기본적으로 Tajo 팀은 오프셋 기반 표현이나 위치 기반 형태를 쓰는 것을 권고합니다. 타임존 ID 리스트는 
  `List of tz database time zones <http://en.wikipedia.org/wiki/List_of_tz_database_time_zones>`_ 에 나열되어있으니 참고하세요.

.. note::

  자바 6은 소수의 위치 기반 표현만 지원하며 UTC를 이용한 오프셋 표현을 지원하지 않습ㄴ다. 그래서 자바 6 사용자에게는 GMT 키워드를 이용한 오프셋 표현을 강력하게 권고합니다. 즉 자바 6 사용자는 'UTC-7' 대신에 'GMT-7'를 반드시 사용하셔야 합니다.  

=====================
타임존 사용 예제
=====================

예제를 설명하기 위해 아래 입력데이터가 ``Asia/Seoul`` 즉 GMT+9 로 쓰여진 데이터라고 가정합니다.

.. code-block:: text

  1980-4-1 01:50:30.010|1980-04-01
  80/4/1 1:50:30 AM|80/4/1
  1980 April 1 1:50:30|1980-04-01


위 원본 데이터가 ''Asia/Seoul'' 타임존을 기반하여 쓰여진 테이블이기 때문에 생성할 때 ``timezone`` 테이블 속성(table property)을 이용하여 아래와 같이 타임존을 설정합니다.

.. code-block:: sql

 CREATE EXTERNAL TABLE table1 (
  t_timestamp  TIMESTAMP,
  t_date    DATE
 ) USING TEXT WITH('text.delimiter'='|', 'timezone'='ASIA/Seoul') LOCATION '/path-to-table/'

기본적으로 tsql이나 TajoClient는 사용자 JVM의 타임존을 따릅니다. 만약 시스템이 'GMT' 타임존을 사용한다면 아래와 같이 원래 시간에서 -9 시간을 뺀 시간으로 아래와 같이 표현됩니다. 여기서 첫번째 컬럼인 타임스탬프 타입 값은 변환이 되지만 두번째 컬럼은 날짜 데이터는 변환되지 않는 것을  주의깊게 보셔야 합니다. 날짜 데이터는 타임존의 영향을 받지 않습니다.

.. code-block:: sql

  default> SELECT * FROM table1
  t_timestamp,            t_date
  ----------------------------------
  1980-03-31 16:50:30.01, 1980-04-01
  1980-03-31 16:50:30   , 1980-04-01
  1980-03-31 16:50:30   , 1980-04-01

사용자가 서울 시간 기준으로 타임스탬프 데이터를 보고 싶다면 세션 변수인 'TIMEZONE'을 아래와 같이 설정하면 됩니다. 그러면 서울 시간 기준으로 사용자에게 출력이 됩니다.

.. code-block:: sql

  default> SET TIME ZONE 'Asia/Seoul'
  default> SELECT * FROM table1
  t_timestamp,            t_date
  ----------------------------------
  1980-04-01 01:50:30.01, 1980-04-01
  1980-04-01 01:50:30   , 1980-04-01
  1980-04-01 01:50:30   , 1980-04-01
