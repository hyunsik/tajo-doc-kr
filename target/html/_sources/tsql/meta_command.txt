*********************************
메타 명령
*********************************


tsql에서 역슬래시 ('\\')로 시작하는 모든 명령들은 tsql 차체에서 처리되는 매타 명령들입니다.

현재는 다음과 같은 메타 명령들이 구현되어 있습니다: ::

  default> \?


  General
    \copyright    show Apache License 2.0
    \version      show Tajo version
    \?            show help
    \? [COMMAND]  show help of a given command
    \help         alias of \?
    \q            quit tsql


  Informational
    \l           list databases
    \c           show current database
    \c [DBNAME]  connect to new database
    \d           list tables
    \d [TBNAME]  describe table
    \df          list functions
    \df NAME     describe function


  Tool
    \!           execute a linux shell command
    \dfs         execute a dfs command
    \admin       execute Tajo admin command


  Variables
    \set [[NAME] [VALUE]  set session variable or list session variables
    \unset NAME           unset session variable


  Documentations
    tsql guide        http://tajo.apache.org/docs/current/cli.html
    Query language    http://tajo.apache.org/docs/current/sql_language.html
    Functions         http://tajo.apache.org/docs/current/functions.html
    Backup & restore  http://tajo.apache.org/docs/current/backup_and_restore.html
    Configuration     http://tajo.apache.org/docs/current/configuration.html

-----------------------------------------------
기본 사용법
-----------------------------------------------

``\l`` 명령은 다음과 같이 모든 데이터베이스 목록을 보여줍니다: ::

  default> \l
  default
  tpch
  work1
  default>



``\d`` 명령은 다음과 같이 현재 데이터베이스의 테이블 목록을 보여줍니다: ::

  default> \d
  customer
  lineitem
  nation
  orders
  part
  partsupp
  region
  supplier


``\d [테이블 명]`` 명령은 다음과 같이 테이블 설명을 보여줍니다: ::

  default> \d orders

  table name: orders
  table path: hdfs:/xxx/xxx/tpch/orders
  store type: CSV
  number of rows: 0
  volume (bytes): 172.0 MB
  schema:
  o_orderkey      INT8
  o_custkey       INT8
  o_orderstatus   TEXT
  o_totalprice    FLOAT8
  o_orderdate     TEXT
  o_orderpriority TEXT
  o_clerk TEXT
  o_shippriority  INT4
  o_comment       TEXT



프롬프트인 ``default>`` 는 현재 데이터베이스를 나타냅니다. 기본적으로, 모든 SQL 문과 메타 명령들은 현재 데이터베이스에 대해 동작합니다. 그리고, ``\c`` 명령으로 현재 데이터베이스를 변경할 수 있습니다.

.. code-block:: sql

  default> \c work1
  You are now connected to database "test" as user "hyunsik".
  work1>


``\df`` 명령은 다음과 같이 기본 제공되는 함수 목록을 보여줍니다: ::

  default> \df
   Name            | Result type     | Argument types        | Description                                   | Type
  -----------------+-----------------+-----------------------+-----------------------------------------------+-----------
   abs             | INT4            | INT4                  | Absolute value                                | GENERAL
   abs             | INT8            | INT8                  | Absolute value                                | GENERAL
   abs             | FLOAT4          | FLOAT4                | Absolute value                                | GENERAL
   abs             | FLOAT8          | FLOAT8                | Absolute value                                | GENERAL
   acos            | FLOAT8          | FLOAT4                | Inverse cosine.                               | GENERAL
   acos            | FLOAT8          | FLOAT8                | Inverse cosine.                               | GENERAL
   utc_usec_to     | INT8            | TEXT,INT8             | Extract field from time                       | GENERAL
   utc_usec_to     | INT8            | TEXT,INT8,INT4        | Extract field from time                       | GENERAL

  (181) rows

  참고로, 처리에 대한 명확한 설명을 위해 많은 세부적인 부분들은 생략하였습니다.

``\df [함수 명]`` 명령은 다음과 같이 해당 함수에 대한 설명을 보여줍니다: ::

  default> \df round;
   Name            | Result type     | Argument types        | Description                                   | Type
  -----------------+-----------------+-----------------------+-----------------------------------------------+-----------
   round           | INT8            | FLOAT4                | Round to nearest integer.                     | GENERAL
   round           | INT8            | FLOAT8                | Round to nearest integer.                     | GENERAL
   round           | INT8            | INT4                  | Round to nearest integer.                     | GENERAL
   round           | INT8            | INT8                  | Round to nearest integer.                     | GENERAL
   round           | FLOAT8          | FLOAT8,INT4           | Round to s decimalN places.                   | GENERAL
   round           | FLOAT8          | INT8,INT4             | Round to s decimalN places.                   | GENERAL

  (6) rows

  Function:    INT8 round(float4)
  Description: Round to nearest integer.
  Example:
  > SELECT round(42.4)
  42

  Function:    FLOAT8 round(float8,int4)
  Description: Round to s decimalN places.
  Example:
  > SELECT round(42.4382, 2)
  42.44
