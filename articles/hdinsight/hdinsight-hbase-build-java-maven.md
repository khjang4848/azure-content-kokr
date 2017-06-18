<properties
pageTitle="Maven을 사용하여 HBase 응용 프로그램을 빌드하고 Windows 기반 HDInsight에 배포 | Microsoft Azure"
description="Apache Maven을 사용하여 Java 기반 Apache HBase 응용 프로그램을 빌드한 다음 Windows 기반 Azure HDInsight 클러스터에 배포하는 방법에 대해 알아봅니다."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="07/25/2016"
ms.author="larryfr"/>

#Maven을 사용하여 Windows 기반 HDInsight(Hadoop)에서 HBase를 사용하는 Java 응용 프로그램 빌드

Apache Maven을 사용하여 Java로 [Apache HBase](http://hbase.apache.org/) 응용 프로그램을 만들어 빌드하는 방법을 알아봅니다. 그런 다음 Azure HDInsight(Hadoop)에서 응용 프로그램을 사용합니다.

[Maven](http://maven.apache.org/)은 Java 프로젝트용 소프트웨어, 문서화 및 보고를 빌드할 수 있는 소프트웨어 프로젝트 관리 및 종합 도구입니다. 이 문서에서는 Maven을 사용하여 Azure HDInsight 클러스터에서 HBase 테이블을 만들고, 쿼리하고, 삭제하는 기본 Java 응용 프로그램을 만드는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서의 단계는 Windows 기반 HDInsight 클러스터를 사용하고 있다고 가정합니다. Linux 기반 HDInsight 클러스터 사용에 대한 자세한 내용은 [Maven을 사용하여 Linux 기반 HDInsight에서 HBase를 사용하는 Java 응용 프로그램 빌드](hdinsight-hbase-build-java-maven-linux.md)를 참조하세요.

##요구 사항

* [Java 플랫폼 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 이상

* [Maven](http://maven.apache.org/)

* [Windows 기반 HDInsight 클러스터 및 HBase](hdinsight-hbase-get-started.md#create-hbase-cluster)

    > [AZURE.NOTE] 이 문서의 단계는 HDInsight 클러스터 버전 3.2 및 3.3으로 테스트되었습니다. 예제에 제공되는 기본값은 HDInsight 3.3 클러스터에 대한 것입니다.

##프로젝트 만들기

1. 개발 환경의 명령줄에서 프로젝트를 만들 위치(예: `cd code\hdinsight`)로 디렉터리를 변경합니다.

2. Maven과 함께 설치되는 __mvn__ 명령을 사용하여 프로젝트용 스캐폴딩을 생성합니다.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    이 명령은 현재 디렉터리에 새 디렉터리를 만들며, 이름은 __artifactID__ 매개 변수로 지정됩니다(이 예제에서는 **hbaseapp**). 이 디렉터리에는 다음 항목이 포함됩니다.

    * __pom.xml__: [프로젝트 개체 모델(POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.

    * __src__: __main\\java\\com\\microsoft\\examples__ 디렉터리를 포함하는 디렉터리이며 여기서 응용 프로그램을 작성합니다.

3. __src\\test\\java\\com\\microsoft\\examples\\apptest.java__ 파일은 이 예제에서 사용되지 않으므로 이 파일을 삭제합니다.

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
    | 3\.3 | 1\.1.2 |

    HDInsight 버전 및 구성 요소에 대한 자세한 내용은 [HDInsight에서 사용할 수 있는 다양한 Hadoop 구성 요소](hdinsight-component-versioning.md)를 참조하세요.

2. HDInsight 3.3 클러스터를 사용하는 경우 `<dependencies>` 섹션에 다음을 추가해야 합니다.

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    이렇게 하면 Hbase 버전 1.1.x에서 사용되는 phoenix-core 구성 요소가 로드됩니다.

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

    이 코드는 HBase에 대한 구성 정보를 포함하는 리소스(__conf\\hbase-site.xml__)를 구성합니다.

    > [AZURE.NOTE] 또한 코드를 통해 구성 값을 설정할 수도 있습니다. 작업 방법은 뒤에 나오는 __CreateTable__ 예제의 설명을 참조하세요.

    이 예제에서는 [Maven 컴파일러 플러그 인](http://maven.apache.org/plugins/maven-compiler-plugin/) 및 [Maven 음영 플러그 인](http://maven.apache.org/plugins/maven-shade-plugin/)도 구성합니다. 컴파일러 플러그 인은 토폴로지를 컴파일하는 데 사용됩니다. 음영 플러그 인은 Maven으로 빌드된 JAR 패키지에서 라이선스 중복을 방지하는 데 사용됩니다. 이 플러그 인을 사용하는 이유는 중복 라이선스 파일이 HDInsight 클러스터에서 런타임으로 오류를 일으키기 때문입니다. `ApacheLicenseResourceTransformer` 구현에서 maven-shade-plugin을 사용하면 이 오류가 방지됩니다.

    또한 maven-shade-plugin은 응용 프로그램에 필요한 모든 종속성을 포함하는 uber jar(또는 fat jar)도 생성합니다.

3. __pom.xml__ 파일을 저장합니다.

4. __hbaseapp__ 디렉터리에 __conf__라는 새 디렉터리를 만듭니다. __conf__ 디렉터리에 __hbase-site.xml__이라는 새 파일을 만들고 다음을 그 내용으로 사용합니다.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    이 파일은 HDInsight 클러스터용 HBase 구성을 로드하는 데 사용됩니다.

    > [AZURE.NOTE] 이 파일은 최소 크기의 hbase-site.xml 파일로, HDInsight 클러스터용 완전한 최소 설정을 포함합니다.

3. __hbase-site.xml__ 파일을 저장합니다.

##응용 프로그램 만들기

1. __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ 디렉터리로 이동하여 app.java 파일 이름을 __CreateTable.java__로 바꿉니다.

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ 디렉터리에서 __SearchByEmail.java__라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

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

6. __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__ 디렉터리에서 __DeleteTable.java__라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

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

1. 명령 프롬프트를 열고 __hbaseapp__ 디렉터리로 이동합니다.

2. 다음 명령을 사용하여 응용 프로그램을 포함하는 JAR 파일을 빌드합니다.

        mvn clean package

    이 코드는 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 응용 프로그램을 빌드 및 패키지화합니다.

3. 명령이 완료되면 __hbaseapp\\target__ 디렉터리에 __hbaseapp-1.0-SNAPSHOT.jar__이라는 파일이 포함됩니다.

    > [AZURE.NOTE] __hbaseapp-1.0-SNAPSHOT.jar__ 파일은 응용 프로그램을 실행하는 데 필요한 모든 종속성을 포함하는 uber jar(fat jar라고도 함)입니다.

##JAR 파일 업로드 및 작업 시작

[HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md)에서 설명한 대로 HDInsight 클러스터에 파일을 업로드하는 방법은 많습니다. 다음 단계에서는 Azure PowerShell을 사용합니다.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Azure PowerShell을 설치 및 구성한 후 __hbase-runner.psm1__이라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    이 파일에는 다음 두 모듈이 포함됩니다.

    * __Add-HDInsightFile__ - HDInsight에 파일을 업로드하는 데 사용합니다.

    * __Start-HBaseExample__ - 이전에 생성한 클래스를 실행하는 데 사용합니다.

2. __hbase-runner.psm1__ 파일을 저장합니다.

3. 새 Azure PowerShell 창을 열고, __hbaseapp__ 디렉터리로 이동한 후 다음 명령을 실행합니다.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    이전에 만든 __hbase-runner.psm1__ 파일의 위치로 경로를 변경합니다. 그러면 이 Azure PowerShell 세션에 대한 모듈이 등록됩니다.

2. 다음 명령을 사용하여 HDInsight 클러스터에 __hbaseapp-1.0-SNAPSHOT.jar__을 업로드합니다.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    __hdinsightclustername__을 HDInsight 클러스터의 이름으로 바꿉니다. 이 명령은 HDInsight 클러스터용 기본 저장소의 __example/jars__ 위치로 __hbaseapp-1.0-SNAPSHOT.jar__을 업로드합니다.

3. 파일을 업로드한 후 다음 코드를 사용하여 __hbaseapp__으로 새 테이블을 만듭니다.

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    __hdinsightclustername__을 HDInsight 클러스터의 이름으로 바꿉니다.

    이 명령은 HDInsight 클러스터에 __people__이라는 새 테이블을 만듭니다. 이 명령은 콘솔 창에 출력을 표시하지 않습니다.

2. 테이블에서 항목을 검색하려면 다음 명령을 사용합니다.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    __hdinsightclustername__을 HDInsight 클러스터의 이름으로 바꿉니다.

    이 명령은 **SearchByEmail** 클래스를 사용하여 __contactinformation__ 열 패밀리 __email__ 열에서 __contoso.com__ 문자열이 포함된 모든 행을 검색합니다. 다음과 같은 결과가 표시됩니다.

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    `-emailRegex` 값에 __fabrikam.com__을 사용하면 메일 필드에 __fabrikam.com__을 포함하는 사용자가 반환됩니다. 이 검색은 정규식 기반 필터를 사용하여 구현되므로, __^r__과 같은 정규식을 입력할 수 있습니다. 이 정규식은 메일이 'r' 문자로 시작하는 항목을 반환합니다.

##테이블 삭제

예제를 완료하면 Azure PowerShell 세션에서 다음 명령을 사용하여 이 예제에 사용된 __people__ 테이블을 삭제합니다.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

__hdinsightclustername__을 HDInsight 클러스터의 이름으로 바꿉니다.

##문제 해결

###Start-HBaseExample을 사용할 경우 결과가 없거나 예기치 않은 결과가 표시됨

`-showErr` 매개 변수를 사용하여 작업을 실행하는 동안 생성된 표준 오류(STDERR)을 확인합니다.

<!---HONumber=AcomDC_0914_2016-->