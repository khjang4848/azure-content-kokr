<properties
    pageTitle="서비스 버스 큐 시작 | Microsoft Azure"
    description="서비스 버스 메시징에 대한 C# 콘솔 응용 프로그램을 작성하는 방법"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>

# 서비스 버스 큐 시작

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## 수행될 작업

이 자습서에서는 다음이 완료됩니다.

1. Azure 포털을 사용하여 서비스 버스 네임스페이스를 만듭니다.

2. Azure 포털을 사용하여 서비스 버스 메시징 큐를 만듭니다.

3. 메시지를 보내는 콘솔 응용 프로그램을 작성합니다.

4. 메시지를 수신하는 콘솔 응용 프로그램을 작성합니다.

## 필수 조건

1. [Visual Studio 2013 또는 Visual Studio 2015](http://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2015를 사용합니다.

2. Azure 구독.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1\. Azure 포털을 사용하여 네임스페이스 만들기

서비스 버스 네임스페이스를 이미 만든 경우 [Azure 포털 을 사용하여 큐 만들기](#2-create-a-queue-using-the-azure-portal) 섹션으로 이동합니다.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2\. Azure 포털을 사용하여 큐 만들기

서비스 버스 큐를 이미 만든 경우 [큐에 메시지 보내기](#3-send-messages-to-the-queue) 섹션으로 이동합니다.

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3\. 큐에 메시지 보내기

큐에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

### 콘솔 응용 프로그램 만들기

1. Visual Studio를 시작하고 새 콘솔 응용 프로그램을 만듭니다.

### 서비스 버스 NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. **찾아보기**를 클릭한 다음 "Microsoft Azure 서비스 버스"를 검색하고 **Microsoft Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

    ![NuGet 패키지 선택][nuget-pkg]

### 큐에 메시지를 보내는 코드 작성

1. Program.cs 파일 위쪽에 다음 using 문을 추가합니다.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. `Main` 메서드에 다음 코드를 추가하고 네임스페이스를 만들 때 가져온 연결 문자열로 **connectionString** 변수를 설정하고 큐를 만들 때 사용된 큐 이름으로 **queueName**을 설정합니다.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Program.cs는 다음과 같아야 합니다.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. 프로그램을 실행하고 Azure 포털을 확인합니다. 네임스페이스 **개요** 블레이드에서 큐의 이름을 클릭합니다. **활성 메시지 수** 값이 1이어야 합니다.
    
      ![메시지 수][queue-message]
    
## 4\. 큐에서 메시지 받기

1. 새 콘솔 응용 프로그램을 만들고 이전의 응용 프로그램 보내기와 유사하게 서비스 버스 NuGet 패키지에 참조를 추가합니다.

2. Program.cs 파일 위쪽에 다음 `using` 문을 추가합니다.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. `Main` 메서드에 다음 코드를 추가하고 네임스페이스를 만들 때 가져온 연결 문자열로 **connectionString** 변수를 설정하고 큐를 만들 때 사용된 큐 이름으로 **queueName**을 설정합니다.

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

	Program.cs 파일은 다음과 같아야 합니다.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. 프로그램을 실행하고 포털을 확인합니다. **큐 길이** 값은 이제 0이어야 합니다.

    ![큐 길이][queue-message-receive]
  
축하합니다. 이제 큐를 만들고 메시지를 보내고 메시지를 받았습니다.

## 다음 단계

Azure 서비스 버스 메시징의 더 많은 고급 기능 중 일부를 보여 주는 [샘플이 있는 GitHub 리포지토리](https://github.com/Azure-Samples/azure-servicebus-messaging-samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->