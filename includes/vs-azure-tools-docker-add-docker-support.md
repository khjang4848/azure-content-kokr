1. Visual Studio **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가 > Docker 지원**을 선택합니다.

    ![Docker 지원 상황에 맞는 메뉴 추가](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. ASP.NET 코어 웹 프로젝트에 Docker 지원을 추가하면 Docker-Compose 파일, 배포 Windows PowerShell 스크립트 및 Docker 속성 파일을 비롯하여 프로젝트에 추가되는 Docker와 관련된 여러 파일이 추가됩니다.

    ![프로젝트에 추가된 Docker 파일](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE] [Windows 베타용 Docker](https://beta.docker.com)를 사용하는 경우 Properties\\Docker.props을 열고 기본값을 제거한 후 값이 적용되도록 Visual Studio를 다시 시작합니다.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```

<!---HONumber=AcomDC_0921_2016-->