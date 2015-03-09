*********************************
파일에 저장된 질의 실행
*********************************


-----------------------------------------------
기본 사용법
-----------------------------------------------


``-f`` 명령은 다음과 같이 한 파일에 저장된 여러 쿼리들을 tsql이 수행하도록 합니다:

.. code-block:: sql

  $ cat aggregation.sql
  select count(*) from table1;
  select sum(score) from table1;

  $ bin/tsql -f aggregation.sql
  Progress: 0%, response time: 0.216 sec
  Progress: 0%, response time: 0.217 sec
  Progress: 100%, response time: 0.331 sec
  ?count
  -------------------------------
  5
  (1 rows, 0.331 sec, 2 B selected)
  Progress: 0%, response time: 0.203 sec
  Progress: 0%, response time: 0.204 sec
  Progress: 50%, response time: 0.406 sec
  Progress: 100%, response time: 0.769 sec
  ?sum
  -------------------------------
  15.0
  (1 rows, 0.769 sec, 5 B selected)



-----------------------------------------------
SQL 파일에 파라미터 값 지정하기
-----------------------------------------------

SQL 파일에 파라미터 값을 지정하려면, -param 키=값 옵션을 사용합니다. 이 기능을 사용할 때는 다음과 같이 파일에 파라미터을 정의합니다:

.. code-block:: sql

  ${파라미터 명}


다음과 같이 $ 기호로 시작하는 중괄호 안에 파라미터 명을 넣어서 정의합니다:

.. code-block:: sql

  $ cat aggregation.sql
  select count(*) from table1 where id = ${p_id};

  $ bin/tsql -param p_id=1 -f aggregation.sql
  Progress: 0%, response time: 0.216 sec
  Progress: 0%, response time: 0.217 sec
  Progress: 100%, response time: 0.331 sec
  ?count
  -------------------------------
  1
  (1 rows, 0.331 sec, 2 B selected)
