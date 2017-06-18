<properties
	pageTitle="비대화형 인증 .NET HDInsight 응용 프로그램 만들기 | Microsoft Azure"
	description="비대화형 인증 .NET HDInsight 응용 프로그램을 만드는 방법을 알아봅니다."
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>

# 비대화형 인증 .NET HDInsight 응용 프로그램 만들기

응용 프로그램의 고유한 ID(비대화형) 또는 응용 프로그램에 로그인한 사용자의 ID(대화형)로 .NET Azure HDInsight 응용 프로그램을 실행할 수 있습니다. 대화형 응용 프로그램 샘플의 경우 [HDInsight.NET SDK를 사용하여 Hive/Pig/Sqoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)을 참조하세요. 이 문서에서는 비대화형 인증 .NET 응용 프로그램을 만들어 Azure HDInsight에 연결하고 Hive 작업을 제출하는 방법을 보여 줍니다.

.NET 응용 프로그램에서 다음이 필요합니다.

- Azure 구독 테넌트 ID
- Azure Directory 응용 프로그램 클라이언트 ID
- Azure Directory 응용 프로그램 비밀 키

기본 프로세스에는 다음 단계가 포함됩니다.

2. Azure Directory 응용 프로그램을 만듭니다.
2. AD 응용 프로그램에 역할을 할당합니다.
3. 클라이언트 응용 프로그램을 개발합니다.


##필수 조건

- HDInsight 클러스터. [시작 자습서](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)에 설명된 지침을 사용하여 만들 수 있습니다.




## Active Directory 응용 프로그램 만들기 
Active Directory 응용 프로그램을 만들 때 실제로 응용 프로그램과 서비스 주체를 만듭니다. 응용 프로그램의 ID로 응용 프로그램을 실행할 수 있습니다.

현재는 새 Active Directory 응용 프로그램을 만들려면 Azure 클래식 포털을 사용해야 합니다. 이 기능은 이후 릴리스에서 Azure 포털에 추가될 예정입니다. Azure PowerShell 또는 Azure CLI를 통해 이러한 단계를 수행할 수도 있습니다. 서비스 주체와 함께 PowerShell 또는 CLI를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 서비스 주체 인증](../resource-group-authenticate-service-principal.md)을 참조하세요.

**Azure Directory 응용 프로그램을 만들려면**

1.	[Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
2.	왼쪽 창에서 **Active Directory**를 선택합니다.

    ![Azure 클래식 포털 Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	새 응용 프로그램을 만드는 데 사용할 디렉터리를 선택합니다. 기존 디렉터리여야 합니다.
4.	기존 응용 프로그램을 나열하려면 위쪽에서 **응용 프로그램**을 클릭합니다.
5.	새 응용 프로그램을 추가하려면 아래쪽에서 **추가**를 클릭합니다.
6.	**이름**을 입력하고 **웹 응용 프로그램 및/또는 Web API**를 선택한 후 **다음**을 클릭합니다.

    ![새 Azure Active Directory 응용 프로그램](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	**로그온 URL**과 **앱 ID URI**를 입력합니다. **로그온 URL**의 경우 응용 프로그램을 설명하는 웹 사이트에 대한 URI를 제공합니다. 웹 사이트의 존재 여부는 확인되지 않습니다. 앱 ID URI의 경우 응용 프로그램을 식별하는 URI를 제공합니다. 그런 다음 **완료**를 클릭합니다. 응용 프로그램을 만드는 데 몇 분 정도 걸립니다. 응용 프로그램이 만들어지면 포털에 새 응용 프로그램의 을 만들면 포털에 새 응용 프로그램의 한 눈에 보기 페이지가 표시됩니다. 포털을 닫지 마세요.

    ![새 Azure Active Directory 응용 프로그램 속성](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**응용 프로그램 클라이언트 ID와 비밀 키를 가져오려면**

1.	새로 만든 AD 응용 프로그램 페이지의 위쪽 메뉴에서 **구성**을 클릭합니다.
2.	**클라이언트 ID**의 복사본을 만듭니다. .NET 응용 프로그램에 필요합니다.
3.	**키** 아래에서 **기간 선택** 드롭다운을 클릭하고 **1년** 또는 **2년**을 선택합니다. 구성을 저장할 때까지 키 값이 표시되지 않습니다.
4.	페이지 아래쪽에서 **저장**을 클릭합니다. 비밀 키가 표시되면 키의 복사본을 만듭니다. .NET 응용 프로그램에 필요합니다.

##역할에 AD 응용 프로그램 할당

작업 수행 권한을 부여하려면 응용 프로그램을 [역할](../active-directory/role-based-access-built-in-roles.md)에 할당해야 합니다. 구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다. 이 자습서에서는 리소스 그룹 수준에서 범위를 설정합니다. Azure 클래식 포털은 리소스 그룹을 지원하지 않으므로 Azure 포털에서 이 부분을 수행해야 합니다.

**AD 응용 프로그램에 소유자 역할을 추가하려면**

1.	[Azure 포털](https://portal.azure.com)에 로그인합니다.
2.	왼쪽 창에서 **리소스 그룹**을 클릭합니다.
3.	이 자습서의 뒷부분에서 Hive 쿼리를 실행할 HDInsight 클러스터가 포함된 리소스 그룹을 클릭합니다. 너무 많은 리소스 그룹이 있는 경우 필터를 사용할 수 있습니다.
4.	클러스터 블레이드에서 **액세스**를 클릭합니다.

    ![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	**사용자** 블레이드에서 **추가**를 클릭합니다.
6.	지침에 따라 마지막 절차에서 만든 AD 응용 프로그램에 **소유자**를 추가합니다. 성공적으로 완료하면 소유자 역할이 있는 사용자가 블레이드에 나열된 응용 프로그램이 표시됩니다.


##HDInsight 클라이언트 응용 프로그램 개발

[HDInsight에서 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)의 지침에 따라 C#.net 콘솔 응용 프로그램을 만듭니다. 그런 다음 GetTokenCloudCredentials 메서드를 다음으로 바꿉니다.

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

PowerShell을 통해 테넌트 ID를 검색하려면

    Get-AzureRmSubscription

또는 Azure CLI:

    azure account show --json

      
## 참고 항목

- [HDInsight에서 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
- [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](../resource-group-create-service-principal-portal.md)
- [Azure Resource Manager를 사용하여 서비스 주체 인증](../resource-group-authenticate-service-principal.md)
- [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0914_2016-->