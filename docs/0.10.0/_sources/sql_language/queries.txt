**************************
질의하기
**************************

=====================
질의구문 개요
=====================

.. code-block:: sql

  SELECT [distinct [all]] * | <expression> [[AS] <alias>] [, ...]
    [FROM <table reference> [[AS] <table alias name>] [, ...]]
    [WHERE <condition>]
    [GROUP BY <expression> [, ...]]
    [HAVING <condition>]
    [ORDER BY <expression> [ASC|DESC] [NULL FIRST|NULL LAST] [, ...]]



=====================
FROM 절
=====================

*개요*

.. code-block:: sql

  [FROM <table reference> [[AS] <table alias name>] [, ...]]


``FROM`` 절을 사용해 하나 이상의 테이블을 컴마로 구분하여 table reference위치에 지정할 수 있습니다.
테이블을 지정하기 위해서 스키마 상의 테이블 이름, 서브쿼리, 조인 테이블, 혹은 이들의 복잡한 조합을 사용할 수 있습니다.

-----------------------
테이블과 테이블 별명
-----------------------

테이블 별명을 사용해 특정 테이블에 또다른 테이블 이름을 지정할 수 있습니다. 
또한 질의의 일부분으로 부터 생성된 테이블에 대해서도 테이블 별명을 통해 임시로 이름을 지어줄 수 있습니다.

테이블 별명을 만들기 위해서는 아래와 같은 구문처럼 ``AS`` 를 사용해 주세요.

.. code-block:: sql

  FROM table_reference AS alias

또한 아래처럼 테이블 별명을 지정할 수도 있습니다.

.. code-block:: sql

  FROM table_reference alias

``AS`` 키워드를 사용하시지 않는다면 *alias* 위치에 어떠한 이름을 지정하셔도 괜찮습니다.

테이블 별명의 일반적인 사용 예는 아래의 예제처럼 매우 긴 테이블 이름에 짧은 테이블 별명을 지정해 주는 것입니다.

.. code-block:: sql

  SELECT * FROM long_table_name_1234 s JOIN another_long_table_name_5678 a ON s.id = a.num;

이제 여러분은 long_table_name_1234 라는 이름을 가진 테이블을 s 라는 테이블 별명만을 사용해 간단히 지정할 수 있습니다.

-------------
조인된 테이블
-------------

타조는 모든 타입의 조인을 지원합니다!


조인 타입
~~~~~~~~~~

Cross Join
^^^^^^^^^^

.. code-block:: sql

  FROM T1 CROSS JOIN T2


*Cartesian product* 라고도 불리는 Cross Join은 테이블 T1 과 테이블 T2 로부터 가능한 모든 조합의 row들을 결과로 출력합니다.

``FROM T1 CROSS JOIN T2`` 구문은 ``FROM T1, T2`` 구문과 동일합니다.

Qualified joins
^^^^^^^^^^^^^^^

Qualified joins 은 조인조건을 지정해 줄 수도 있고,  암묵적인 조인 조건을 가지고 있을 수도 있습니다. 
Inner/Outer/Natural 조인타입 모두가 qualified joins 으로 사용될 수 있습니다.
Natural join을 제외하고, ``ON`` 혹은 ``USING`` 절을 각 조인에 사용해 특별한 조인 조건을 지정해 줄 수 있습니다.
조인 조건은 반드시 하나이상의 boolean 수식을 포함하고 있어야 합니다. 또한 필터 조건만을 포함하고 있어도 됩니다.

**Inner Join**

.. code-block:: sql

  T1 [INNER] JOIN T2 ON boolean_expression
  T1 [INNER] JOIN T2 USING (join column list)

``INNER`` 키워드는 기본으로 사용되는 조인타입이며 inner join을 사용할 때 ``INNER`` 키워드는 생략될 수 있습니다.

**Outer Join**

