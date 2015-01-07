*************************************
아파치 타조 테이블 관리 개요
*************************************

개요
========

Managed Table
================

.. todo::

External Table
================

.. todo::

테이블 속성들
================
모든 테이블 포맷은 테이블의 물리적 속성 조절을 허용하거나 허용하지 않도록 설정할 수 있는 파라메터를 제공합니다.
CREATE TABLE 선언문 내의 ``WITH`` 절을 통해 사용자에게 여러 속성들을 설정하도록 허용할 수 있습니다.

아래의 예제는 필드 구분자, NULL 문자, 그리고 압축 코덱들을 설정합니다.

.. code-block:: sql

 CREATE TABLE table1 (
  id int,
  name text,
  score float,
  type text
 ) USING CSV WITH('text.delimiter'='\u0001',
                  'text.null'='\\N',
                  'compression.codec'='org.apache.hadoop.io.compress.SnappyCodec');

각 물리적 테이블 레이아웃은 그들만의 특별한 속성들을 가지고 있습니다. 이들은  :doc:`/table_management/file_formats` 에 정리되어 있습니다.

테이블의 공통 속성들
=======================

대부분의 테이블들에서 사용되는 공통 테이블 속성들에 대한 정보입니다.

압축
-----------
.. todo::

타임존
---------

타조에는 아래의 예제처럼 ``timezone`` 에 대한 테이블 속성을 통해 
데이터를 테이블로부터 읽거나 테이블에 쓸 경우에 특정 타임존을 지정할 수 있습니다. 

.. code-block:: sql

   CREATE EXTERNAL TABLE table1 (
    t_timestamp  TIMESTAMP,
    t_date    DATE
   ) USING TEXT WITH('timezone'='ASIA/Seoul') LOCATION '/path-to-table/'
 
타임존에 대한 자세한 정보는 :doc:`/time_zone` 을 참고해 주세요.
