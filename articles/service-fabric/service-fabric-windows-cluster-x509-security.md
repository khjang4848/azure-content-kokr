<properties
   pageTitle="보안 개인 클러스터에 연결 | Microsoft Azure"
   description="이 문서에서는 클라이언트와 클러스터 사이 뿐만 아니라 독립 실행형 클러스터 또는 개인 클러스터 내에서 통신을 보호하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# X.509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호

이 문서에서는 독립 실행형 Windows 클러스터의 다양한 노드 간에 통신을 보호하는 방법 뿐만 아니라 X.509 인증서를 사용하여 이 클러스터에 연결된 클라이언트를 인증하는 방법을 설명합니다. 이렇게 하면 권한이 있는 사용자만 클러스터 및 배포된 응용 프로그램에 액세스할 수 있으며 관리 작업을 수행할 수 있습니다. 인증서 보안은 클러스터가 만들어지기 전에 클러스터에서 사용되어야 합니다.

노드 간 보안, 클라이언트-노드 보안 및 역할 기반 액세스 제어와 같은 클러스터 보안에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

## 어떤 인증서가 필요한가요?

처음에 클러스터의 노드 중 하나에 [독립 실행형 클러스터 패키지를 다운로드](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)합니다. 다운로드한 패키지에서 **ClusterConfig.X509.MultiMachine.json** 파일을 찾을 수 있습니다. 파일을 열고 **속성** 섹션의 **보안**을 위한 섹션을 검토합니다.

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

이 섹션에서는 독립 실행형 Windows 클러스터를 보호하기 위한 인증서를 설명합니다. 인증서 기반 보안을 활성화하려면 **ClusterCredentialType** 및 **ServerCredentialType** 값을 *X509*로 설정합니다.

