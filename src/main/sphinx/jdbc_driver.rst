*************************************
Tajo JDBC Driver
*************************************

Apache Tajo™  JDBC 드라이버를 제공합니다.  java 어플리케이션에서  RDBMS 접속하듯이 Tajo의 JDBC드라이버를 이용해서
쉽게 접속할 수 있습니다.
이 절에서는, 어떻게 JDBC Driver를 얻고 사용할 수 있는지 예제코드를 통해서 설명합니다.
In this section, we explain how to get JDBC driver and an example code.

How to get JDBC driver
=======================

배포판에서 JDBC Driver jar파일을 얻는 법
--------------------------------

Tajo의 배포판에는 JDBC Driver가 포함되어 있습니다. Driver jar파일의 경로는 아래와 같습니다.
``${TAJO_HOME}/share/jdbc-dist/``


소스코드 빌드를 통해서 JDBC Jar파일을 얻는 법
--------------------------------

또는 직접 source Code를 빌드하신다면 아래와 같이 우선  소스코드 압축파일의 압축을 푼 후에
메이븐 명령을 통해서 빌드를 수행하면 메이븐의 빌드결과 디렉토리인 target 디렉토리의 하위에
Tajo의 배포판과 동일한 폴더구조가 생성됩니다. 상세 경로는 아래와 같습니다.


.. code-block:: bash
  $ tar xzvf tajo-x.y.z-src.tar.gz 
  $ mvn clean package -DskipTests -Pdist -Dtar 
  $ ls -l tajo-dist/target/tajo-x.y.z/share/jdbc-dist


CLASSPATH에 JDBC Jar 등록
=======================

JDBC Driver를 찾았다면 이제 classpath에 등록을 해주어야 합니다.  또한 hadoop 에 대한 classpath도 반드시 등록해줘야 합니다.
아래와 같이 classpath 환경변수에 설정할 수 있습니다.
.. code-block:: bash

  CLASSPATH=path/to/tajo-jdbc/*:path/to/tajo-site.xml:path/to/core-site.xml:path/to/hdfs-site.xml

.. note::
  그리고 Tajo의 환경설정파일과 hadoop의 설정파일도 classpath에 등록해주어야 합니다. 예를 들면 ''tajo-site.xml'' ,
  ''core-site.xml'' 그리고 ''hdfs-site.xml'' 과 같은 파일들을 등록해야 합니다.
 

Tajo의 JDBC Driver를 이용해서 client에서 접속하는 예제
=======================

Tajo의 JDBC Driver 이름은 ``org.apache.tajo.jdbc.TajoDriver``. 
clssForName구문을 이용해서 등록할 수 있습니다.``Class.forName("org.apache.tajo.jdbc.TajoDriver")``.
접속 url은 JDBC url규약에 따라  ``jdbc:tajo://<TajoMaster hostname>:<TajoMaster client rpc port>/<database name>``
같은 형태가 됩니다. 
Tajo에서 클라이언트의 원격접속을 위한 기본포트는  ``26002`` 을 사용합니다. 
따라서 만약 포트를 변경하고 싶으시면 설정 문서를 참고하세요. `/configuration/configuration_defaults`.

.. note::

  현재 Tajo는 더 이상 데이터베이스의 네임스페이스 컨셉에 대해서 지원하지 않습니다. 모든 테이블은 ''default'' 데이터베이스 안에 포함되어 있습니다.
  따라서 Tajo를 사용하실 때에는 mysql 과 같은 데이터베이스처럼 database명을 지정하지 않으셔도 됩니다.


그럼 이제 실제로 작성된 예제를 보도록 하겠습니다.

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
만약 위와 같은 결과를 얻으셨다면 tajo JDBC Driver가 HDFS의 data Node에 접근을 시도했다가 실패했을 확률이 높습니다.
많은 경우에 HDFS Cluster가 private network로 구축되어 있어서 이 때문에 네트워크적으로 접근할 수 없어서 에러가 발생하는
경우가 많은데요 이때에는 호스트네임을 클라이언트사이드에서 공유해서 JDBC Driver로 접근가능하도록 해주어야 합니다.


