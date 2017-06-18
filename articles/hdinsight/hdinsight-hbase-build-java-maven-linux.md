<properties
	pageTitle="Maven 및 Java를 사용하여 HBase 응용 프로그램을 빌드한 다음 Linux 기반 HDInsight에 배포 | Microsoft Azure"
	description="Apache Maven을 사용하여 Java 기반 Apache HBase 응용 프로그램을 빌드한 다음 Azure 클라우드에서 Linux 기반 HDInsight에 배포하는 방법에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="larryfr"/>

#Maven을 사용하여 Linux 기반 HDInsight(Hadoop)에서 HBase를 사용하는 Java 응용 프로그램 빌드

Apache Maven을 사용하여 Java로 [Apache HBase](http://hbase.apache.org/) 응용 프로그램을 만들어 빌드하는 방법을 알아봅니다. 그런 다음 Linux 기반 HDInsight 클러스터에서 응용 프로그램을 사용합니다.

[Maven](http://maven.apache.org/)은 Java 프로젝트용 소프트웨어, 문서화 및 보고를 빌드할 수 있는 소프트웨어 프로젝트 관리 및 종합 도구입니다. 이 문서에서는 Maven을 사용하여 Linux 기반 HDInsight 클러스터에서 HBase 테이블을 만들고, 쿼리하고, 삭제하는 기본 Java 응용 프로그램을 만드는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서의 단계는 Linux 기반 HDInsight 클러스터를 사용하고 있다고 가정합니다. Windows 기반 HDInsight 클러스터 사용에 대한 자세한 내용은 [Maven을 사용하여 Windows 기반 HDInsight에서 HBase를 사용하는 Java 응용 프로그램 빌드](hdinsight-hbase-build-java-maven.md)를 참조하세요.

##요구 사항

* [Java 플랫폼 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 이상

* [Maven](http://maven.apache.org/)

* [Linux 기반 Azure HDInsight 클러스터 및 HBase](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] 이 문서의 단계는 HDInsight 클러스터 버전 3.2, 3.3 및 3.4로 테스트되었습니다. 예제에 제공되는 기본값은 HDInsight 3.4 클러스터에 대한 것입니다.

* **SSH 및 SCP 사용 경험**. HDInsight에서 SSH와 SCP를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

    * **Linux, Unix 또는 OS X 클라이언트**: [Linux, OS X 또는 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

    * **Windows 클라이언트**: [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

##프로젝트 만들기

1. 개발 환경의 명령줄에서 프로젝트를 만들 위치(예: `cd code/hdinsight`)로 디렉터리를 변경합니다.

2. Maven과 함께 설치되는 __mvn__ 명령을 사용하여 프로젝트용 스캐폴딩을 생성합니다.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	이 명령은 현재 디렉터리에 새 디렉터리를 만들며, 이름은 __artifactID__ 매개 변수로 지정됩니다(이 예제에서는 **hbaseapp**). 이 디렉터리에는 다음 항목이 포함됩니다.

	* __pom.xml__: [프로젝트 개체 모델(POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.

	* __src__: __main/java/com/microsoft/examples__ 디렉터리를 포함하는 디렉터리이며 여기서 응용 프로그램을 작성합니다.

3. __src/test/java/com/microsoft/examples/apptest.java__ 파일은 이 예제에서 사용되지 않으므로 이 파일을 삭제합니다.

##프로젝트 개체 모델 업데이트

1. __pom.xml__ 파일을 편집하고 `<dependencies>` 섹션 안에 다음 코드를 추가합니다.

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

	이 코드를 통해 Maven은 프로젝트에 __hbase-client__ 버전 __1.1.2__가 필요하다는 것을 인식합니다. 컴파일 시간에 이 파일이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 중앙 리포지토리 검색](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar)을 사용하여 이 종속성에 대한 자세한 정보를 확인할 수 있습니다.

    > [AZURE.IMPORTANT] 버전 번호는 HDInsight 클러스터와 함께 제공되는 HBase 버전과 일치해야 합니다. 다음 표를 사용하여 올바른 버전 번호를 찾으세요.

    | HDInsight 클러스터 버전 | 사용할 HBase 버전 |
    | ----- | ----- |
    | 3\.2 | 0\.98.4-hadoop2 |
    | 3\.3 및 3.4 | 1\.1.2 |

    HDInsight 버전 및 구성 요소에 대한 자세한 내용은 [HDInsight에서 사용할 수 있는 다양한 Hadoop 구성 요소](hdinsight-component-versioning.md)를 참조하세요.

2. HDInsight 3.3 또는 3.4 클러스터를 사용하는 경우 `<dependencies>` 섹션에 다음을 추가해야 합니다.

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    이렇게 하면 Hbase 버전 1.1.x에 필요한 phoenix-core 구성 요소가 로드됩니다.

2. __pom.xml__ 파일에 다음 코드를 추가합니다. 이 코드는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어`</dependencies>`과 `</project>` 사이에 있어야 합니다.

		<build>
		  <sourceDirectory>src</sourceDirectory>
		  <resources>
	        <resource>
	          <directory>${basedir}/conf</directory>
	          <filtering>false</filtering>
	          <includes>
	            <include>hbase-site.xml</include>
	          </includes>
	        </resource>
	      </resources>
		  <plugins>
		    <plugin>
        	  <groupId>org.apache.maven.plugins</groupId>
        	  <artifactId>maven-compiler-plugin</artifactId>
						<version>3.3</version>
        	  <configuration>
          	    <source>1.7</source>
          	    <target>1.7</target>
        	  </configuration>
      		</plugin>
		    <plugin>
		      <groupId>org.apache.maven.plugins</groupId>
		      <artifactId>maven-shade-plugin</artifactId>
		      <version>2.3</version>
		      <configuration>
		        <transformers>
		          <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
	              </transformer>
	            </transformers>
		      </configuration>
		      <executions>
		        <execution>
		          <phase>package</phase>
		          <goals>
		            <goal>shade</goal>
		          </goals>
		        </execution>
		      </executions>
		    </plugin>
		  </plugins>
		</build>

	이 코드는 HBase에 대한 구성 정보를 포함하는 리소스(__conf/hbase-site.xml__)를 구성합니다.

	> [AZURE.NOTE] 또한 코드를 통해 구성 값을 설정할 수도 있습니다. 작업 방법은 뒤에 나오는 __CreateTable__ 예제의 설명을 참조하세요.

	이 예제에서는 [Maven 컴파일러 플러그 인](http://maven.apache.org/plugins/maven-compiler-plugin/) 및 [Maven 음영 플러그 인](http://maven.apache.org/plugins/maven-shade-plugin/)도 구성합니다. 컴파일러 플러그 인은 토폴로지를 컴파일하는 데 사용됩니다. 음영 플러그 인은 Maven으로 빌드된 JAR 패키지에서 라이선스 중복을 방지하는 데 사용됩니다. 이 플러그 인을 사용하는 이유는 중복 라이선스 파일이 HDInsight 클러스터에서 런타임으로 오류를 일으키기 때문입니다. `ApacheLicenseResourceTransformer` 구현에서 maven-shade-plugin을 사용하면 이 오류가 방지됩니다.

	또한 maven-shade-plugin은 응용 프로그램에 필요한 모든 종속성을 포함하는 uber jar(또는 fat jar)도 생성합니다.

3. __pom.xml__ 파일을 저장합니다.

4. __hbaseapp__ 디렉터리에 __conf__라는 새 디렉터리를 만듭니다. HBase에 연결하기 위한 구성 정보를 저장하는 데 사용됩니다.

5. 다음 명령을 사용하여 HDInsight 서버에서 __conf__ 디렉터리로 HBase 구성을 복사합니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME**을 HDInsight 클러스터 이름으로 바꿉니다.

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE] SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##응용 프로그램 만들기

1. __hbaseapp/src/main/java/com/microsoft/examples__ 디렉터리로 이동하여 app.java 파일 이름을 __CreateTable.java__로 바꿉니다.

2. __CreateTable.java__ 파일을 열고 기존 내용을 다음으로 바꿉니다.

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

	이 코드는 __CreateTable__ 클래스이며, __people__이라는 테이블을 만들고 미리 정의된 사용자로 채웁니다.

3. __CreateTable.java__ 파일을 저장합니다.

4. __hbaseapp/src/main/java/com/microsoft/examples__ 디렉터리에서 __SearchByEmail.java__라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.client.ResultScanner;
		import org.apache.hadoop.hbase.client.Result;
		import org.apache.hadoop.hbase.filter.RegexStringComparator;
		import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
		import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.util.GenericOptionsParser;

		public class SearchByEmail {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Use GenericOptionsParser to get only the parameters to the class
		    // and not all the parameters passed (when using WebHCat for example)
		    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
		    if (otherArgs.length != 1) {
		      System.out.println("usage: [regular expression]");
		      System.exit(-1);
		    }

			// Open the table
		    HTable table = new HTable(config, "people");

			// Define the family and qualifiers to be used
		    byte[] contactFamily = Bytes.toBytes("contactinfo");
		    byte[] emailQualifier = Bytes.toBytes("email");
		    byte[] nameFamily = Bytes.toBytes("name");
		    byte[] firstNameQualifier = Bytes.toBytes("first");
		    byte[] lastNameQualifier = Bytes.toBytes("last");

			// Create a new regex filter
		    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
			// Attach the regex filter to a filter
			//   for the email column
		    SingleColumnValueFilter filter = new SingleColumnValueFilter(
		      contactFamily,
		      emailQualifier,
		      CompareOp.EQUAL,
		      emailFilter
		    );

			// Create a scan and set the filter
		    Scan scan = new Scan();
		    scan.setFilter(filter);

			// Get the results
		    ResultScanner results = table.getScanner(scan);
			// Iterate over results and print  values
		    for (Result result : results ) {
		      String id = new String(result.getRow());
		      byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
		      String firstName = new String(firstNameObj);
		      byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
		      String lastName = new String(lastNameObj);
		      System.out.println(firstName + " " + lastName + " - ID: " + id);
			  byte[] emailObj = result.getValue(contactFamily, emailQualifier);
		      String email = new String(emailObj);
			  System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
		    }
		    results.close();
			table.close();
		  }
		}

	__SearchByEmail__ 클래스를 사용하여 메일 주소로 행을 쿼리할 수 있습니다. 정규식 필터를 사용하므로, 이 클래스를 사용할 때 문자열 또는 정규식을 제공할 수 있습니다.

5. __SearchByEmail.java__ 파일을 저장합니다.

6. __hbaseapp/src/main/hava/com/microsoft/examples__ 디렉터리에서 __DeleteTable.java__라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;

		public class DeleteTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);

		    // Disable, and then delete the table
		    admin.disableTable("people");
		    admin.deleteTable("people");
		  }
		}

	이 클래스는 __CreateTable__ 클래스로 생성된 테이블을 비활성화하고 제거하여 이 예제를 정리하는 데 사용됩니다.

7. __DeleteTable.java__ 파일을 저장합니다.

##응용 프로그램 빌드 및 패키지화

2. __hbaseapp__ 디렉터리에서 다음 명령을 사용하여 응용 프로그램을 포함하는 JAR 파일을 빌드합니다.

		mvn clean package

	이 코드는 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 응용 프로그램을 빌드 및 패키지화합니다.

3. 명령이 완료되면 __hbaseapp/target__ 디렉터리에 __hbaseapp-1.0-SNAPSHOT.jar__이라는 파일이 포함됩니다.

	> [AZURE.NOTE] __hbaseapp-1.0-SNAPSHOT.jar__ 파일은 응용 프로그램을 실행하는 데 필요한 모든 종속성을 포함하는 uber jar(fat jar라고도 함)입니다.

##JAR 파일 업로드 및 작업 실행

1. 다음을 사용하여 jar을 HDInsight 클러스터에 업로드합니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME**을 HDInsight 클러스터 이름으로 바꿉니다.

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	그러면 SSH 사용자 계정의 홈 디렉터리에 파일을 업로드합니다.

	> [AZURE.NOTE] SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. SSH를 사용하여 HDInsight 클러스터에 연결합니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME**을 HDInsight 클러스터 이름으로 바꿉니다.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE] SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 연결된 후 다음을 사용하여 Java 응용 프로그램을 사용하는 새 HBase 테이블을 만듭니다.

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	그러면 __people__이라는 새 HBase 테이블이 만들어지고 데이터로 채워집니다.

4. 그런 후, 다음을 사용하여 테이블에 저장된 메일 주소를 검색합니다.

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	다음과 같은 결과가 표시됩니다.

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##테이블 삭제

예제를 완료하면 Azure PowerShell 세션에서 다음 명령을 사용하여 이 예제에 사용된 __people__ 테이블을 삭제합니다.

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=AcomDC_0914_2016-->