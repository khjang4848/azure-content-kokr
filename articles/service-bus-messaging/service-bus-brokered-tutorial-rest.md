<properties 
    pageTitle="서비스 버스 조정된 메시징 REST 자습서 | Microsoft Azure"
    description="조정된 메시징 REST 자습서"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# 서비스 버스 조정된 메시징 REST 자습서

이 자습서에서는 기본 REST 기반 Azure Service Bus 큐 및 항목/구독을 만드는 방법을 보여 줍니다.

## 네임스페이스 만들기

첫 단계는 서비스 네임스페이스를 만들고 [SAS(공유 액세스 서명)](../service-bus/service-bus-sas-overview.md) 키를 확보합니다. 네임스페이스는 서비스 버스를 통해 노출되는 각 응용 프로그램에 대한 응용 프로그램 경계를 제공합니다. SAS 키는 서비스 네임스페이스가 만들어질 때 시스템에 의해 자동으로 생성됩니다. 서비스 네임스페이스 및 SAS 키 조합은 서비스 버스에 자격 증명을 제공하여 응용 프로그램에 대한 액세스를 인증합니다.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 콘솔 클라이언트 만들기

서비스 버스 큐를 사용하면 메시지를 선입 선출 큐에 저장할 수 있습니다. 항목 및 구독은 게시/구독 패턴을 구현합니다. 즉 항목을 만들고 해당 항목과 연결된 하나 이상의 구독을 만듭니다. 항목으로 메시지를 보내면 해당 항목의 구독자에게 즉시 전송됩니다.

이 자습서의 코드는 다음과 같은 작업을 수행합니다.

- 네임스페이스 및 SAS([공유 액세스 서명](../service-bus/service-bus-sas-overview.md)) 키를 사용하여 Service Bus 네임스페이스 리소스에 대한 액세스 권한을 얻습니다.

- 큐를 만들고, 큐에 메시지를 보내며, 큐로부터 메시지를 읽습니다.

- 항목과, 해당 항목에 대한 구독을 만들고 구독으로부터 메시지를 보내고 읽습니다.

- Service Bus에서 구독 규칙을 포함하여 모든 큐, 항목 및 구독 정보를 검색합니다.

- 큐, 항목 및 구독 리소스를 삭제합니다.

서비스가 REST 스타일 웹 서비스이므로 전체 교환이 문자열과 관련이 있기 때문에 특수 형식이 관련되지 않습니다. 즉, Visual Studio 프로젝트가 Service Bus 라이브러리를 참조하지 않아야 합니다.

1단계에서 네임스페이스와 자격 증명을 구한 후 기본 Visual Studio 콘솔 응용 프로그램을 만듭니다.

### 콘솔 응용 프로그램 만들기

1. **시작** 메뉴에서 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 클릭하여 Visual Studio를 관리자 권한으로 시작합니다.

1. 새 콘솔 응용 프로그램 프로젝트를 만듭니다. **파일** 메뉴를 클릭하고 **새로 만들기**, **프로젝트**를 차례로 클릭합니다. **새 프로젝트** 대화 상자에서 **Visual C#**을 클릭하고(**Visual C#**이 보이지 않으면 **다른 언어** 아래 확인) **콘솔 응용 프로그램** 서식 파일을 선택하여 이름을 **Microsoft.ServiceBus.Samples**로 지정합니다. 기본 위치를 사용합니다. **확인**을 클릭하여 프로젝트를 만듭니다.

1. Program.cs에서 `using` 문이 다음과 같이 표시되는지 확인합니다.

	```
	using System;
	using System.Globalization;
	using System.IO;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Xml;
	```

1. 필요한 경우 프로그램의 네임스페이스 이름을 Visual Studio 기본값에서 `Microsoft.ServiceBus.Samples`로 변경합니다.

1. `Program` 클래스 내에 다음 전역 변수를 추가합니다.
	
	```
	static string serviceNamespace;
	static string baseAddress;
	static string token;
	const string sbHostName = "servicebus.windows.net";
	```

1. `Main()` 안에 다음 코드를 붙여넣습니다.

	```
	Console.Write("Enter your service namespace: ");
	serviceNamespace = Console.ReadLine();
	
	Console.Write("Enter your SAS key: ");
	string SASKey = Console.ReadLine();
	
	baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
	try
	{
	    token = GetSASToken("RootManageSharedAccessKey", SASKey);
	
	    string queueName = "Queue" + Guid.NewGuid().ToString();
	
	    // Create and put a message in the queue
	    CreateQueue(queueName, token);
	    SendMessage(queueName, "msg1");
	    string msg = ReceiveAndDeleteMessage(queueName);
	
	    string topicName = "Topic" + Guid.NewGuid().ToString();
	    string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
	    CreateTopic(topicName);
	    CreateSubscription(topicName, subscriptionName);
	    SendMessage(topicName, "msg2");
	
	    Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
	
	    // Get an Atom feed with all the queues in the namespace
	    Console.WriteLine(GetResources("$Resources/Queues"));
	
	    // Get an Atom feed with all the topics in the namespace
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the subscriptions for the topic we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions"));
	
	    // Get an Atom feed with all the rules for the topic and subscription we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
	
	    // Delete the queue we created
	    DeleteResource(queueName);
	
	    // Delete the topic we created
	    DeleteResource(topicName);
	
	    // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Queues"));
	}
	catch (WebException we)
	{
	    using (HttpWebResponse response = we.Response as HttpWebResponse)
	    {
	        if (response != null)
	        {
	            Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
	        }
	        else
	        {
	            Console.WriteLine(we.ToString());
	        }
	    }
	}
	
	Console.WriteLine("\nPress ENTER to exit.");
	Console.ReadLine();
	```

