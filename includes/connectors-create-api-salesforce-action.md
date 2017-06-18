조건을 추가했으므로 트리거에 의해 생성되는 데이터로 흥미로운 작업을 진행해볼 수 있습니다. 다음 단계에 따라 **Salesforce - 개체 가져오기** 동작을 추가합니다. 이 동작은 새 잠재 고객이 만들어질 때마다 데이터를 가져옵니다. 또한 Salesforce - 개체 가져오기 동작의 데이터를 사용하여 Office 365 커넥터를 통해 전자 메일을 보내는 두 번째 동작도 추가합니다.

이 동작을 구성하려면 다음 정보를 제공해야 합니다. 트리거에 의해 생성된 데이터를 새 파일의 일부 속성에 대한 입력으로 쉽게 사용할 수 있습니다.

|파일 속성 만들기|설명|
|---|---|
|개체 형식|관심이 있는 Salesforce 개체의 형식입니다. 예로 Lead, Account 등이 있습니다.|
|개체 ID|개체에 대한 식별자를 나타냅니다.|


1. **동작 추가** 링크를 선택합니다. 수행할 동작을 검색할 수 있는 검색 상자가 열립니다. 이 예제에서는 Salesforce 동작을 사용합니다.  
![Salesforce 동작 이미지 1](./media/connectors-create-api-salesforce/action-1.png)  
- *salesforce*를 입력하여 salesforce와 관련된 동작을 검색합니다.
- 수행할 동작으로 **Salesforce - 개체 가져오기**를 선택합니다. **참고**: 논리 앱에 Salesforce 계정에 액세스하기 위한 권한을 아직 부여하지 않았으면 이러한 권한을 부여하라는 메시지가 표시됩니다.  
![Salesforce 동작 이미지 2](./media/connectors-create-api-salesforce/action-2.png)  
- **개체 가져오기** 컨트롤이 열립니다.
- 개체 형식으로 *lead*를 선택합니다.
- **개체 ID** 컨트롤을 선택합니다.
- **...**를 선택하여 동작에 대한 입력으로 사용할 수 있는 토큰의 목록을 확장합니다.  
![Salesforce 동작 이미지 3](./media/connectors-create-api-salesforce/action-3.png)  
- 열린 **잠재 고객 ID** 컨트롤을 선택합니다.  
![Salesforce 동작 이미지 4](./media/connectors-create-api-salesforce/action-4.png)  
- 이제 잠재 고객 ID 토큰이 개체 ID 컨트롤에 있습니다. 이것은 개체 가져오기 동작이 이 논리 앱을 트리거한 잠재 고객의 잠재 고객 ID와 같은 ID를 갖는 잠재 고객을 검색함을 나타냅니다.  
![Salesforce 동작 이미지 5](./media/connectors-create-api-salesforce/action-5.png)  
- 작업을 저장합니다. 이제 논리 앱에 개체 가져오기 동작을 추가하는 동작이 끝났습니다. 개체 가져오기 컨트롤은 다음과 같습니다.  
![Salesforce 동작 이미지 6](./media/connectors-create-api-salesforce/action-6.png)

잠재 고객을 가져오는 동작을 추가했으므로 새로 만든 잠재 고객과 관련해서 관심 있는 동작을 수행할 수 있습니다. 엔터프라이즈에서는 메일 그룹에 새 잠재 고객을 만들었음을 알리는 전자 메일을 보낼 수 있습니다. Office 365 커넥터를 사용하여 Salesforce의 새 잠재 고객 개체에 대한 관련 정보 일부를 전자 메일로 보낼 수 있습니다.

1. **동작 추가**를 선택하고 검색 컨트롤에 *전자 메일*을 입력합니다. 이렇게 하면 전자 메일 송수신과 관련된 동작으로 필터링됩니다.
- **Office 365 Outlook - 전자 메일 보내기** 목록 항목을 선택합니다. Office 365 계정에 대한 *연결*을 아직 만들지 않은 경우 지금 Office 365 자격 증명을 입력하여 만들라는 메시지가 표시됩니다. 작업이 끝나면 **전자 메일 보내기** 컨트롤이 열립니다.  
![Salesforce 동작 이미지 7](./media/connectors-create-api-salesforce/action-7.png)  
- 전자 메일을 보낼 전자 메일 주소를 **받는 사람** 컨트롤에 입력합니다.
-  **제목** 컨트롤에 *생성된 새 잠재 고객 -*을 입력하고 *회사* 토큰을 선택합니다. 이렇게 하면 Salesforce에서 만들어진 새 잠재 고객의 *회사* 필드가 표시됩니다.
-  **본문** 컨트롤에서 새 잠재 고객 개체의 토큰을 선택할 수도 있고, 전자 메일 본문에 표시하려는 텍스트를 입력할 수도 있습니다. 예를 들면 다음과 같습니다.  
![Salesforce 동작 이미지 8](./media/connectors-create-api-salesforce/action-8.png)  
- 워크플로를 저장합니다.

이것으로 끝입니다. 논리 앱이 완료되었습니다.

이제 논리 앱을 테스트할 수 있습니다. Salesforce에서 만든 조건을 충족하는 새 잠재 고객을 만듭니다. 이 연습 과정을 완료한 경우 *amazon.com*을 포함하는 전자 메일 주소를 사용하여 잠재 고객을 만들면 됩니다. 몇 초 후에 논리 앱이 트리거되고 다음과 유사한 결과가 표시될 수 있습니다.  
![Salesforce 동작 이미지 9](./media/connectors-create-api-salesforce/action-9.png)  

<!---HONumber=AcomDC_0914_2016-->