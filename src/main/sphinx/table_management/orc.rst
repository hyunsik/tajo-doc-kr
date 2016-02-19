***
ORC
***

**ORC(Optimized Row Columnar)** 는 하이브(Hive)에서 나온 컬럼 지향 저장 포맷입니다. ORC는 읽기, 쓰기 및 데이터 처리 성능이 뛰어납니다.
자세한 설명은 하이브 위키의 `ORC Files <https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC>`_ 를 참고 바랍니다.

=======================
ORC 테이블은 어떻게 만드나요?
=======================

``CREATE TABLE`` SQL문에 익숙하지 않다면 :doc:`/sql_language/ddl` 문서를 참고 바랍니다.

테이블에 특정 파일 포맷을 지정하기 위해서는 ``CREATE TABLE`` 문에 ``USING`` 키워드를 사용해야 합니다. 아래 예제는 orc 파일을 위한 테이블을 만드는 것입니다.

.. code-block:: sql

  CREATE TABLE table1 (
    id int,
    name text,
    score float,
    type text
  ) USING orc;

==========
물리적 속성들
==========

일부 테이블 저장 포맷은 테이블의 물리적 속성을 조정하고 혹은 특정 기능을 끄고 켤 수 있도록 제공합니다.
사용자들은 CREATE TABLE 문에 WITH 절을 써서 그런 속성들을 제어할 수 있습니다.

아래는 ORC 파일이 제공하는 물리적 속성들입니다.

* ``orc.max.merge.distance``: ORC 파일을 읽을 때 스트라입(stripe)이 이 설정값보다 가깝게 위치한다면 병합하여 한 번에 읽어들입니다. 기본값은 1MB입니다.
* ``orc.max.read.buffer``: ORC 파일을 읽을 때 읽기 버퍼 크기의 최대치를 설정합니다. 다시 말하면 한 번에 읽는 최대의 크기를 의미하기도 합니다. 기본값은 8MB입니다.
* ``orc.stripe.size``: 각 스트라입의 크기를 결정합니다. 기본값은 64MB입니다.
* ``orc.compression.kind``: 데이터를 쓸 때 어떤 압축 알고리즘을 쓸 지를 의미합니다. ``none``, ``snappy``, ``zlib`` 중의 하나여야 하며 기본값은 ``none``입니다.
* ``orc.buffer.size``: 쓰기 버퍼의 크기를 결정합니다. 기본값은 256KB 입니다.
* ``orc.rowindex.stride``: ORC 인덱스 스트라이드 기본값을 결정합니다(스트라이드는 인덱스 엔트리 하나가 나타내는 로우 수입니다). 기본값은 10000입니다.

=====================================
아파치 하이브(Apache Hive™) 와의 호환성 이슈
=====================================

현재 타조는 플랫한 관계 테이블만을 지원합니다.
현재 중첩 스키마나 비-스칼라 타입 지원을 위한 작업 중입니다 (`TAJO-710 <https://issues.apache.org/jira/browse/TAJO-710>`_).