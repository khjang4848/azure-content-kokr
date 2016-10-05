<properties
   pageTitle="서비스 패브릭 서비스 끝점 지정 | Microsoft Azure"
   description="HTTPS 끝점을 설정하는 방법을 포함하여 서비스 매니페스트에서 끝점 리소스를 설명하는 방법"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# 서비스 매니페스트에서 리소스 지정

## 개요

서비스 매니페스트를 사용하면 컴파일된 코드를 변경하지 않고도 서비스에서 사용하는 리소스를 선언/변경할 수 있게 해줍니다. Azure 서비스 패브릭은 서비스에 대한 끝점 리소스 구성을 지원합니다. 서비스 매니페스트에 지정된 리소스에 대한 액세스는 응용 프로그램 매니페스트의 SecurityGroup을 통해 제어할 수 있습니다. 리소스를 선언하면 배포 시에 이러한 리소스를 변경할 수 있으며 즉, 서비스에 새로운 구성 메커니즘을 도입하지 않아도 됩니다. ServiceManifest.xml 파일에 대한 스키마 정의는 서비스 패브릭 SDK 및 도구와 함께 *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*에 설치됩니다.

## 끝점

서비스 매니페스트에 끝점 리소스가 정의되면 서비스 패브릭에서는 포트가 명시적으로 지정되지 않을 경우 예약된 응용 프로그램 포트 범위에 포함되는 포트를 할당합니다. 예를 들어 이 단락 다음에 제공된 매니페스트 코드 조각에 지정된 끝점 *ServiceEndpoint1*을 보세요. 또한 서비스에서 리소스의 특정 포트를 요청할 수도 있습니다. 다른 클러스터 노드에서 실행되는 서비스 복제본을 다른 포트 번호에 할당할 수 있으며, 같은 노드에서 실행되는 서비스의 복제본은 포트를 공유합니다. 그러면 서비스 복제본은 복제 및 클라이언트 요청의 수신 대기를 위해 필요한 경우 이러한 포트를 사용할 수 있습니다.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

구성 패키지 설정 파일(settings.xml)의 끝점 참조에 대한 자세한 내용은 [상태 저장 Reliable Services 구성](service-fabric-reliable-services-configuration.md)을 참조하세요.

## 예: 서비스에 대한 HTTP 끝점 지정

다음 서비스 매니페스트는 &lt;Resources&gt; 요소에서 1 TCP 끝점 리소스 및 2 HTTP 끝점 리소스를 정의합니다.

HTTP 끝점은 서비스 패브릭에 의해 자동으로 ACL 처리됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## 예: 서비스에 대한 HTTPS 끝점 지정

HTTPS 프로토콜은 서버 인증을 제공하며, 클라이언트-서버 통신을 암호화하는 데에도 사용됩니다. 서비스 패브릭 서비스에서 HTTPS를 사용할 수 있도록 설정하려면 위의 *ServiceEndpoint3* 끝점과 같이 서비스 매니페스트의 *리소스 -> 끝점 -> 끝점* 섹션에서 프로토콜을 지정합니다.

>[AZURE.NOTE] 서비스의 프로토콜은 주요 변경 내용으로 간주되어 응용 프로그램을 업그레이드하는 동안 변경할 수 없습니다.


HTTPS에 대해 설정해야 하는 예제 ApplicationManifest는 다음과 같습니다. 인증서에 대한 지문을 제공해야 합니다. EndpointRef는 HTTPS 프로토콜을 설정하는 ServiceManifest의 EndpointResource에 대한 참조입니다. 둘 이상의 Endpointcertificate를 추가할 수 있습니다.

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

<!---HONumber=AcomDC_0921_2016-->