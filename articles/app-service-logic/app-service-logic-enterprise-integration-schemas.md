<properties 
	pageTitle="스키마 및 엔터프라이즈 통합 팩 개요 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합 팩 및 논리 앱에서 스키마를 사용하는 방법 알아보기" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="deonhe"/>

# 스키마 및 엔터프라이즈 통합 팩에 대해 알아보기  

## 스키마를 사용하는 이유
스키마를 사용하여 수신한 XML 문서가 유효한지를 확인합니다. 이는 예상되는 데이터가 미리 정의된 형식으로 문서에 포함된다는 것을 말합니다.

## 스키마를 추가하는 방법
Azure 포털에서 다음을 수행합니다.

1. **더 많은 서비스**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)
3. 스키마를 추가할 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)
4. **스키마** 타일을 선택합니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)

#### 2MB보다 작은 스키마 파일을 추가하려면  

5. 열린 스키마 블레이드에서 **추가** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)
6. 스키마의 **이름**을 입력한 다음 스키마 파일을 업로드하고 **스키마** 텍스트 상자의 오른쪽에 있는 폴더 아이콘을 선택합니다. 업로드 프로세스가 완료되면 **확인** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)

#### 2MB(최대 8MB)보다 큰 스키마 파일을 추가하려면  

7. Blob 보안 액세스 수준이 **공용**인 경우 ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)

  * 스키마를 저장소에 업로드하고 URI를 복사합니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)

  * 스키마 추가에서 큰 파일을 선택하고 콘텐츠 URI에 URI를 제공합니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

8. Blob 보안 액세스 수준이 **익명 액세스 없음**인 경우 ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)

  * 스키마를 저장소에 업로드합니다. ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

  * 스키마에 대한 공유 액세스 서명을 생성합니다. ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

  * 스키마 추가에서 큰 파일을 선택하고 콘텐츠 URI에 공유 액세스 서명 URI를 제공합니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

9. 새로 추가된 스키마가 표시됩니다. ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## 스키마 사용 방법
- 스키마는 B2B 시나리오에서 교환되는 메시지의 유효성을 검사하는 데 사용됩니다.

## 스키마 편집 방법
1. **스키마** 타일을 선택합니다.
2. 열린 스키마 블레이드에서 편집하려는 스키마를 선택합니다.
3. 스키마 블레이드에서 **업로드** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)
4. 열린 파일 선택 대화 상자를 사용하여 업로드하려는 스키마 파일을 선택합니다.
5. 파일 선택에서 **열기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)
6. 성공적으로 업로드되었다는 알림을 받게 됩니다.

## 스키마 삭제 방법
1. **스키마** 타일을 선택합니다.
2. 열린 스키마 블레이드에서 삭제하려는 스키마를 선택합니다.
3. 스키마 블레이드의 메뉴 모음에서 **삭제** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)
4. 선택한 스키마를 삭제하려는 경우 삭제 스키마 대화 상자에서 **예**를 선택하여 선택한 내용을 확인합니다. ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)
5. 마지막으로, 스키마 블레이드에서 스키마 목록을 새로 고치면 삭제한 스키마는 더 이상 나열되지 않습니다. ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)

## 다음 단계

- [엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0921_2016-->