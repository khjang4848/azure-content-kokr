1. [Azure 포털][]에 로그온합니다.

2. 포털의 왼쪽 탐색 창에서 **새로 만들기**, **엔터프라이즈 통합** 및 **서비스 버스**를 차례로 클릭합니다.

4. **네임스페이스 만들기** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.

5. 네임스페이스 이름을 사용할 수 있게 설정한 후 가격 책정 계층(기본, 표준 또는 프리미엄)을 선택합니다.

7. **구독** 필드에서 네임스페이스를 만들 Azure 구독을 선택합니다.

9. **리소스 그룹** 필드에서 네임스페이스가 있는 기존 리소스 그룹을 선택하거나 새로 만듭니다.

8. **위치**에서 네임스페이스가 호스트되어야 하는 국가 또는 지역을 선택합니다.

	![네임스페이스 만들기][create-namespace]

6. **만들기**를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.
 
### 관리 자격 증명 얻기

1. 네임스페이스 목록에서 새로 만든 네임스페이스 이름을 클릭합니다.
 
3. 네임스페이스 블레이드에서 **공유 액세스 정책**을 클릭합니다.

4. **공유 액세스 정책** 블레이드에서 **RootManageSharedAccessKey**를 클릭합니다.

	![connection-info][connection-info]

5. **정책: RootManageSharedAccessKey** 블레이드에서 **연결 문자열–기본 키** 옆의 복사 단추를 클릭하여 나중에 사용하기 위해 연결 문자열을 클립보드에 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 붙여 넣습니다.

	![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure 포털]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->