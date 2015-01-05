***********************************************
데이터 삽입: INSERT (OVERWRITE) INTO
***********************************************

INSERT OVERWRITE 구문은 주어진 경로의 데이터 혹은 이미 존재하는 테이블에 지정한 데이터를 삽입합니다. 타조의 INSERT OVERWRITE 구문은 표준 SQL의 ``INSERT INTO SELECT`` 구문을 따릅니다. 아래에 데이터 삽입 구문에 대한 다양한 예제가 있습니다.

.. code-block:: sql

  create table t1 (col1 int8, col2 int4, col3 float8);

  -- 입력 테이블 (lineitem) 과 출력 테이블 (t1) 의 스키마가 같을 경우
  INSERT OVERWRITE INTO t1 SELECT l_orderkey, l_partkey, l_quantity FROM lineitem;
  -- 혹은 이와 같이 사용할 수 도 있습니다.
  INSERT OVERWRITE INTO t1 SELECT * FROM lineitem;

  -- 출력 테이블 (t1) 의 스키마가 전체 스키마의 일부분일 경우
  INSERT OVERWRITE INTO t1 SELECT l_orderkey FROM lineitem;

  -- 특정한 컬럼들을 지정해 출력 테이블 (t1) 에 삽입할 수도 있습니다.
  INSERT OVERWRITE INTO t1 (col1, col3) SELECT l_orderkey, l_quantity FROM lineitem;

또한, INSERT OVERWRITE 구문은 테이블 뿐만 아니라 특정한 경로의 데이터에 대해 덮어쓰기의 형태로 데이터를 삽입할 수도 있습니다.

.. code-block:: sql

  INSERT OVERWRITE INTO LOCATION '/dir/subdir' SELECT l_orderkey, l_quantity FROM lineitem;