## 관리 자격 증명 만들기

다음 단계는 이전 단계에서 입력한 네임스페이스와 SAS 키를 처리하고 SAS 토큰을 반환하는 메서드를 작성하는 것입니다. 이 예제는 1시간 동안 유효한 SAS 토큰을 만듭니다.

### GetSASToken() 메서드 만들기

`Main()` 메서드 뒤의 `Program` 클래스에 다음 코드를 붙여넣습니다.

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## 큐 만들기

다음 단계에서는 REST 스타일 HTTP PUT 명령을 사용하여 큐를 만드는 메서드를 작성합니다.

이전 단계에서 추가한 `GetSASToken()` 코드 바로 뒤에 다음 코드를 붙여넣습니다.

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## 큐에 메시지 보내기

이 단계에서는 이전 단계에서 만든 큐로 메시지를 보내는 REST-style HTTP POST 명령을 사용하는 메서드를 추가합니다.

1. 이전 단계에서 추가한 `CreateQueue()` 코드 바로 뒤에 다음 코드를 붙여넣습니다.

	```
	// Sends a message to the "queueName" queue, given the name and the value to enqueue
	// Uses an HTTP POST request.
	private static void SendMessage(string queueName, string body)
	{
	    string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
	    Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
	    WebClient webClient = new WebClient();
	    webClient.Headers[HttpRequestHeader.Authorization] = token;
	
	    webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
	}
	```

1. 표준 broker 저장 메시지 속성은 `BrokerProperties` HTTP 헤더에 배치됩니다. 브로커 속성은 JSON 형식으로 직렬화되어야 합니다. **TimeToLive** 값을 30초로 지정하고 메시지 레이블 “M1”을 메시지에 추가하려면 앞의 예제에 표시된 `webClient.UploadData()` 호출의 바로 앞에 다음 코드를 추가합니다.

	```
	// Add brokered message properties "TimeToLive" and "Label"
	webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
	```

	조정된 메시지 속성이 추가됩니다. 따라서 보내기 요청에서는 요청의 일부인 조정된 메시지 속성을 모두 지원하는 API 버전을 지정해야 합니다. 지정된 API 버전이 조정된 메시지 속성을 지원하지 않으면 해당 속성이 무시됩니다.

1. 사용자 지정 메시지 속성은 키-값 쌍의 집합으로 정의됩니다. 각 사용자 지정 속성은 자체 TPPT 헤더에 저장됩니다. 사용자 지정 속성 "우선 순위" 및 "고객"을 추가하려면 앞의 예제에 표시된 `webClient.UploadData()` 호출 바로 앞에 다음 코드를 추가합니다.

	```
	// Add custom properties "Priority" and "Customer".
	webClient.Headers.Add("Priority", "High");
	webClient.Headers.Add("Customer", "12345");
	```

## 큐로부터 메시지 수신 및 삭제

다음 단계는 큐로부터 메시지를 수신하고 삭제하는 REST 스타일 HTTP DELETE를 사용하는 메서드를 추가하는 것입니다.

이전 단계에서 추가한 `SendMessage()` 코드 바로 뒤에 다음 코드를 붙여넣습니다.

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## 항목 및 구독 만들기

다음 단계는 REST 스타일 HTTP PUT 명령을 사용하여 항목을 만드는 메서드를 작성하는 것입니다. 그런 다음 해당 항목에 대한 구독을 만드는 메서드를 작성합니다.

### 토픽 만들기

이전 단계에서 추가한 `ReceiveAndDeleteMessage()` 코드 바로 뒤에 다음 코드를 붙여넣습니다.

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### 구독 만들기

다음 코드는 이전 단계에서 만든 항목에 대한 구독을 만듭니다. 다음 코드를 `CreateTopic()` 정의 바로 뒤에 추가합니다.

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## 메시지 리소스 검색

이 단계에서는 메시지 속성을 검색 한 다음 이전 단계에서 만든 메시징 리소스를 삭제하는 코드를 추가합니다.

### 지정된 리소스로 Atom 피드 검색

이전 단계에서 추가한 `CreateSubscription()` 메서드 바로 뒤에 다음 코드를 추가합니다.

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### 메시징 엔터티 삭제

이전 단계에서 추가한 코드 바로 뒤에 다음 코드를 추가합니다.

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### Atom 피드 포맷

`GetResources()` 메서드에는 검색한 Atom 피드를 다시 포맷하여 가독성을 높이는 `FormatXml()` 메서드에 대한 호출이 포함되어 있습니다. 다음은 `FormatXml()` 정의로, 이 코드를 이전 섹션에서 추가한 `DeleteResource()` 코드 바로 뒤에 추가합니다.

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## 응용 프로그램 빌드 및 실행

이제 응용 프로그램을 빌드 및 실행할 수 있습니다. Visual Studio의 **빌드** 메뉴에서 **솔루션 빌드**를 클릭하거나 **Ctrl+Shift+B** 키를 누릅니다.

### 응용 프로그램 실행

오류가 없는 경우 F5 키를 눌러 응용 프로그램을 실행합니다. 메시지가 표시되면 첫 번째 단계에서 얻은 네임스페이스, SAS 키 이름 및 SAS 키 값을 입력합니다.

### 예

다음 예제는 이 자습서의 모든 단계 완료 후 표시되는 전체 코드입니다.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## 다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [서비스 버스 메시징 개요](service-bus-messaging-overview.md)
- [Azure 서비스 버스 기본 사항](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [서비스 버스 릴레이 REST 자습서](../service-bus-relay/service-bus-relay-rest-tutorial.md)

<!---HONumber=AcomDC_0928_2016-->