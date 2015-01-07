*****************************
TSQL 소개
*****************************

==========
요약
==========

.. code-block:: bash

  bin/tsql [옵션] [데이터베이스 명]

*데이터베이스 명* 을 지정한 경우, tsql 구동 시 해당 데이터베이스에 자동으로 연결됩니다. 그렇지 않은 경우, tsql은 ``default`` 데이터베이스에 연결됩니다.

옵션 목록

* ``-c "quoted sql"`` : 인용부호 안의 sql 문을 실행하고 쉘을 종료합니다.
* ``-f filename (--file filename)`` : filename에 해당하는 파일 내용을 쉡 입력에 해당하는 명령으로 실행합니다.
* ``-h hostname (--host hostname)`` : 타조 마스터가 실행 중인 장비의 호스트 명을 지정합니다.
* ``-p port (--port port)`` : TCP 포트를 지정합니다. 별도로 지정하지 않은 경우, 기본값은 26002 입니다.
* ``-conf configuration (--conf configuration)`` : 타조 설정 값을 지정합니다.
* ``-param parameter (--param parameter)`` : SQL 파일의 파라미터 값을 사용합니다.
* ``-B (--background)`` : 백그라운드 프로세스로 실행합니다.

===================
tsql 쉘 시작
===================

호스트 명과 포트 번호를 지정하지 않은 경우, tsql은 ${TAJO_HOME}/conf/tajo-site.xml 파일에 정의된 타조 마스터에 연결을 시도합니다. ::

  bin/tsql

  default>

특정 타조 마스터에 연결을 원하는 경우, 다음과 같이 '-h' 그리고 (또는) 'p' 옵션을 사용합니다: ::

  bin/tsql -h localhost -p 9004

  default>

프롬프트는 현재 데이터베이스를 알려줍니다.
