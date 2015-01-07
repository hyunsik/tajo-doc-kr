*****************
소스코드 빌드
*****************

필요한 부분들과 소스가 준비되었다면, 이제 소크코드를 빌드할 수 있다

설치 과정 첫 단계로, 자신의 시스템에 맞게 소스트리를 설정하고 원하는 옵션들을 선택한다. 이 과정은 설정 스크립트를 실행해서 수행한다. 기본 설치는 단순히 enter를 치면 된다.

소스코드를 컴파일을 통해 바이너리 아카이브 생성은 다음과 같이 한다:

.. code-block:: bash

  $ cd tajo-x.y.z
  $ mvn clean install -DskipTests -Pdist -Dtar -Dhadoop.version=2.X.X
  $ ls tajo-dist/target/tajo-x.y.z-SNAPSHOT.tar.gz

.. note::

  하둡 버전을 지정하지 않으면, 타조 클러스터가 동작하지 않기 때문에, maven build 명령을 통해 반다시 하둡 버전을 지정할 것을 권한다.

  예:

    $ mvn clean install -DskipTests -Pdist -Dtar -Dhadoop.version=2.5.1

다음 단계로, 다음과 같이, 생성된 tar.gz 파일을 적절한 디렉토리에 옮긴 후, 압축을 푼다:

.. code-block:: bash

  $ cd [a directory to be parent of tajo binary]
  $ tar xzvf ${TAJO_SRC}/tajo-dist/target/tajo-x.y.z-SNAPSHOT.tar.gz