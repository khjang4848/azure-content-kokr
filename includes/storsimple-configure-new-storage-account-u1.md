<!--author=alkohli last changed: 9/17/15-->

#### StorSimple 8000 시리즈 업데이트 1.0에서 저장소 계정을 추가하려면

1. StorSimple 관리자 서비스 방문 페이지에서 서비스를 선택하고 두번 클릭합니다. 이렇게 하면 **퀵 스타트** 페이지로 이동됩니다. **구성** 페이지를 선택합니다.

2. **저장소 계정 추가/편집**을 클릭합니다.

3. **저장소 계정 추가/편집** 대화 상자에서 **새로 추가**를 클릭합니다.

4. **공급자** 필드에서 적절한 클라우드 서비스 공급자를 선택합니다. 지원되는 공급자는 Azure, Amazon S3, Amazon S3 with RRS, HP 및 OpenStack입니다. 클라우드 서비스 공급자의 저장소 계정과 연결된 위치 및 자격 증명을 지정합니다. 자격 증명에 대해 제공하는 필드는 지정하는 클라우드 서비스 공급자에 따라 달라질 수 있습니다.
  - Azure를 클라우드 서비스 공급자로 선택한 경우 Microsoft Azure 저장소 계정에 대한 **이름** 및 기본 **액세스 키**를 제공합니다. Azure 계정에 대한 위치는 자동으로 채워집니다.

        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)

 - Amazon S3 또는 Amazon S3 with RRS을 선택한 경우 친숙한 **저장소 계정 이름**, **액세스 키**, 및 **비밀 키**를 제공합니다. Amazon S3 및 Amazon S3 with RRS의 경우 다음 위치가 지원됩니다.

		- US Standard
		- US West (Oregon)
		- US West (Northern California)
		- EU (Ireland)
		- Asia Pacific (Singapore)
		- Asia Pacific (Sydney)
		- Asia Pacific (Tokyo)
		- South America (Sao Paulo)

        ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
	  		
 - 클라우드 서비스 공급자로 HP를 선택한 경우, 친숙한 **저장소 계정 이름**, **테넌트 ID**, **사용자 이름** 및 **암호**를 제공합니다. HP의 경우 다음 위치가 지원됩니다.

		- US East
		- US West
	  
        ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
	  		
 - **Openstack**을 클라우드 서비스 공급자로 선택한 경우, **호스트 이름**, **액세스 키** 및 **비밀 키**를 제공합니다.

        > [AZURE.NOTE] Azure를 제외한 모든 클라우드 서비스 공급자는 대화명 사용이 허용됩니다. 대화명을 사용하고 같은 자격 증명 집합의 저장소 계정을 여러 개 만들 수 있습니다.

        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)

5. **SSL 모드 사용**을 선택하여 장치와 클라우드 간의 네트워크 통신을 위한 안전한 채널을 만듭니다. 사설 클라우드 내에서 작업 중인 경우에만 **SSL 모드 사용** 확인란을 지웁니다.

      >[AZURE.NOTE]HP를 공급자로 사용하는 경우 SSL을 항상 사용할 수 있습니다.
  		
6. 확인 아이콘![확인 아이콘](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png)을 클릭합니다. 저장소 계정이 성공적으로 만들어진 후 알림이 표시됩니다.

7. 새로 만들어진 저장소 계정이 **저장소 계정**의 **구성** 페이지에 표시됩니다. **저장**을 클릭하여 새 저장소 계정을 저장합니다. 확인하라는 메시지가 표시되면 **확인**을 클릭합니다.

<!-----HONumber=Oct15_HO3-->