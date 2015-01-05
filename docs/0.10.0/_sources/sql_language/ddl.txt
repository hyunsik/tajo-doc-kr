****************************************************
데이터 정의 언어 (DDL, Data Definition Language)
****************************************************

=======================================
CREATE DATABASE (데이터베이스 생성)
=======================================

*개요*

.. code-block:: sql

  CREATE DATABASE [IF NOT EXISTS] <database_name> 

``IF NOT EXISTS`` 구문을 사용하실 경우 ``CREATE DATABASE`` 구문을 사용해 만들고자 하는 데이터베이스가 이미 존재할 경우 발생할 수 있는 오류를 피할 수 있습니다.

===================================
DROP DATABASE (데이터베이스 삭제)
===================================

*개요*

.. code-block:: sql

  DROP DATABASE [IF EXISTS] <database_name>

``IF EXISTS`` 구문을 사용하실 경우 ``DROP DATABASE`` 구문을 사용해 데이터베이스를 삭제하고자 할 때, 해당 데이터베이스가 존재 하지 않을 경우 발생할 수 있는 오류를 피할 수 있습니다.

==================================
CREATE TABLE (테이블 생성)
==================================

*개요*

.. code-block:: sql

  CREATE TABLE [IF NOT EXISTS] <table_name> [(<column_name> <data_type>, ... )]
  [using <storage_type> [with (<key> = <value>, ...)]] [AS <select_statement>]

  CREATE EXTERNAL TABLE [IF NOT EXISTS] <table_name> (<column_name> <data_type>, ... )
  using <storage_type> [with (<key> = <value>, ...)] LOCATION '<path>'

``IF NOT EXISTS`` 구문을 사용하실 경우 ``CREATE [EXTERNAL] TABLE`` 구문을 사용하여 테이블을 만들때 만들고자 하는 테이블이 현재 데이터베이스에 이미 존재할 경우 발생할 수 있는 오류를 피할 수 있습니다.

------------------------
 테이블 압축
------------------------

압축데이터를 포함한 외부 테이블을 추가하고 싶을 경우, CREATE TABLE 구문에 ''compression.code'' 파라메터를 추가하시면 됩니다.

.. code-block:: sql

  create EXTERNAL table lineitem (
  L_ORDERKEY bigint, 
  L_PARTKEY bigint, 
  ...
  L_COMMENT text) 

  USING csv WITH ('text.delimiter'='|','compression.codec'='org.apache.hadoop.io.compress.DeflateCodec')
  LOCATION 'hdfs://localhost:9010/tajo/warehouse/lineitem_100_snappy';

`compression.codec` 파라메터로 아래의 여러가지 압축 코덱들 중 하나를 선택하시면 됩니다.
  * org.apache.hadoop.io.compress.BZip2Codec
  * org.apache.hadoop.io.compress.DeflateCodec
  * org.apache.hadoop.io.compress.GzipCodec
  * org.apache.hadoop.io.compress.SnappyCodec 

==================================
 DROP TABLE (테이블 삭제)
==================================

*개요*

.. code-block:: sql

  DROP TABLE [IF EXISTS] <table_name> [PURGE]

``IF EXISTS`` 구문을 사용하실 경우 ``DROP DATABASE`` 구문을 사용해 테이블을 제거할 때 현재 데이터베이스에 해당 테이블이 존재하지 않을 경우 발생 할 수 있는 오류를 피할 수 있습니다. ``DROP TABLE`` 구문은 실제 데이터는 삭제하지 않지만 타조 카탈로그에서 해당 테이블 정보를 삭제합니다. ``PURGE`` 옵션을 사용하신다면, ``DROP TABLE`` 타조 카탈로그의 해당 테이블 정보뿐만 아니라 실제 데이터까지 모두 삭제하게 됩니다.
