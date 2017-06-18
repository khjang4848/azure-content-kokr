<properties 
	pageTitle="Azure 저장소 탐색기를 사용하여 Azure Blob 저장소의 데이터 이동 | Microsoft Azure" 
	description="Azure 저장소 탐색기를 사용하여 Azure Blob 저장소의 데이터 이동" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/31/2016"
	ms.author="bradsev" />

# Azure 저장소 탐색기를 사용하여 Azure Blob 저장소의 데이터 이동

Azure Storage 탐색기는 Windows, macOS 및 Linux에서 Azure Storage 데이터 작업 시에 사용할 수 있는 Microsoft의 무료 도구입니다. 이 항목에서는 Azure Storage Explorer를 사용하여 Azure Blob 저장소에서 데이터를 업로드 및 다운로드하는 방법을 설명합니다. 이 도구는 [Microsoft Azure Storage 탐색기](http://storageexplorer.com/)에서 다운로드할 수 있습니다.

Azure Blob 저장소로 및/또는 저장소에서 데이터를 이동하는 데 사용되는 기술 지침은 여기에 연결되어 있습니다.
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

 
> [AZURE.NOTE] [Azure의 데이터 과학 가상 컴퓨터](machine-learning-data-science-virtual-machines.md)에서 제공하는 스크립트를 통해 설정된 VM을 사용하는 경우 Azure 저장소 탐색기가 VM에 이미 설치되어 있습니다.
 
> [AZURE.NOTE] Azure Blob 저장소에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../storage/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob 서비스](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.

## 필수 조건

이 문서에서는 사용자에게 Azure 구독, 저장소 계정 및 계정에 해당하는 저장소 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

- Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- 저장소 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요. 저장소 계정의 선택키를 적어 두세요. Azure 저장소 탐색기 도구를 사용하여 계정에 연결하려면 이 키가 있어야 합니다.
- Azure Storage 탐색기 도구는 [Microsoft Azure Storage 탐색기](http://storageexplorer.com/)에서 다운로드할 수 있습니다. 설치 중에 표시되는 기본값을 적용합니다.


<a id="explorer"></a>
## Azure 저장소 탐색기 사용 

다음 단계에서는 Azure 저장소 탐색기를 사용하여 데이터를 업로드/다운로드하는 방법을 설명합니다.

1.  Microsoft Azure Storage 탐색기를 시작합니다.
2.  **계정에 로그인...** 마법사를 실행하려면 **Azure 계정 설정** 아이콘을 선택하고 **계정 추가**를 선택한 후에 자격 증명을 입력합니다. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  **Azure Storage에 연결** 마법사를 실행하려면 **Azure Storage에 연결** 아이콘을 선택합니다. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. **Azure Storage에 연결** 마법사에서 Azure 저장소 계정의 선택키를 입력한 후에 **다음**을 클릭합니다. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. **계정 이름** 상자에 저장소 계정 이름을 입력한 후에 **다음**을 클릭합니다. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 그러면 추가한 저장소 계정이 목록에 표시됩니다. 저장소 계정에서 Blob 컨테이너를 만들려면 해당 계정의 **Blob 컨테이너** 노드를 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 선택한 후에 이름을 입력합니다.
7. 컨테이너에 데이터를 업로드하려면 대상 컨테이너를 선택하고 **업로드** 단추를 클릭합니다.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. **파일** 상자 오른쪽의 **...**을 클릭하고 파일 시스템에서 업로드할 파일을 하나 이상 선택한 후에 **업로드**를 클릭하여 파일 업로드를 시작합니다.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. 데이터를 다운로드하려면 해당 컨테이너에서 다운로드하려는 Blob를 선택한 후에 **다운로드**를 클릭합니다. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

<!---HONumber=AcomDC_0914_2016-->