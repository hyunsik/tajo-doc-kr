***************************
카탈로그 백업 및 복원
***************************

현재 타조는 다음 두가지에 대한 백업을 지원합니다. 

* SQL 덤프
* 데이터베이스 수준의 백업 

==========
SQL 덤프 
==========

SQL 덤프는 쉬우면서도 강력한 기능으로, 데이터베이스 수준의 호환성을 걱정하지 않아도 됩니다. 카탈로그를 백업하려면, 간단히 bin/tajo-dump 명령을 실행하면 되며, 기본 사용법은 다음과 같습니다: ::

  $ tajo-dump 테이블명 > 출력파일

예를 들어, customer 테이블을 백업하려면, 다음과 같이 명령을 입력합니다: ::

  $ bin/tajo-dump customer > table_backup.sql
  $
  $ cat table_backup.sql
  -- Tajo database dump
  -- Dump date: 10/04/2013 16:28:03
  --

  --
  -- Name: customer; Type: TABLE; Storage: CSV
  -- Path: file:/home/hyunsik/tpch/customer
  --
  CREATE EXTERNAL TABLE customer (c_custkey INT8, c_name TEXT, c_address TEXT, c_nationkey INT8, c_phone TEXT, c_acctbal FLOAT8, c_mktsegment TEXT, c_comment TEXT) USING CSV LOCATION 'file:/home/hyunsik/tpch/customer';
  

SQL 덤프 파일로부터 카탈로그를 복원하려면, 다음 명령을 사용합니다: ::

  $ bin/tsql -f table_backup.sql


옵션 '-a'를 사용하면, 모든 테이블들의 DDL을 덤프할 수 있습니다. ::

  $ bin/tajo-dump -a > all_backup.sql

============================
데이터베이스 수준의 백업
============================

.. todo::

