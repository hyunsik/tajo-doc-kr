*********************************
어드민 명령들
*********************************


==========
요약
==========

tsql 는 다음과 같은 어드민 명령을 제공합니다:

.. code-block:: sql

  default> \admin;
  usage: admin [options]
   -cluster          Show Cluster Info
   -desc             Show Query Description
   -h,--host <arg>   Tajo server host
   -kill <arg>       Kill a running query
   -list             Show Tajo query list
   -p,--port <arg>   Tajo server port
   -showmasters      gets list of tajomasters in the cluster


-----------------------------------------------
기본 사용법
-----------------------------------------------

``-list`` 옵션은 다음과 같이 실행 중인 모든 질의 목록을 보여줍니다: ::

  default> \admin -list
  QueryId              State               StartTime           Query
  -------------------- ------------------- ------------------- -----------------------------
  q_1411357607375_0006 QUERY_RUNNING       2014-09-23 07:19:40 select count(*) from lineitem


``-desc`` 옵션은 다음과 같이 실행 중인 해당 질의에 대한 세부 설명을 보여줍니다: ::

  default> \admin -desc q_1411357607375_0006
  Id: 1
  Query Id: q_1411357607375_0006
  Started Time: 2014-09-23 07:19:40
  Query State: QUERY_RUNNING
  Execution Time: 20.0 sec
  Query Progress: 0.249
  Query Statement:
  select count(*) from lineitem


``-kill`` 옵션은 다음과 같이 지정된 실행 중인 질의를 중지합니다: ::

  default> \admin -kill q_1411357607375_0007
  q_1411357607375_0007 is killed successfully.



``-showmasters`` 옵션은 다음과 같이 모든 타조 마스터 목록을 보여줍니다: ::

  default> \admin -showmasters
  grtajo01
