<properties 
   pageTitle="Node.js용 Azure SDK를 사용하여 Azure Data Lake 저장소 관리 | Microsoft Azure"
   description="데이터 레이크 저장소 계정 및 파일 시스템을 관리하는 방법을 알아봅니다." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# Node.js용 Azure SDK를 사용하여 Azure 데이터 레이크 저장소 관리

> [AZURE.SELECTOR]
- [포털](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)


Node.js용 Azure SDK는 Azure Data Lake 저장소 계정은 물론 파일 시스템 작업을 관리하는 데 사용할 수 있습니다.

현재는 다음이 지원됩니다.

  *  **Node.js 버전: 0.10.0 이상**
  *  **계정에 대한 REST API 버전: 2015-10-01-preview**
  *  **FileSystem용 REST API 버전: 2015-10-01-preview**

##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## 기능

- 계정 관리: 만들기, 가져오기, 나열, 업데이트 및 삭제
- 파일 시스템 관리: 만들기, 가져오기, 업로드, 추가, 다운로드, 읽기, 삭제, 나열

## 설치 방법

```bash
npm install azure-arm-datalake-store
```

## Azure Active Directory를 사용하여 인증

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Data Lake 분석 클라이언트 만들기

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## Data Lake 저장소 계정 만들기

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## 콘텐츠를 포함하는 파일 만들기
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## 파일 및 폴더 목록 가져오기

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## 참고 항목

- [Node.js용 Microsoft Azure SDK](https://github.com/azure/azure-sdk-for-node)
- [Node.js용 Microsoft Azure SDK - Data Lake 분석 관리](https://www.npmjs.com/package/azure-arm-datalake-analytics)

<!---HONumber=AcomDC_0914_2016-->