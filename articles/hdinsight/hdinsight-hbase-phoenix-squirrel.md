<properties 
   pageTitle="HDinsight에서 Apache Phoenix 및 SQuirreL 사용 | Microsoft Azure" 
   description="HDInsight에서 Apache Phoenix를 사용하는 방법 및 워크스테이션에서 SQuirreL을 설치 및 구성하여 HDInsight에서 HBase 클러스터에 연결하는 방법에 대해 알아봅니다." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# HDinsight에서 Windows 기반 HBase 클러스터와 함께 Apache Phoenix 및 SQuirreL 사용  

HDInsight에서 [Apache Phoenix](http://phoenix.apache.org/)를 사용하는 방법 및 워크스테이션에서 SQuirrel을 설치 및 구성하여 HDInsight에서 HBase 클러스터에 연결하는 방법에 대해 알아봅니다. Phoenix에 대한 자세한 내용은 [15분 이내의 Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)를 참조하세요. Phoenix 문법은 [피닉스 문법](http://phoenix.apache.org/language/index.html)을 참조하세요.

>[AZURE.NOTE] HDInsight의 Phoenix 버전 정보는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.
>
> 이 문서에 있는 정보는 Windows 기반 HDInsight 클러스터에 지정됩니다. Linux 기반 HDInsight에서 Phoenix 사용 방법에 대한 자세한 내용은 [HDInsight의 Linux 기반 HBase 클러스터와 함께 Apache Phoenix 사용](hdinsight-hbase-phoenix-squirrel-linux.md)을 참조하세요.

##SQLLine 사용
[SQLLine](http://sqlline.sourceforge.net/)은 SQL을 실행하는 명령줄 유틸리티입니다.

###필수 조건
SQLLine을 시작하려면 다음이 있어야 합니다.

- **HDInsight의 HBase 클러스터**. HBase 클러스터 프로비전에 대한 자세한 내용은 [HDInsight에서 Apache HBase 시작][hdinsight-hbase-get-started]을 참조하세요.
- **원격 데스크톱 프로토콜을 통해 HBase 클러스터에 연결**. 자세한 내용은 [Azure 클래식 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리][hdinsight-manage-portal]를 참조하세요.

**호스트 이름을 확인하려면**

1. 데스크톱에서 **Hadoop 명령줄**을 엽니다.
2. 다음 명령을 실행하여 DNS 접미사를 가져옵니다.

		ipconfig

	**연결별 DNS 접미사**를 적어 둡니다. 예를 들면 *myhbasecluster.f5.internal.cloudapp.net*과 같습니다. HBase 클러스터에 연결할 때 FQDN을 사용하여 Zookeeper 중 하나에 연결해야 합니다. 각 HDInsight 클러스터에는 3개의 Zookeeper, *zookeeper0*, *zookeeper1* 및 *zookeeper2*가 있습니다. FQDN은 *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*과 같습니다.

**SQLLine을 사용하려면**

1. 데스크톱에서 **Hadoop 명령줄**을 엽니다.
2. 다음 명령을 실행하여 SQLLine을 엽니다.

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	이 샘플에 사용되는 명령:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

자세한 내용은 [SQLLine 설명서](http://sqlline.sourceforge.net/#manual) 및 [Phoenix 문법](http://phoenix.apache.org/language/index.html)을 참조하세요.


















##SQuirrel 사용

[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/)는 JDBC 호환 데이터베이스를 보고, 테이블에서 데이터를 찾고, SQL 명령을 실행할 수 있는 그래픽 Java 프로그램입니다. HDInsight에서 Apache 피닉스에 연결하는데 사용할 수 있습니다.

이 섹션에서는 워크스테이션에서 SQuirreL을 설치 및 구성하여 HDInsight에서 VPN을 통해 HBase 클러스터에 연결하는 방법에 대해 알아봅니다.

###필수 조건

다음 절차를 수행하기 전에 다음이 있어야 합니다.

- DNS 가상 컴퓨터를 사용하여 Azure 가상 네트워크에 배포한 HBase 클러스터. 자세한 내용은 [Azure 가상 네트워크에 HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet]을 참조하세요.

	>[AZURE.IMPORTANT] 가상 네트워크에 DNS 서버를 설치해야 합니다. 자세한 내용은 [두 Azure 가상 네트워크 간 DNS 구성](hdinsight-hbase-geo-replication-configure-DNS.md)을 참조하세요.

- HBase 클러스터 연결별 DNS 접미사를 가져옵니다. 이렇게 하려면 클러스터에 RDP를 연결하고 IPConfig를 실행합니다. DNS 접미사는 다음과 유사합니다.

		myhbase.b7.internal.cloudapp.net
- 워크스테이션에서 [Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx)을 다운로드하여 설치합니다. 인증서를 만들려면 패키지의 makecert가 필요합니다.
- 워크스테이션에서 [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)을 다운로드하여 설치합니다. SQuirreL SQL Client 버전 3.0 이상에는 JRE 버전 1.6 이상이 필요합니다.


###Azure 가상 네트워크에 대한 지점 및 사이트 간 VPN 연결 구성

지점 및 사이트 간 VPN 연결을 구성하는 단계는 3단계로 구성됩니다.

1. [가상 네트워크 및 동적 라우팅 게이트웨이 구성](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [인증서 만들기](#Create-your-certificates)
3. [VPN 클라이언트 구성](#Configure-your-VPN-client)

자세한 내용은 [Azure 가상 네트워크에 대한 지점 및 사이트 간 VPN 연결 구성](../vpn-gateway/vpn-gateway-point-to-site-create.md)을 참조하세요.

#### 가상 네트워크 및 동적 라우팅 게이트웨이 구성

Azure 가상 네트워크에서 HBase 클러스터를 프로비전했는지 확인합니다(이 섹션의 필수 조건 참조). 다음 단계에서는 지점 및 사이트 간 연결을 구성합니다.

**지점 및 사이트 간 연결을 구성하려면**

1. [Azure 클래식 포털][azure-portal]에 로그인합니다.
2. 왼쪽에서 **네트워크**를 클릭합니다.
3. 만든 가상 네트워크를 클릭합니다([Azure 가상 네트워크에 HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet] 참조).
4. 위쪽에서 **구성**을 클릭합니다.
5. **지점 및 사이트 간 연결** 섹션에서 **지점 및 사이트 간 연결 구성**을 선택합니다.
6. **시작 IP** 및 **CIDR**을 구성하여 연결된 경우 VPN 클라이언트에서 IP 주소를 받을 IP 주소 범위를 지정합니다. 범위는 온-프레미스 네트워크 및 연결할 Azure 가상 네트워크에 있는 범위와 겹칠 수 없습니다. 예를 들어 가상 네트워크에 대해 10.0.0.0/20을 선택한 경우 클라이언트 주소 공간은 10.1.0.0/24를 선택할 수 있습니다. 자세한 내용은 [지점 및 사이트 간 연결][vnet-point-to-site-connectivity] 페이지를 참조하세요.
7. 가상 네트워크 주소 공간 섹션에서 **게이트웨이 서브넷 추가**를 클릭합니다.
7. 페이지 아래쪽에서 **저장**을 클릭합니다.
8. **예**를 클릭하여 변경 내용을 확인합니다. 시스템에서 변경을 완료할 때까지 기다린 후 다음 절차를 진행합니다.


**동적 라우팅 게이트웨이를 만들려면**

1. Azure 클래식 포털의 페이지 위쪽에서 **대시보드**를 클릭합니다.
2. 페이지 아래쪽에서 **게이트웨이 만들기**를 클릭합니다.
3. **예**를 클릭하여 확인합니다. 게이트웨이가 만들어질 때까지 기다립니다.
4. 위쪽에서 **대시보드**를 클릭합니다. 가상 네트워크에 대한 시각적 다이어그램이 표시됩니다.

	![Azure 가상 네트워크 지점 및 사이트 간 가상 다이어그램][img-vnet-diagram]

	다이어그램에 클라이언트 연결이 0개인 것으로 표시됩니다. 가상 네트워크에 연결하면 이 숫자가 1로 업데이트됩니다.

#### 인증서 만들기

X.509 인증서를 만드는 한 가지 방법은 [Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx)과 함께 제공되는 인증서 만들기 도구(makecert.exe)를 사용하는 것입니다.


**자체 서명된 루트 인증서를 만들려면**

1. 워크스테이션에서 명령 프롬프트 창을 엽니다.
2. Visual Studio Tools 폴더로 이동합니다.
3. 아래 예제의 다음 명령은 워크스테이션의 개인 인증서 저장소에 루트 인증서를 만들고 설치하며, 나중에 Azure 클래식 포털에 업로드할 해당.cer 파일을 만듭니다.

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	.cer 파일을 저장할 디렉터리로 변경합니다. 여기서 HBaseVnetVPNRootCertificate는 인증서에 사용할 이름입니다.

	명령 프롬프트를 닫지 마세요. 다음 절차에서 필요합니다.

	>[AZURE.NOTE] 클라이언트 인증서를 생성할 루트 인증서를 만들었기 때문에 이 인증서를 해당 개인 키와 함께 내보내고 복구 가능한 안전한 위치에 저장할 수 있습니다.

**클라이언트 인증서를 만들려면**

- 동일한 명령 프롬프트(루트 인증서를 만든 것과 동일한 컴퓨터에 있어야 하며, 루트 인증서에서 클라이언트 인증서를 생성해야 함)에서 다음 명령을 실행합니다.

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate는 루트 인증서 이름입니다. 루트 인증서 이름과 일치해야 합니다.

	루트 인증서 및 클라이언트 인증서는 컴퓨터의 개인 인증서 저장소에 저장됩니다. Certmgr.msc를 사용하여 확인합니다.

	![Azure 가상 네트워크 지점 대 사이트 vpn 인증서][img-certificate]

	클라이언트 인증서는 가상 네트워크에 연결하려는 각 컴퓨터에 설치되어야 합니다. 가상 네트워크에 연결 하려는 각 컴퓨터에 대해 고유한 클라이언트 인증서를 만드는 것이 좋습니다. 클라이언트 인증서를 내보내려면 certmgr.msc를 사용합니다.

**Azure 클래식 포털에 루트 인증서를 업로드하려면**

1. Azure 클래식 포털의 왼쪽에서 **네트워크**를 클릭합니다.
2. HBase 클러스터가 배포된 가상 네트워크를 클릭합니다.
3. 위쪽에서 **인증서**를 클릭합니다.
4. 아래쪽에서 **업로드**를 클릭하고 마지막 이전 절차에서 만든 루트 인증서 파일을 지정합니다. 인증서를 가져올 때까지 기다립니다.
5. 위쪽에서 **대시보드**를 클릭합니다. 가상 다이어그램에 상태가 표시됩니다.


#### VPN 클라이언트 구성



**클라이언트 VPN 패키지를 다운로드하여 설치하려면**

1. 가상 네트워크의 대시보드 페이지에 있는 간략 상태 섹션에서 워크스테이션 OS 버전에 따라 **64비트 클라이언트 VPN 패키지 다운로드** 또는 **32비트 클라이언트 VPN 패키지 다운로드**를 클릭합니다.
2. **실행**을 클릭하여 패키지를 설치합니다.
3. 보안 프롬프트에서 **추가 정보**를 클릭한 다음 **실행**을 클릭합니다.
4. **예**를 두 번 클릭합니다.

**VPN에 연결**

1. 워크스테이션의 바탕 화면에서 작업 표시줄에 있는 네트워크 아이콘을 클릭합니다. 가상 네트워크 이름과 함께 VPN 연결이 표시됩니다.
2. VPN 연결 이름을 클릭합니다.
3. **Connect**를 클릭합니다.

**VPN 연결 및 도메인 이름 확인을 테스트하려면**

- 워크스테이션에서 명령 프롬프트를 열고 다음 이름 중 하나를 ping합니다(HBase 클러스터의 DNS 접미사는 myhbase.b7.internal.cloudapp.net).

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###워크스테이션에서 SQuirreL 설치 및 구성

**SQuirreL을 설치하려면**

1. [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation)에서 SQuirreL SQL Client jar 파일을 다운로드합니다.
2. jar 파일을 엽니다/실행합니다. [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)가 필요합니다.
3. **다음**를 두 번 클릭합니다.
4. 쓰기 권한이 있는 경로를 지정하고 **다음**을 클릭합니다.
	>[AZURE.NOTE] 기본 설치 폴더는 C:\\Program Files\\squirrel-sql-3.6 폴더에 있습니다. 이 경로에 쓰려면 설치 관리자에 관리자 권한이 부여되어야 합니다. 관리자 권한으로 명령 프롬프트를 열고 Java의 bin 폴더로 이동한 후 다음을 실행합니다.
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. **확인**을 클릭하여 대상 디렉터리 만들기를 확인합니다.
6. 기본 설정은 기본 및 표준 패키지를 설치하는 것입니다. **Next**를 클릭합니다.
7. **다음**을 두 번 클릭한 후 **완료**를 클릭합니다.


**Phoenix 드라이버를 설치하려면**

Phoenix 드라이버 jar 파일은 HBase 클러스터에 있습니다. 경로는 버전에 따라 다음과 유사합니다.

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
워크스테이션의 [SQuirrel 설치 폴더]/lib 경로 아래에 복사해야 합니다. 가장 쉬운 방법은 RDP를 통해 클러스터에 연결한 후 파일 복사/붙여넣기(Ctrl+C 및 Ctrl+V)를 사용하여 워크스테이션에 복사하는 것입니다.

**SQuirreL에 Phoenix 드라이버를 추가하려면**

1. 워크스테이션에서 SQuirreL SQL Client를 엽니다.
2. 왼쪽에서 **드라이버** 탭을 클릭합니다.
2. **드라이버** 메뉴에서 **새 드라이버**를 클릭합니다.
3. 다음 정보를 입력합니다.

	- **이름**: Phoenix
	- **예제 URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **클래스 이름**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING] 예제 URL에는 모두 소문자를 사용합니다. 그 중 하나가 다운된 경우 전체 zookeeper 쿼럼을 사용할 수 있습니다. 호스트 이름은 zookeeper0, zookeeper1 및 zookeeper2입니다.

	![HDInsight HBase Phoenix SQuirreL 드라이버][img-squirrel-driver]
4. **확인**을 클릭합니다.

**HBase 클러스터에 대한 별칭을 만들려면**

1. SQuirreL에서 왼쪽의 **별칭** 탭을 클릭합니다.
2. **별칭** 메뉴에서 **새 별칭**을 클릭합니다.
3. 다음 정보를 입력합니다.

	- **이름**: HBase 클러스터의 이름 또는 사용자가 원하는 이름
	- **드라이버**: Phoenix. 마지막 절차에서 만든 드라이버 이름과 일치해야 합니다.
	- **URL**: 드라이버 구성에서 복사됩니다. 모두 소문자를 사용해야 합니다.
	- **사용자 이름**: 모든 텍스트일 수 있습니다. 여기 VPN 연결을 사용하기 때문에 사용자 이름은 전혀 사용되지 않습니다.
	- **암호**: 모든 텍스트일 수 있습니다.

	![HDInsight HBase Phoenix SQuirreL 드라이버][img-squirrel-alias]
4. **테스트**를 클릭합니다.
5. **연결**을 클릭합니다. 연결된 경우 SQuirreL은 다음과 같습니다.

	![HBase Phoenix SQuirreL][img-squirrel]

**테스트를 실행하려면**

1. **개체** 탭 오른쪽에 있는 **SQL** 탭을 클릭합니다.
2. 다음 코드를 복사하여 붙여넣습니다.

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. 실행 단추를 클릭합니다.

	![HBase Phoenix SQuirreL][img-squirrel-sql]
4. **개체** 탭으로 다시 전환합니다.
5. 별칭 이름을 확장한 다음 **테이블**을 확장합니다. 새 테이블이 나열됩니다.
 
##다음 단계
이 문서에서는 HDInsight에서 Apache Phoenix를 사용하는 방법에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight HBase 개요][hdinsight-hbase-overview]\: HBase는 비구조적/반구조적 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.
- [Azure 가상 네트워크에서 HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet]\: 가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다.
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md): 두 Azure 데이터 센터에서 HBase 복제를 구성하는 방법에 대해 알아봅니다.
- [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석][hbase-twitter-sentiment]\: HDInsight의 Hadoop 클러스터에서 HBase를 사용하여 빅데이터에 대한 실시간 [데이터 분석](http://en.wikipedia.org/wiki/Sentiment_analysis)을 수행하는 방법에 대해 알아봅니다.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0914_2016-->