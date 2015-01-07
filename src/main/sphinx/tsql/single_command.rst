*********************************
단일 명령 실행
*********************************


tsql 프롬프트 시작 없이 여러 질의를 실행하고자 할 때는, tsql에서 제공하는 ``-c`` 명령을 사용합니다. 이 경우, 타조는 다음과 같이 세미콜론으로 구분된 각 쿼리를 실행합니다:

.. code-block:: sql

  $ bin/tsql  -c "select count(*) from table1; select sum(score) from table1;"
  Progress: 0%, response time: 0.217 sec
  Progress: 0%, response time: 0.218 sec
  Progress: 100%, response time: 0.317 sec
  ?count
  -------------------------------
  5
  (1 rows, 0.317 sec, 2 B selected)
  Progress: 0%, response time: 0.202 sec
  Progress: 0%, response time: 0.204 sec
  Progress: 100%, response time: 0.345 sec
  ?sum
  -------------------------------
  15.0
  (1 rows, 0.345 sec, 5 B selected)
