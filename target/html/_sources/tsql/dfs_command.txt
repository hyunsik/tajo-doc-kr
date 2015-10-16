*********************************
HDFS 명령 실행법
*********************************

tsql 안에서 하둡의 dfs 명령 (FsShell) 을 실행할 수 있습니다. ``\dfs`` 명령은 하둡 dfs 명령에 대한 축약에 해당합니다. 이 명령을 사용할 때는, 다음과 같이 FsShell 파라미터들을 지정하기만 하면 됩니다:

.. code-block:: sql

  default> \dfs -ls /
  Found 3 items
  drwxr-xr-x   - tajo supergroup          0 2014-08-14 04:04 /tajo
  drwxr-xr-x   - tajo supergroup          0 2014-09-04 02:20 /tmp
  drwxr-xr-x   - tajo supergroup          0 2014-09-16 13:41 /user

  default> \dfs -ls /tajo
  Found 2 items
  drwxr-xr-x   - tajo supergroup          0 2014-08-14 04:04 /tajo/system
  drwxr-xr-x   - tajo supergroup          0 2014-08-14 04:15 /tajo/warehouse

  default> \dfs -mkdir /tajo/temp

  default> \dfs -ls /tajo
  Found 3 items
  drwxr-xr-x   - tajo supergroup          0 2014-08-14 04:04 /tajo/system
  drwxr-xr-x   - tajo supergroup          0 2014-09-23 06:48 /tajo/temp
  drwxr-xr-x   - tajo supergroup          0 2014-08-14 04:15 /tajo/warehouse
