*************************************
타조 JDBC 드라이버
*************************************

아파치 타조 프로젝트는 자바 프로그램에서 아파치 타조를 RDBMS 같은 방식으로 쉽게 접근할 수 있도록 JDBC 드라이버를 제공합니다. 이 섹션에서는 JDBC 드라이버를 받는 방법과 예제 코드를 설명합니다.

JDBC 드라이버 받는 법
=======================

바이너리 배포 버전에서 찾기
--------------------------------

타조 바이너리 배포 버전에는 JDBC jar 파일과 이에 의존성이 있는 다른 jar 파일들을 포함되어 있습니다. 이 파일들은 ``${TAJO_HOME}/share/jdbc-dist/``에 위치합니다.

소스 코드 빌드 하기
--------------------------------

다음 명령을 통해 타조 소스 코드로부터 JDBC 및 관련 jar 파일들을 빌드할 수 있습니다:

.. code-block:: bash

  $ tar xzvf tajo-x.y.z-src.tar.gz
  $ mvn clean package -DskipTests -Pdist -Dtar
  $ ls -l tajo-dist/target/tajo-x.y.z/share/jdbc-dist


CLASSPATH 설정하기
=======================

JDBC 드라이버를 사용하려면, ``tajo-dist/target/tajo-x.y.z/share/jdbc-dist``에 있는 jar 파일들을 ``CLASSPATH``에 넣어주어야 합니다. 또, 하둡 경로도 ``CLASSPATH``에 포함되어야 합니다. 따라서, ``CLASSPATH``는 다음과 같게 설정됩니다:

.. code-block:: bash

  CLASSPATH=path/to/tajo-jdbc/*:path/to/tajo-site.xml:path/to/core-site.xml:path/to/hdfs-site.xml

.. note::

  타조 설정 파일(예: ``tajo-site.xml``)과 하둡 설정 파일(예: ``core-site.xml``, ``hdfs-site.xml``)이 있는 경로를 꼭 ``CLASSPATH``에 넣어야 합니다.

JDBC 클라이언트 예제
=======================

JDBC 드라이버 클래스의 이름은 ``org.apache.tajo.jdbc.TajoDriver``입니다. 드라이버 클래스를 로딩하려면 ``Class.forName("org.apache.tajo.jdbc.TajoDriver")`` 명령을 수행합니다. 접속 url은 ``jdbc:tajo://<TajoMaster hostname>:<TajoMaster client rpc port>/<database name>``여야 합니다. 타조 마스터의 기본 클라이언트 RPC 접속 포트는 ``26002``입니다. 접속 포트를 변경하려면 :doc:`/configuration/service_config_defaults`를 참고하세요.

.. note::
  
  현재 타조는 데이터베이스 및 네임스페이스 개념을 지원하지 않습니다. 모든 테이블은 ``default`` 데이터베이스안에 존재합니다. 따라서 데이터베이스명을 지정할 필요가 없습니다.

아래는 JDBC 클라이언트의 예제입니다.

.. code-block:: java

  import java.sql.Connection;
  import java.sql.ResultSet;
  import java.sql.Statement;
  import java.sql.DriverManager;

  public class TajoJDBCClient {
    
    ....

    public static void main(String[] args) throws Exception {

      try {
        Class.forName("org.apache.tajo.jdbc.TajoDriver");
      } catch (ClassNotFoundException e) {
        // fill your handling code
      }

      Connection conn = DriverManager.getConnection("jdbc:tajo://127.0.0.1:26002/default");

      Statement stmt = null;
      ResultSet rs = null;
      try {
        stmt = conn.createStatement();
        rs = stmt.executeQuery("select * from table1");
        while (rs.next()) {
          System.out.println(rs.getString(1) + "," + rs.getString(3));
        }
      } finally {
        if (rs != null) rs.close();
        if (stmt != null) stmt.close();
        if (conn != null) conn.close();
      }
    }
  }


FAQ
===========================================

java.nio.channels.UnresolvedAddressException
--------------------------------------------

최종 결과를 가져올 때, 타조 JDBC 드라이버는 HDFS 데이터 노드에 접근을 시도합니다. 따라서 JDBC 클라이언트와 HDFS 데이터 노드는 네트워크 접속이 가능해야 합니다. 많은 경우에 HDFS 클러스터는 사설 네트워크 안에서 사설 호스트명을 가지도록 구축됩니다. 이 경우, JDBC 클라이언트 쪽에 해당 호스트명을 등록해 주어야 합니다.

