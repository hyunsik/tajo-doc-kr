*********************************
세션 변수들
*********************************


모든 타조 마스터와의 클라이언트 연결은 각각의 고유한 세션을 생성하고, 연결이 끊길 때까지 서로가 이 세션를 사용하게 됩니다. 하나의 세션은 각각마다 설정에 필요한 세션 변수들을 제공합니다.

``tsql`` 은 세션 변수들을 다루기 위한 메타 명령인 ``\set`` 을 제공합니다. 단순히 ``\set`` 명령만으로 모든 세션 변수들을 볼 수 있습니다. ::

  default> \set
  'name1'='val1'
  'name2'='val2'
  'name3'='val3'
       ...

``\set key val`` 는 *key* 에 해당되는 세션 변수에 *val* 에 해당하는 값을 설정합니다. ::

  default> \set
  'CURRENT_DATABASE'='default'

  default> \set key1 val1

  default> \set
  'CURRENT_DATABASE'='default'
  'key1'='val1'


또한, ``\unset key`` 는 *key* 에 해당하는 세션 변수에 값을 미설정 상태로 초기화합니다.


현재 타조는 다음과 같은 세션 변수들을 제공합니다.

* ``DIST_QUERY_BROADCAST_JOIN_THRESHOLD``
* ``DIST_QUERY_JOIN_TASK_VOLUME``
* ``DIST_QUERY_SORT_TASK_VOLUME``
* ``DIST_QUERY_GROUPBY_TASK_VOLUME``
* ``DIST_QUERY_JOIN_PARTITION_VOLUME``
* ``DIST_QUERY_GROUPBY_PARTITION_VOLUME``
* ``DIST_QUERY_TABLE_PARTITION_VOLUME``
* ``EXECUTOR_EXTERNAL_SORT_BUFFER_SIZE``
* ``EXECUTOR_HASH_JOIN_SIZE_THRESHOLD``
* ``EXECUTOR_INNER_HASH_JOIN_SIZE_THRESHOLD``
* ``EXECUTOR_OUTER_HASH_JOIN_SIZE_THRESHOLD``
* ``EXECUTOR_GROUPBY_INMEMORY_HASH_THRESHOLD``
* ``MAX_OUTPUT_FILE_SIZE``
* ``CODEGEN``
* ``CLIENT_SESSION_EXPIRY_TIME``
* ``CLI_MAX_COLUMN``
* ``CLI_NULL_CHAR``
* ``CLI_PRINT_PAUSE_NUM_RECORDS``
* ``CLI_PRINT_PAUSE``
* ``CLI_PRINT_ERROR_TRACE``
* ``CLI_OUTPUT_FORMATTER_CLASS``
* ``CLI_ERROR_STOP``
* ``TIMEZONE``
* ``DATE_ORDER``
* ``TEXT_NULL``
* ``DEBUG_ENABLED``
* ``TEST_BROADCAST_JOIN_ENABLED``
* ``TEST_JOIN_OPT_ENABLED``
* ``TEST_FILTER_PUSHDOWN_ENABLED``
* ``TEST_MIN_TASK_NUM``
* ``BEHAVIOR_ARITHMETIC_ABORT``
* ``RESULT_SET_FETCH_ROWNUM``


