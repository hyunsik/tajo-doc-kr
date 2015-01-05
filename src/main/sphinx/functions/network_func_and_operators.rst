******************************
네트워크 함수와 연산자들
******************************

=============
전제조건
=============

아파치 타조는 GeoIP 데이터베이스를 사용한 네트워크 함수와 연산자들을 제공합니다.
네트워크 함수와 연산자들을 사용하기 위해서는 모든 워커의 로컬디스크에 GeoIP 데이터베이스가 설치되어 있어야 합니다.
(GeoIP의 설치를 위해서는 이 링크를 참고해 주세요:  http://dev.maxmind.com/geoip/legacy/downloadable/)

GeoIP 데이터베이스가 설치되었다면, 설치위치를 ``conf/tajo-site.xml`` 에 아래와 같이 입력해주시면 모든 준비가 끝납니다.
::
  <property>
    <name>tajo.function.geoip-database-location</name>
    <value>/path/to/geoip/database/file</value>
  </property>

===================
지원되는 함수들
===================

.. function:: geoip_country_code (string addr)

  입력된 IPv4 주소를 GeoIP의 국가코드로 변환해 돌려줍니다.
 
  :인자 addr: 문자열로 입력된 IPv4 주소
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select geoip_country_code('163.152.71.31')
    > 'KR'

.. function:: geoip_country_code (inet4 addr)

  입력된 IPv4 주소를 GeoIP의 국가코드로 변환해 돌려줍니다.
  
  :인자 addr: inet4 타입으로 입력된 IPv4 주소
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select geoip_country_code('163.152.71.31')
    > 'KR'

.. function:: geoip_in_country (string addr, string code)

  입력된 주소와 국가코드가 일치하다면 true 를 돌려줍니다. 그렇지않다면, false를 돌려줍니다.

  :인자 addr: 문자열로 입력된 IPv4 주소
  :인자 code: 국가코드
  :리턴타입: boolean
  :예제:

  .. code-block:: sql

    select geoip_in_country('163.152.71.31', 'KR')
    > true

.. function:: geoip_in_country (inet4 addr, string code)

  입력된 주소와 국가코드가 일치하다면 true 를 돌려줍니다. 그렇지않다면, false를 돌려줍니다.

  :인자 addr: inet4 타입으로 입력된 IPv4 주소
  :인자 code: 국가코드
  :리턴타입: boolean
  :예제:

  .. code-block:: sql

    select geoip_in_country(163.152.71.31, 'KR')
    > true