.. code-block:: sql

  T1 (LEFT|RIGHT|FULL) OUTER JOIN T2 ON boolean_expression
  T1 (LEFT|RIGHT|FULL) OUTER JOIN T2 USING (join column list)

``LEFT``, ``RIGHT``, 혹은 ``FULL`` 키워드 중 하나를 반드시 사용해 outer join 의 정확한 조인타입을 지정해 주어야 합니다.
Outer join의 조인 조건은 참조된 테이블의 조인 조건에 따라 다르게 적용됩니다. 
Outer join에 대해 더 자세한 정보를 원하시면 `Advanced outer join constructs  <http://www.ibm.com/developerworks/data/library/techarticle/purcell/0201purcell.html>`_ 를 참조해 주세요.

**Natural Join**

.. code-block:: sql

  T1 NATURAL JOIN T2

``NATURAL`` 키워드는  ``USING`` 키워드의 짧은 형태입니다. 이는 ``USING`` 키워드를 사용했을 때 두 테이블이 가진 모든 공통된 컬럼을 지정한 경우와
동일한 의미를 지닙니다. 동일한 컬럼들은 출력 테이블에 중복되지 않고 한번만 나타납니다. 
만약 동일한 컬럼이 없다면, ``NATURAL`` 조인은 ``CROSS JOIN`` 과 동일하게 동작합니다.

**Subqueries**

서브쿼리를 사용해 파생테이블을 지정할 수 있습니다. 서브쿼리는 아래의 예제처럼 괄호를 사용해 SQL 구문을 포함하는데 필요하며, 테이블 별명을 사용해 파생 테이블에 접근 할 수 있습니다. 

.. code-block:: sql

  FROM (SELECT * FROM table1) AS alias_name

=====================
Where 절
=====================

Where 절은 아래와 같은 형태로 사용됩니다.

*개요*

.. code-block:: sql

  WHERE search_condition

``search_condition`` 은 모든 boolean 수식을 포함할 수 있습니다. 
추가적인 조건절을 알고 싶으시다면, :doc:`/sql_language/predicates` 을 참조해 주세요.

==========================
Groupby 와 Having 절
==========================

*개요*

.. code-block:: sql

  SELECT select_list
      FROM ...
      [WHERE ...]
      GROUP BY grouping_column_reference [, grouping_column_reference]...
      [HAVING boolean_expression]

``WHERE`` 절을 통과한 row들은 filter ``GROUP BY`` 절에 명시된 grouping의 대상이 될 수도 있습니다.
Grouping 은 동일한 값을 가진 row의 집합을 하나의 그룹으로 만들고, 집계 함수를 사용해 동일 그룹에 포함된 row들을 계산합니다.
``HAVING`` 절은 ``GROUP BY`` 절과 함께 사용되며, 조건에 맞지 않는 row들을 제거합니다.

``grouping_column_reference`` 위치에 특정한 컬럼을 지정할 수도 있고, 스칼라 함수와 사칙 연산을 포함한 복잡한 수식도 포함할 수 있습니다.

.. code-block:: sql

  SELECT l_orderkey, SUM(l_quantity) AS quantity FROM lineitem GROUP BY l_orderkey;

  SELECT substr(l_shipdate,1,4) as year, SUM(l_orderkey) AS total2 FROM lineitem GROUP BY substr(l_shipdate,1,4);

만약 SQL 수식이 ``GROUP BY`` 절을 포함한다면, select 구문은 ``grouping_column_reference`` 혹은 집계 함수들의 리스트를 포함하고 있어야 합니다.
예를 들어, 아래의 예제는 ``GROUP BY`` 절에  ``l_orderkey`` 컬럼이 포함되어 있지 않기 때문에 올바른 질의가 아닙니다.


.. code-block:: sql

  SELECT l_orderkey, l_partkey, SUM(l_orderkey) AS total FROM lineitem GROUP BY l_partkey;

