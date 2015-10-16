*****************************
인덱스 (실험중)
*****************************

인덱스는 효율적인 질의 처리에 사용되는 자료구조입니다. 인덱스를 통해, Tajo 질의 엔진은 검색 값을 즉각적으로 얻을 수 있습니다.

인덱스는 실험중인 기능입니다. 인덱스를 사용하고자 한다면, ``index_support`` 브랜치의 소스코드를 다운받으십시오.::

  git clone -b index_support https://git-wip-us.apache.org/repos/asf/tajo.git tajo-index

소스코드를 빌드하려면, :doc:`getting_started` 를 참고하십시오.

이 장에서는 Tajo가 지원하는 인덱스 타입과 인덱스를 이용한 질의 실행, 그리고 인덱스와 관련된 향후 계획에 대해 서술합니다.

.. toctree::
  :maxdepth: 1

  index/types
  index/how_to_use
  index/future_work