>[AZURE.NOTE] [지문](https://en.wikipedia.org/wiki/Public_key_fingerprint)은 인증서의 기본 ID입니다. [인증서의 지문을 검색하는 방법](https://msdn.microsoft.com/library/ms734695.aspx)을 참고하여 만든 인증서의 지문을 확인합니다.

다음 테이블에서는 클러스터 설치에 필요한 인증서를 나열합니다.

|**CertificateInformation 설정**|**설명**|
|-----------------------|--------------------------|
|ClusterCertificate|이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다. 업그레이드에 두 개의 다른 인증서, 기본 및 보조 인증서를 사용할 수 있습니다. **지문** 섹션에서 기본 인증서의 지문 및 **ThumbprintSecondary** 변수에서 보조 인증서의 지문을 설정합니다.|
|ServerCertificate|이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. 편의상 *ClusterCertificate* 및 *ServerCertificate*에 동일한 인증서를 사용하도록 선택할 수 있습니다. 업그레이드에 두 개의 다른 서버 인증서, 기본 및 보조 인증서를 사용할 수 있습니다. **지문** 섹션에서 기본 인증서의 지문 및 **ThumbprintSecondary** 변수에서 보조 인증서의 지문을 설정합니다. |
|ClientCertificateThumbprints|인증된 클라이언트에 설치하려는 인증서의 집합입니다. 클러스터에 대한 액세스를 허용하려는 컴퓨터에 다양한 클라이언트 인증서가 설치되었을 수도 있습니다. **CertificateThumbprint** 변수에서 각 인증서의 지문을 설정합니다. **IsAdmin**을 *true*로 설정한 경우 이 인증서가 설치된 클라이언트는 클러스터에서 관리자 관리 작업을 수행할 수 있습니다. **IsAdmin**이 *false*로 설정된 경우 이 인증서가 있는 클라이언트는 사용자 액세스 권한(일반적으로 읽기 전용)에 대해 허용되는 작업을 수행할 수만 있습니다. 역할에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)](service-fabric-cluster-security.md/#role-based-access-control-rbac)를 읽어보세요. |
|ClientCertificateCommonNames|**CertificateCommonName**에 대한 첫 번째 클라이언트 인증서의 일반 이름을 설정합니다. **CertificateIssuerThumbprint**는 이 인증서의 발급자 지문입니다. [인증서 사용](https://msdn.microsoft.com/library/ms731899.aspx)을 참고하여 일반 이름 및 발급자에 대해 자세히 알아 봅니다.|

다음은 클러스터, 서버 및 클라이언트 인증서가 제공된 예제 클러스터 구성입니다.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
		"metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
      		"metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
      		"metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
		"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
		}
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## X.509 인증서 획득
클러스터 내에서 통신을 보호하려면 클러스터 노드에 대한 X.509 인증서를 먼저 가져와야 합니다. 또한 권한이 있는 컴퓨터/사용자에게 이 클러스터에 연결을 제한하려면 클라이언트 컴퓨터에 인증서를 가져와서 설치해야 합니다.

프로덕션 워크로드를 실행하는 클러스터의 경우 클러스터 보호를 위해 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)로 서명된 X.509 인증서를 사용해야 합니다. 이러한 인증서를 얻는 방법에 대한 자세한 내용은 [방법: 인증서 가져오기](http://msdn.microsoft.com/library/aa702761.aspx)를 참조하세요.

테스트 목적으로 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다.

## 선택 사항: 자체 서명된 인증서 만들기
제대로 보호할 수 있는 자체 서명된 인증서를 만드는 한 가지 방법은 *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\ClusterSetup\\Secure* 디렉터리의 서비스 패브릭 SDK 폴더에 있는 *CertSetup.ps1* 스크립트를 사용하는 것입니다. 이 파일을 편집하고 이를 사용하여 적절한 이름으로 인증서를 만듭니다.

이제 암호로 보호된 PFX 파일에 인증서를 내보냅니다. 먼저 인증서의 지문을 가져와야 합니다. certmgr.exe 응용 프로그램을 실행합니다. **로컬 컴퓨터/개인** 폴더로 이동하고 방금 만든 인증서를 찾습니다. 인증서를 두 번 클릭하여 열고 *세부 정보* 탭을 선택하고 *지문* 필드로 스크롤합니다. 공백을 제거하여 아래 PowerShell 명령에 지문 값을 복사합니다. *$pswd* 값을 적절한 보안 암호로 변경하여 보호하고 PowerShell을 실행합니다.

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

컴퓨터에 설치된 인증서의 세부 정보를 보기 위해 다음 PowerShell 명령을 실행할 수 있습니다.

```
$cert = Get-Item Cert:\LocalMachine\My<Thumbprint>
Write-Host $cert.ToString($true)
```

또는 Azure 구독을 보유한 경우 [X.509 인증서 획득](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts) 섹션에 따라 인증서를 만들 수 있습니다.

## 인증서를 설치합니다.
인증서를 만든 후에 클러스터 노드에 설치할 수 있습니다. 노드에 최신 Windows PowerShell 3.x가 설치되어 있어야 합니다. 클러스터와 서버 인증서 및 모든 보조 인증서의 경우 각 노드에서 이러한 단계를 반복해야 합니다.

1. .pfx 파일을 노드에 복사합니다.
2. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 입력합니다. *$pswd*를 이 인증서를 만드는 데 사용한 암호로 바꿉니다. *$PfxFilePath*를 이 노드에 복사된 .pfx의 전체 경로로 바꿉니다.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. 다음으로 네트워크 서비스 계정에서 실행되는 서비스 패브릭 프로세스가 다음 스크립트를 실행하여 사용할 수 있도록 이 인증서에 액세스 제어를 설정해야 합니다. 서비스 계정에 대해 인증서의 지문 및 "NETWORK SERVICE"를 제공합니다. certmgr.exe 도구를 사용하고 인증서의 개인 키 관리를 확인하여 인증서의 ACL이 올바른지 확인할 수 있습니다.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. 각 서버 인증서에 대해 위의 단계를 반복합니다. 또한 이러한 단계를 사용하여 클러스터에 대한 액세스를 허용하려는 컴퓨터에 클라이언트 인증서를 설치할 수 있습니다.

## 보안 클러스터 만들기
**ClusterConfig.X509.MultiMachine.json** 파일의 **보안** 섹션을 구성한 후에 [클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md#createcluster) 섹션을 진행하여 노드를 구성하고 독립 실행형 클러스터를 만들 수 있습니다. 클러스터를 만드는 동안 **ClusterConfig.X509.MultiMachine.json** 파일을 사용하도록 합니다. 예를 들어, 명령은 다음과 같을 수 있습니다.

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

보안 독립 실행형 Windows 클러스터를 성공적으로 실행하고 여기에 연결할 인증된 클라이언트를 설정했다면 [PowerShell을 사용하여 보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) 섹션을 따라 연결합니다. 예:

```
Connect-ServiceFabricCluster -ConnectionEndpoint 10.7.0.4:19000 -KeepAliveIntervalInSec 10 -X509Credential -ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 -FindType FindByThumbprint -FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 -StoreLocation CurrentUser -StoreName My
```

클러스터의 컴퓨터 중 하나에 로그온한 경우 로컬로 설치된 인증서가 이미 있기 때문에 간단히 Powershell 명령을 실행하여 클러스터에 연결하고 노드 목록을 표시할 수 있습니다.

```
Connect-ServiceFabricCluster
Get-ServiceFabricNode
```
클러스터를 제거하려면 다음 명령을 호출합니다.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

<!---HONumber=AcomDC_0921_2016-->