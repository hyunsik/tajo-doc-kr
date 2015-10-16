******************
테이블 관리
******************

Tajo에서 테이블은 데이터에 대한 논리적인 관점을 제공합니다. 테이블은 논리적인 스키마, 파티션들, URL과 많은 속성들의 논리적 조합으로 생각할 수 있습니다. 
물리적인 관점에서 테이블은 HDFS상의 폴더, 하나의 파일, 하나의 HBase 테이블, 혹은 RDBMS 테이블이 될 수 있습니다. 
Tajo를 잘 활용하기 위해서 여러분은 테이블의 물리적인 레이아웃에 대해 이들의 여러 속성들과 특징들을 정확하게 이해하여야 합니다. 
이 섹션은 Tajo의 테이블 관리에 대한 모든것을 담고 있습니다.

.. toctree::
    :maxdepth: 1

    table_management/table_overview
    table_management/file_formats
    table_management/compression
