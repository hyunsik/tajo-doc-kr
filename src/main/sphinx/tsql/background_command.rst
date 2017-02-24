*********************************
백그라운드 프로세스로 실행
*********************************


일반적인 방법으로 tsql을 백그라운드 프로세스로 실행하려고 하면, Jline2의 제약 때문에 질의를 실행하기도 전에 tsql은 종료 될 것입니다.
예: 

 .. code-block:: sql

  $ bin/tsql  -f aggregation.sql &
  [1] 19303
  $
  [1]+  Stopped                 ./bin/tsql -f aggregation.sql


이러한 문제를 피하기 위해서, 타조는 다음과 같이 -B 명령을 제공합니다.:

.. code-block:: sql

  $ bin/tsql  -B -f aggregation.sql &
    [2] 19419
    Progress: 0%, response time: 0.218 sec
    Progress: 0%, response time: 0.22 sec
    Progress: 0%, response time: 0.421 sec
    Progress: 0%, response time: 0.823 sec
    Progress: 0%, response time: 1.425 sec
    Progress: 1%, response time: 2.227 sec