집계 함수는 ``DISTINCT`` 키워드를 포함할 수 있습니다. 
이를 포함한 집계 함수들은 집계 수식에 포함된 특정 컬럼에 대해 중복되지 않은 유일한 값 하나씩을 집계 함수의 입력값으로 사용해 합계를 계산합니다.
이에 대한 예제가 아래에 있습니다.

.. code-block:: sql

  SELECT l_partkey, COUNT(distinct l_quantity), SUM(distinct l_extendedprice) AS total FROM lineitem GROUP BY l_partkey;

==========================
Orderby 와 Limit 절
==========================

*개요*

.. code-block:: sql

  FROM ... ORDER BY <sort_expr> [(ASC|DESC)] [NULL (FIRST|LAST) [,...]

``sort_expr`` 키워드는 컬럼 이름,  컬럼의 별명, 그리고 복잡한 수식을 포함할 수 있습니다. 
``ASC`` 키워드는 ``sort_expr`` 값이 오름차순으로 정렬될 것임을 의미합니다. 
``DESC`` 키워드는 반대로 ``sort_expr`` 값이 내림차순으로 정렬될 것임을 의미합니다.
``ASC`` 가 기본 정렬순서로 사용됩니다.

``NULLS FIRST`` 와 ``NULLS LAST`` 옵션은 정렬 연산 중 null값 이 non-null값 전과 후에 등장했을때 null값을 어떻게 처리할 지를 결정합니다.
기본적으로, null 값은 어떤 non-null 값 보다 크게 처리됩니다.
즉, ``NULLS FIRST`` 는 ``DESC`` 정렬 순서의 기본 동작방식이고, ``NULLS LAST`` 의 경우는 반대입니다.

==========================
Window 함수
==========================

Window 함수는 어떤 window 프레임에 속하는 여러 테이블의 row들을 대상으로 계산을 수행합니다.

*개요*

.. code-block:: sql

  SELECT ...., func(param) OVER ([PARTITION BY partition-expr [, ...]] [ORDER BY sort-expr [, ...]]), ....,  FROM

OVER 키워드 내의 PARTITION BY 키워드는 PARTITION BY 수식들에 명시된 값에서 같은 값을 지닌 row들을 여러개의 그룹, 혹은 파티션으로 구분하도록 합니다.
각각의 row들에 대해서 현재 row와 같은 파티션 내에 속하는 row들에 대해 window 함수를 사용해 원하는 연산을 수행합니다. 

아래에 window 함수들에 대한 몇몇 간단한 예제를 설명하였습니다.

---------
예제
---------

여러개의 window 함수들은 SQL 구문에 아래와 같이 사용될 수 있습니다.

.. code-block:: sql

  SELECT l_orderkey, sum(l_discount) OVER (PARTITION BY l_orderkey), sum(l_quantity) OVER (PARTITION BY l_orderkey) FROM LINEITEM;

만약 ``OVER()`` 절이 아래와 같이 비어있다면, 모든 테이블의 row들을 하나의 window 프레임에 포함되도록 합니다.

.. code-block:: sql

  SELECT salary, sum(salary) OVER () FROM empsalary;

또한, ``ORDER BY`` 절의 경우 아래와 같이 ``PARTITION BY`` 절 없이도 사용될 수 있습니다.

.. code-block:: sql

  SELECT salary, sum(salary) OVER (ORDER BY salary) FROM empsalary;

이와 더불어, 모든 수식과 집계 함수들은 아래의 예제 처럼 ``ORDER BY`` 내에서 사용될 수 있습니다. 

.. code-block:: sql

  select
    l_orderkey,
    count(*) as cnt,
    row_number() over (partition by l_orderkey order by count(*) desc)
    row_num
  from
    lineitem
  group by
    l_orderkey

.. note::
  현제, 타조는 하나의 SQL 수식에 여러개의 다른 파티션 구문에 대해 지원하고 있지 않습니다.
