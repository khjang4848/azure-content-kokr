<properties
	pageTitle="Azure AD Java 시작 | Microsoft Azure"
	description="API 액세스를 위해 사용자를 로그인하는 Java 명령줄 앱을 빌드하는 방법"
	services="active-directory"
	documentationCenter="java"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>


# Azure AD에서 API를 액세스하기 위해 Java 명령줄 앱 사용

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD를 사용하면 단순하고 간편하게 웹앱의 ID 관리를 아웃소싱하고 몇 개의 코드 줄만으로 단일 로그인 및 로그아웃을 제공할 수 있습니다. Java 웹앱에서는 Microsoft에서 구현한 커뮤니티 기반 ADAL4J를 사용하여 이 작업을 수행할 수 있습니다.

  다음의 경우 ADAL4J를 사용합니다.
- ID 공급자로 Azure AD를 사용하여 사용자를 앱에 로그인합니다.
- 사용자에 대한 일부 정보를 표시합니다.
- 앱에서 사용자를 로그아웃합니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. 앱을 설정하여 ADAL4J 라이브러리를 사용합니다.
3. ADAL4J 라이브러리를 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행
4. 사용자에 대한 데이터를 출력합니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip)하세요. 응용 프로그램을 등록할 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## 1\. Azure AD에 응용 프로그램을 등록합니다.
앱에서 사용자를 인증할 수 있게 하려면 먼저 새 응용 프로그램을 테넌트에 등록해야 합니다.

- Azure 관리 포털에 로그인합니다.
- 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
- 응용 프로그램을 등록할 테넌트를 선택합니다.
- **응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 추가를 클릭합니다.
- 프롬프트에 따라 새 **웹 응용 프로그램 및/또는 WebAPI**를 만듭니다.
    - 응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    - **로그온 URL**은 앱의 기본 URL입니다. 기본값은 `http://localhost:8080/adal4jsample/`입니다.
    - **앱 ID URI**는 응용 프로그램의 고유 식별자입니다. 규칙은 `https://<tenant-domain>/<app-name>`(예: `http://localhost:8080/adal4jsample/`)을 사용하는 것입니다.
- 등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 구성 탭에서 복사해둡니다.

앱의 포털에서 응용 프로그램에 **응용 프로그램 암호**를 만든 후 복사해둡니다. 곧 필요합니다.


## 2\. Maven을 사용하여 ADAL4J 라이브러리 및 필수 구성 요소를 사용하도록 앱을 설정합니다.
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 ADAL4J를 구성합니다. ADAL4J은 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

-	프로젝트의 루트 디렉터리에서 `pom.xml`을 열거나 만들고 `// TODO: provide dependencies for Maven`를 찾아서 다음으로 바꿉니다.

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>public-client-adal4j-sample</artifactId>
	<packaging>jar</packaging>
	<version>0.0.1-SNAPSHOT</version>
	<name>public-client-adal4j-sample</name>
	<url>http://maven.apache.org</url>
	<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>com.microsoft.azure</groupId>
			<artifactId>adal4j</artifactId>
			<version>1.1.2</version>
		</dependency>
		<dependency>
			<groupId>com.nimbusds</groupId>
			<artifactId>oauth2-oidc-sdk</artifactId>
			<version>4.5</version>
		</dependency>
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.5</version>
		</dependency>
</dependencies>
	<build>
		<finalName>public-client-adal4j-sample</finalName>
		<plugins>
		        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>install</id>
						<phase>install</phase>
						<goals>
							<goal>sources</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.5</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
		</plugins>
	</build>

</project>


```



## 3\. java PublicClient 파일을 만듭니다.

앞서 설명한 대로 Graph API를 사용하여 로그인한 사용자에 대한 데이터를 가져옵니다. 쉽게 수행할 수 있도록 **디렉터리 개체**를 나타내는 파일 및 **사용자**를 나타내는 개별 파일 모두를 만들어서 Java의 OO 패턴을 사용할 수 있도록 합니다.

1. DirectoryObject에 대한 기본 데이터를 저장하는 데 사용하는 `DirectoryObject.java`라는 파일을 만듭니다.(나중에 수행할 수 있는 다른 Graph 쿼리에 이 파일을 자유롭게 사용할 수 있음) 아래에서 잘라내고 붙여 넣을 수 있습니다.

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##샘플 컴파일 및 실행

루트 디렉터리로 다시 변경하고 다음 명령을 실행하여 `maven`을 사용하여 모은 샘플을 빌드합니다. 여기에는 종속성에 대해 작성한 `pom.xml` 파일이 사용됩니다.

`$ mvn package`

이제 `/targets` 디렉터리에 `adal4jsample.war` 파일이 있습니다. Tomcat 컨테이너에서 해당 파일을 배포하고 URL를 방문할 수 있습니다.

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
최신 Tomcat 서버를 사용하여 WAR를 쉽게 배포할 수 있습니다. `http://localhost:8080/manager/`로 이동하여 '`adal4jsample.war` 파일을 업로드하는 지침에 따릅니다. 올바른 끝점을 사용하여 자동으로 배포합니다.

##다음 단계

축하합니다. 이제 사용자를 인증하고 OAuth 2.0을 사용하여 Web API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있는 Java 응용 프로그램이 작성되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다.

참조를 위해 완료된 샘플(사용자 구성 값 제외)이 [여기에 .zip으로 제공](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

<!---HONumber=AcomDC_0921_2016-->