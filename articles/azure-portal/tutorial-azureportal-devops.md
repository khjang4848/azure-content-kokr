<properties
	pageTitle="자습서: Azure 포털을 사용한 DevOps | Microsoft Azure"
	description="Azure 포털에서 다양한 DevOps 워크플로에 대해 알아봅니다."
	services="azure-portal"
	documentationCenter=""
	authors="mlearned"
	manager="douge"
	editor="mlearned"/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/05/2016"
	ms.author="mlearned"/>

# 자습서: Azure 포털을 사용한 DevOps

Azure 플랫폼은 유연한 DevOps 워크플로를 담고 있습니다. 이 자습서에서는 Azure 포털의 기능을 활용하여 실행 중인 응용 프로그램을 개발, 테스트, 배포, 문제 해결, 모니터링 및 관리하는 방법을 알아봅니다. 이 자습서는 다음에 중점을 둡니다.

1.  웹앱 만들기 및 지속적인 배포 사용

2.  앱 개발 및 테스트

3.  앱 모니터링 및 문제 해결

4.  일반 응용 프로그램 관리 작업

## 웹앱 만들기 및 지속적인 배포 사용

[Azure 앱 서비스](https://azure.microsoft.com/services/app-service/)를 사용하여 웹앱을 만들고 이 자습서의 뒷부분에서 사용할 수 있습니다. 실행 중인 Azure 환경에 소스 코드 리포지토리의 지속적인 배포를 처음부터 사용할 수 있습니다.

1.  Azure 포털에 로그인합니다.

2.  **앱 서비스** > **추가 아이콘**을 선택하고 이름을 입력하며 구독을 선택한 다음 새 리소스 그룹을 만들어서 서비스에 대한 컨테이너로 사용합니다.

    리소스 그룹을 사용하면 [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/)를 통한 단일 그룹으로 청구, 배포 및 모니터링 등 솔루션의 다양한 측면을 관리할 수 있습니다.

	![image1][image1]


3.  앱 서비스는 몇 분 후에 생성됩니다. 포털에서 서비스에 대한 다양한 메뉴 옵션을 탐색하는 데 몇 분이 걸립니다.

	![image2][image2]

4.  URL을 클릭합니다. 도구 및 리포지토리에 대해 사용할 수 있는 선택은 다양합니다. 또한 .NET, Java, Ruby 등 선택한 언어와 프레임워크를 사용할 수 있습니다.

	![image3][image3]

3.  Azure 포털에서 연속 배포는 몇 가지 간단한 단계를 수행해야 하는 쉬운 프로세스입니다. Azure 포털의 방금 만든 앱 서비스에 대한 아이콘에서 설정을 선택합니다.

    ![image4][image4]

    오른쪽에 열리는 블레이드에서 게시 섹션으로 스크롤합니다.

    ![image5][image5]

4.  다음으로 앱에 연속 배포를 사용하도록 설정하는 일부 설정을 구성합니다. 배포 원본을 클릭한 다음 원본 선택을 클릭합니다. 리포지토리 원본에 다양한 옵션이 있습니다.

	![image6][image6]

1.  이 예제에서는 GitHub를 선택합니다. 원하는 경우 사용자가 선택한 리포지토리를 선택하고 권한 부여 자격 증명을 설정합니다.

    ![image7][image7]

2.  리포지토리에 권한을 부여한 후에 배포할 프로젝트 및 분기를 선택할 수 있습니다. 여러 가상 샘플 예제가 아래에 나열됩니다.

    ![image8][image8]

3.  프로젝트 및 분기를 선택하면 확인을 클릭합니다. 배포 알림이 표시되기 시작해야 합니다.

    ![image9][image9]

4.  Github로 다시 이동하여 Azure와 원본 제어 리포지토리를 통합하도록 만든 웹후크를 확인합니다. Azure 포털을 사용하면 간단한 단계를 통해 Github을 사용하여 통합할 수 있습니다.

    ![image10][image10]

5.  연속 배포를 보여주려면 리포지토리에 일부 콘텐츠를 신속하게 추가합니다. 간단한 예제의 경우 Github 리포지토리에 샘플 텍스트 파일을 추가합니다. 앱 서비스로 .NET, Ruby, Python 또는 기타 다른 유형의 응용 프로그램을 자유롭게 사용합니다. 텍스트 파일, ASP.NET MVC, Java 또는 Ruby 응용 프로그램을 선택한 리포지토리에 추가해도 됩니다.

    ![image11][image11]

6.  리포지토리에 변경 내용을 커밋한 후에 포털 알림 영역에서 새 배포 시작을 확인합니다. 리포지토리에 커밋한 후에 변경 내용을 신속하게 표시하지 않으면 동기화를 클릭합니다.

    ![image12][image12]

7.  이 시점에서 앱 서비스에 대한 페이지를 사용해보고 로드하는 경우 403 오류가 나타날 수 있습니다. 이 예제에서는 index.htm 또는 default.html과 같은 파일 등 페이지에 일반적인 기본 문서가 설정되지 않았기 때문입니다. Azure 포털의 도구를 사용하여 이 문제를 신속하게 해결할 수 있습니다. Azure 포털에서 설정 > 응용 프로그램 설정을 선택합니다.

	 ![image13][image13]

8.  응용 프로그램 설정을 위한 블레이드가 열립니다. "SamplePage.html" 페이지의 이름을 입력하고 저장을 클릭합니다. 다른 설정을 탐색하는 데 몇 분이 걸립니다.

	![image14][image14]

9.  필요에 따라 브라우저 URL을 새로 고쳐서 필요한 변경 내용을 확인할 수 있습니다. 이 경우에 간단한 텍스트가 페이지를 채웁니다. 리포지토리에 추가 변경 사항이 있을 때마다 새 자동 배포가 발생합니다.

    ![image15][image15]

    Azure 포털을 사용하여 연속 배포를 사용하면 쉽습니다. 더 복잡한 릴리스 파이프라인을 구축하고 기존 원본 제어와 연속 통합 시스템을 사용하여 다른 여러 가지 기술을 사용하여 Azure에 배포할 수도 있습니다(예: 자동화된 구축 및 릴리스 관리 시스템 활용).

## 앱 개발 및 테스트

다음으로, 코드 기본을 변경하고 이러한 변경 내용을 신속하게 배포합니다. 또한 웹앱에 대한 일부 성능 테스트를 설정합니다.

1.  Azure 포털의 탐색 창에서 앱 서비스를 선택하고 해당 앱 서비스를 찾습니다.

    ![image16][image16]

2.  도구를 클릭합니다.

    ![image17][image17]

3.  도구에서 개발 범주를 확인합니다. 여러 가지 유용한 도구를 사용하여 Azure 포털에서 나가지 않고 앱으로 작업할 수 있습니다. 콘솔을 클릭합니다.

    ![image18][image18]

4.  콘솔 창에서 앱에 대한 라이브 명령을 실행할 수 있습니다. dir 명령을 입력하고 Enter 키를 누릅니다. 상승된 권한이 필요한 명령은 작동하지 않습니다.

    ![image19][image19]

5.  개발 범주로 다시 이동하고 Visual Studio Online을 선택합니다. 참고: Visual Studio Online은 이제 Visual Studio Team Services로 이름을 지정합니다.

    ![image20][image20]

6.  앱에 대한 브라우저 내 편집 환경을 설정합니다.

    ![image21][image21]

7.  앱에 웹 확장을 설치합니다. 확장은 기능을 쉽고 빠르게 Azure의 앱에 추가합니다. 기타 확장 형식 중 일부를 다음 스크린샷에서 사용할 수 있습니다.

    ![image22][image22]

8.  Visual Studio Online 확장을 설치하면 이동을 클릭합니다.

    ![image23][image23]

9.  브라우저에서 직접 개발 IDE를 표시한 위치에 브라우저 탭이 열립니다. 아래 환경은 Chrome에 있습니다.

    ![image24][image24]

10. 파일 편집 ,파일 및 폴더 추가 및 라이브 사이트에서 콘텐츠를 다운로드 등 여러 가지 작업을 수행할 수 있습니다. SamplePage.html 파일에 빠른 편집을 사용합니다.

    ![image25][image25]

11. 몇 분 내에 변경 내용은 자동으로 저장됩니다. 페이지로 다시 이동하면 변경 내용을 볼 수 있습니다. 다음과 같은 라이브 편집이 프로덕션 환경에 적합하지 않다는 점을 염두에 둡니다. 그러나 도구를 사용하면 개발 및 테스트 환경을 쉽고 빠르게 변경할 수 있습니다.

    ![image26][image26]

    ![image27][image27]

12. 도구 블레이드로 다시 이동하고 개발 범주에서 성능 테스트를 클릭합니다.

    ![image28][image28]

13. 팀 서비스 계정을 설정해야 합니다. 자세한 내용을 보려면 [팀 서비스 계정 만들기](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)를 참조하세요.

14. 새로 만들기를 클릭하여 성능 테스트를 만듭니다.

    ![image29][image29]

    다양한 값을 구성하고 대화 상자 맨 아래에서 테스트 실행을 클릭하여 성능 테스트를 시작합니다.

    ![image30][image30]

	![image31][image31]

1.  테스트가 실행되기 시작하면 상태를 모니터링할 수 있습니다.

    ![image32][image32]

    테스트가 완료되면 결과를 클릭하여 자세한 정보를 표시합니다.

    ![image33][image33]

2.  이 예제에서는 제한된 데이터를 분석하도록 작은 테스트를 실행하기도 하지만 다양한 메트릭을 확인할 뿐만 아니라 이 보기에서 테스트를 다시 실행할 수 있습니다. Azure 포털을 통해 웹 성능 테스트를 쉽게 만들고, 실행 및 분석할 수 있습니다. 아래 스크린샷은 성능 데이터를 표시합니다.

    ![image34][image34]

    ![image35][image35]

    ![image36][image36]

## 앱 모니터링 및 문제 해결

Azure는 실행 중인 응용 프로그램을 모니터링하고 문제를 해결하는 다양한 기능을 제공합니다.

1.  Azure 포털에서 웹앱에 대한 도구를 선택합니다.

    ![image37][image37]

2.  문제 해결 범주에서 사용하는 도구를 다양하게 선택하여 실행 중인 앱과 관련된 잠재적인 문제를 해결합니다. HTTP 라이브 트래픽 모니터링, 자체 치유 사용, 로그 보기 등의 작업을 수행할 수 있습니다.

    ![image38][image38]

3.  사이트 메트릭을 선택하여 몇 가지 HTTP 코드를 신속하게 확인합니다.

    ![image39][image39]

4.  서비스로서의 진단을 선택합니다. 응용 프로그램 형식을 선택한 다음 실행을 선택합니다.

    ![image40][image40]

    컬렉션을 시작합니다.

    ![image41][image41]

5.  적절한 로그를 선택하여 잠재적인 문제를 진단할 수 있습니다. 로깅을 사용하도록 설정하여 HTTP 로그 등 사용 가능한 데이터 옵션을 모두 확인해야 합니다.

    ![image42][image42]

    메모리 덤프 파일을 클릭하여 잠재적인 문제를 찾는 데 도움이 되는 DebugDiag 분석 보고서를 다운로드하고 분석할 수 있습니다.

    ![image43][image43]

6.  더 많은 데이터를 보려면 추가 로깅을 사용해야 합니다. Azure 포털에서 웹앱으로 이동하고 설정을 선택합니다.

    ![image44][image44]

7.  기능 범주까지 아래로 스크롤한 다음 진단 로그를 선택합니다.

  	 ![image45][image45]

8.  로깅에 대한 다양한 옵션을 살펴봅니다. 웹 서버 로깅을 설정하고 저장을 클릭합니다.

    ![image46][image46]

9.  앱에 대한 도구 영역으로 다시 이동하여 서비스로서의 진단을 선택한 다음 실행을 클릭하여 데이터 수집을 다시 실행합니다.

    ![image47][image47]

10. HTTP 로깅 설정을 사용하면 이제 HTTP 로그에 수집된 데이터를 확인할 수 있습니다.

    ![image48][image48]

11. HTML 파일 로그를 클릭하여 추가로 조사할 풍부한 브라우저 기반 보고서를 생성합니다.

    ![image49][image49]

12. 앱에 대한 Azure 포털의 도구 섹션으로 다시 이동합니다. 도구 섹션으로 스크롤한 다음 프로세스 탐색기를 선택합니다.

    ![image50][image50]

13. 프로세스 탐색기를 선택하여 프로세스를 실행하는 방법에 대한 세부 정보를 볼 수 있습니다. 아래에서 프로세스를 자세히 살펴보고 나서 Azure 포털에서 모든 프로세스를 종료할 수 있습니다.

    ![image51][image51]

    ![image52][image52]

14. 왼쪽의 설정 블레이드로 다시 이동합니다. 새 지원 요청을 클릭합니다.

    ![image53][image53]

15. 오른쪽에 블레이드에서 문제에 대한 세부 정보를 채우고 연락처 정보를 입력하고 나서 진단 데이터를 업로드할 수 있습니다. Azure 포털을 통해 Microsoft로 작업하면 원활한 환경을 지원할 수 있습니다.

    ![image54][image54]

    ![image55][image55]

    Azure 포털 도움말은 강력하고 친숙한 도구 환경을 제공하여 실행 중인 응용 프로그램을 모니터링하고 문제를 해결하는 데 도움을 줍니다. 프로세스 재활용, 다양한 데이터 컬렉션 사용 및 사용 안 함, Microsoft 전문 지원과 통합 등의 작업을 수행하여 신속하게 조치를 취할 수도 있습니다.

## 일반 응용 프로그램 관리

응용 프로그램을 관리하는 경우, 백업 전략 구성, ID 공급자 구현과 관리 및 역할 기반 액세스 제어 구성과 같은 작업을 수행해야 하는 경우가 많습니다. 기타 DevOps 환경과 마찬가지로 Azure 플랫폼에서는 이러한 작업을 포털에 직접 통합합니다.

1.  웹앱을 데이터 손실로부터 안전하게 지키려면 백업을 구성해야 합니다. 웹앱의 설정 영역으로 이동합니다.

    ![image56][image56]

2.  오른쪽에 블레이드에서 기능 범주로 스크롤을 내립니다.

	 ![image57][image57]

1.  백업을 선택하면 오른쪽에 있는 블레이드가 열립니다.

    ![image58][image58]

2.  구성을 클릭하고 오른쪽의 블레이드에서 저장소 계정을 선택합니다.

    ![image59][image59]

3.  이제 백업을 저장할 저장소 컨테이너를 만들고 선택합니다. 블레이드 하단의 만들기를 클릭합니다. 그런 다음 컨테이너를 선택합니다.

    ![image60][image60]

4.  컨테이너를 선택하면 데이터베이스에 대한 설치 백업 뿐만 아니라 일정을 구성할 수 있습니다. 이 시나리오에서 저장 아이콘을 클릭합니다.

     ![image61][image61]

5.  저장한 후에 백업에 대한 왼쪽의 블레이드로 다시 스크롤합니다. 응용 프로그램을 백업하려면 지금 백업을 클릭합니다.

     ![image62][image62]

6.  몇 분 내에 만든 백업을 볼 수 있습니다. 스크린샷에 있는 지금 복원 옵션을 확인합니다.

     ![image63][image63]

7.  지금 복원을 클릭하고 오른쪽의 블레이드에서 옵션을 확인합니다. 적절한 백업을 선택하고 쉽게 필요에 따라 이전 상태로 복원할 수 있습니다. Azure 포털은 앱에 간단한 재해 복구 전략을 손쉽게 사용할 수 있도록 합니다.

     ![image64][image64]

8.  왼쪽에서 설정 블레이드로 다시 이동하고 기능에서 인증/권한 부여를 선택합니다.

     ![image65][image65]

9.  오른쪽의 블레이드에서 앱 서비스 인증을 선택합니다. 인기 있는 공급자를 통해 구성할 수 있는 다양한 옵션을 확인합니다.

     ![image66][image66]

10. 선택한 공급자를 선택하고 범위에 대한 옵션을 확인합니다. 앱 ID 및 앱 암호를 제공하고 앱에 Facebook 인증을 빠르게 설정할 수 있습니다. Azure 포털을 통해 앱에 대한 턴키 솔루션으로 인증을 활성화할 수 있습니다.

     ![image67][image67]

11. 설정 블레이드로 다시 이동하고 리소스 관리 범주에서 사용자를 선택합니다.

     ![image68][image68]

12. 오른쪽의 블레이드에서 역할 및 사용자를 추가하기 위한 다양한 옵션을 검사합니다. Azure 포털을 사용하면 응용 프로그램에서 RBAC(역할 기반 액세스 제어)를 쉽게 제어할 수 있습니다.

     ![image69][image69]


## 요약

이 자습서에서는 웹앱에 신속하게 연속 배포를 사용하고 다양한 개발을 수행하며 작업을 테스트하고 라이브 앱을 모니터링하며 문제를 해결하고 마지막으로 중요한 전략(예: 재해 복구, ID 및 역할 기반 액세스 제어)을 관리함으로써 Azure 플랫폼을 사용하는 장점 중 일부를 설명했습니다. Azure 플랫폼을 사용하면 이러한 DevOps 워크플로에 대한 통합된 환경을 사용할 수 있고 현재 작업에 컨텍스트를 유지하여 효율적으로 작업할 수 있습니다.


## 다음 단계 

* Azure Resource Manager는 Azure 플랫폼에서 DevOps를 사용하는 데 중요합니다. 더 알아보려면 [Azure Resource Manager 개요](../resource-group-overview.md)를 방문하세요.

* Azure 앱 서비스 배포에 대한 자세한 내용을 알아보려면 [Azure 앱 서비스에 앱 배포](../app-service-web/web-sites-deploy.md)를 방문하세요.


[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png

<!-----HONumber=AcomDC_0615_2016-->