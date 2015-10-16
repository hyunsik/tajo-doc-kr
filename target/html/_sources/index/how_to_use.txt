*************************************
인덱스 사용법
*************************************

-------------------------------------
1. 인덱스 생성
-------------------------------------

인덱스를 활용하기 위한 첫 번째 단계는 인덱스 생성입니다. SQL을 이용해 인덱스를 생성할 수 있습니다(:doc:`/sql_language/ddl`). Tajo API를 통해서도 가능합니다(:doc:`/tajo_client_api`). 예를 들어, 당신은 아래의 질의문을 Tajo에 입력함으로써 lineitem 테이블의 BST 인덱스를 생성할 수 있습니다.

.. code-block:: sql

     default> create index l_orderkey_idx on lineitem (l_orderkey);

인덱스가 성공적으로 생성되면, 당신은 해당 인덱스에 대해 아래와 같은 정보를 볼 수 있습니다: ::

  default> \d lineitem

  table name: default.lineitem
  table path: hdfs://localhost:7020/tpch/lineitem
  store type: CSV
  number of rows: unknown
  volume: 753.9 MB
  Options:
  	'text.delimiter'='|'

  schema:
  l_orderkey	INT8
  l_partkey	INT8
  l_suppkey	INT8
  l_linenumber	INT8
  l_quantity	FLOAT4
  l_extendedprice	FLOAT4
  l_discount	FLOAT4
  l_tax	FLOAT4
  l_returnflag	TEXT
  l_linestatus	TEXT
  l_shipdate	DATE
  l_commitdate	DATE
  l_receiptdate	DATE
  l_shipinstruct	TEXT
  l_shipmode	TEXT
  l_comment	TEXT


  Indexes:
  "l_orderkey_idx" TWO_LEVEL_BIN_TREE (l_orderkey ASC NULLS LAST )

-------------------------------------
2. 인덱스 스캔 허가/불허
-------------------------------------

인덱스가 성공적으로 생성되면, 당신은 반드시 아래와 같이 인덱스 스캔 기능을 허가해야 합니다:

.. code-block:: sql

     default> \set INDEX_ENABLED true

만약 더 이상 인덱스 스캔 기능을 사용하지 않으려면, 아래와 같이 간단히 불허할 수 있습니다:

.. code-block:: sql

     default> \set INDEX_ENABLED false

.. note::

     인덱스 스캔 기능이 한번 허가가 되면, 현재 Tajo는 효율성에 대한 고려 없이 언제나 해당 기능을 수행합니다. 수행 결과로 얻게 될 튜플의 갯수가 적절할 것으로 예상된다면 이 옵션을 설정해야 합니다.