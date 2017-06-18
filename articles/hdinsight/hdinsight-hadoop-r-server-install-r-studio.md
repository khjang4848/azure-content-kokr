<properties
	pageTitle="HDInsight의 R 서버를 사용하여 RStudio 설치(미리 보기) | Microsoft Azure"
	description="HDInsight의 R 서버를 사용하여 RStudio를 설치하는 방법"
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# HDInsight의 R 서버를 사용하여 RStudio 설치(미리 보기)

현재 R에 사용할 수 있는 IDE(통합 개발 환경)에는 Microsoft에서 최근에 발표한 [R Tools for Visual Studio](https://www.visualstudio.com/ko-KR/features/rtvs-vs.aspx)(RTVS), [RStudio](https://www.rstudio.com/products/rstudio-server/)의 데스크톱 및 서버 도구 집합 또는 Walware의 Eclipse 기반 [StatET](http://www.walware.de/goto/statet)를 포함하여 여러 가지가 있습니다. Linux에서 가장 많이 사용되는 것은 원격 클라이언트에서 사용할 수 있도록 브라우저 기반 IDE를 제공하는 [RStudio 서버](https://www.rstudio.com/products/rstudio-server/)입니다. RStudio 서버를 HDInsight Premium 클러스터의 에지 노드에 설치하면 클러스터의 R 서버를 사용하여 R 스크립트를 개발 및 실행할 수 있는 완벽한 IDE 환경이 제공되므로 R 콘솔의 기본 사용 시보다 생산성을 크게 높일 수 있습니다.

이 문서에서는 사용자 지정 스크립트를 사용하여 클러스터의 에지 노드에 RStudio 서버의 커뮤니티(무료) 버전을 설치하는 방법을 알아봅니다. 상업적으로 사용이 허가된 Pro 버전의 RStudio 서버를 사용하려면 [RStudio 서버](https://www.rstudio.com/products/rstudio/download-server/)에서 제공하는 설치 지침을 따라야 합니다.

> [AZURE.NOTE] 이 문서의 단계에는 HDInsight 클러스터의 R 서버가 필요하며 [R 스크립트 작업 설치](hdinsight-hadoop-r-scripts-linux.md)를 사용하여 R이 설치된 HDInsight 클러스터를 사용하는 경우 제대로 작동하지 않습니다.

## 필수 조건

* R 서버가 설치된 Azure HDInsight 클러스터. 자세한 내용은 [HDInsight 클러스터에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요.
* SSH 클라이언트. Linux 및 Unix 배포 또는 Macintosh OS X의 경우 `ssh` 명령은 운영 체제에 제공됩니다. Windows의 경우 [OpenSSH 옵션](https://www.youtube.com/watch?v=CwYSvvGaiWU) 또는 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)와 [Cygwin](http://www.redhat.com/services/custom/cygwin/)를 사용하는 것이 좋습니다.


## 사용자 지정 스크립트를 사용하여 클러스터에 RStudio 설치

1. 클러스터의 에지 노드를 식별합니다. R 서버가 설치된 HDInsight 클러스터의 경우 헤드 노드와 에지 노드에 대한 명명 규칙은 다음과 같습니다.

	* 헤드 노드 - `CLUSTERNAME-ssh.azurehdinsight.net`
	* 에지 노드 - `R-Server.CLUSTERNAME-ssh.azurehdinsight.net`

2. 위 명명 패턴을 사용하여 클러스터의 에지 노드로 SSH를 실행합니다.
 
	* Linux 클라이언트에서 연결하려면 [Linux 기반 HDInsight 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)을 참조하세요.
	* Windows 클라이언트에서 연결하려면 [PuTTY를 사용하여 Linux 기반 HDInsight 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)을 참조하세요.

3. 연결이 완료되면 클러스터의 루트 사용자가 됩니다. SSH 세션에서 다음 명령을 사용합니다.

		sudo su -

4. 사용자 지정 스크립트를 다운로드하여 RStudio를 설치합니다. 다음 명령을 사용합니다.

		wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. 사용자 지정 스크립트 파일에 대한 권한을 변경하고 스크립트를 실행합니다. 다음 명령을 사용합니다.

		chmod 755 InstallRStudio.sh
		./InstallRStudio.sh

6. R 서버가 설치된 HDInsight 클러스터를 만드는 동안 SSH 암호를 사용한 경우 이 단계를 건너뛰고 다음 단계로 진행할 수 있습니다. 그렇지 않고 SSH 키를 사용하여 클러스터를 만든 경우 SSH 사용자에 대한 암호를 설정해야 합니다. RStudio에 연결할 때 이 암호가 필요합니다. 다음 명령을 실행합니다. **현재 Kerberos 암호**를 묻는 메시지가 표시되면 **Enter** 키를 누르면 됩니다. `USERNAME`을(를) HDInsight 클러스터에 대한 SSH 사용자로 교체해야 합니다.

		passwd USERNAME
		Current Kerberos password:
		New password:
		Retype new password:
		Current Kerberos password:
		
	암호가 성공적으로 설정된 경우 다음과 같은 메시지가 표시됩니다.

		passwd: password updated successfully


	SSH 세션을 종료합니다.

7. HDInsight 클러스터에서 `localhost:8787`을 클라이언트 컴퓨터에 매핑하여 클러스터에 대한 SSH 터널을 만듭니다. 새 브라우저 세션을 열기 전에 SSH 터널을 만들어야 합니다.

	* [Cygwin](http://www.redhat.com/services/custom/cygwin/)을 사용하는 Linux 클라이언트 또는 Windows 클라이언트에서 터미널 세션을 열고 다음 명령을 사용합니다.

			ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
			
		**USERNAME**을 HDInsight 클러스터에 대한 SSH 사용자로 바꾸고 **CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다. `-i id_rsa_key`을(를) 추가하여 암호 대신 SSH 키를 사용할 수도 있습니다.

	* PuTTY와 Windows 클라이언트를 사용하는 경우

		1.  PuTTY를 열고 연결 정보를 입력합니다. PuTTY를 잘 알고 있지 않다면 HDInsight와 함께 사용하는 방법에 대한 정보에 대해 [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조합니다.
		2.  대화 상자의 왼쪽에 있는 **Category** 섹션에서 **Connection**, **SSH**를 차례로 확장한 다음 **Tunnels**를 선택합니다.
		3.  **Options controlling SSH port forwarding** 양식에 다음 정보를 제공합니다.

			* **Source port** - 전달하려는 클라이언트의 포트입니다. 예를 들면 **8787**과 같습니다.
			* **Destination** - 로컬 클라이언트 컴퓨터에 매핑해야 하는 대상입니다. 예를 들면 **localhost:8787**과 같습니다.

			![SSH 터널 만들기](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "SSH 터널 만들기")

		4. **Add**를 클릭하여 설정을 추가한 다음 **Open**을 클릭하여 SSH 연결을 엽니다.
		5. 메시지가 표시되면 서버에 로그인합니다. 이 SSH 세션을 설정하고 터널을 사용하도록 설정합니다.

8. 웹 브라우저를 열고 터널에 대해 입력한 포트에 따라 다음 URL을 입력합니다.

		http://localhost:8787/ 

9. 클러스터에 연결할 SSH 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다. 클러스터를 만드는 동안 SSH 키를 사용한 경우 위 5단계에서 만든 암호를 입력해야 합니다.

	![R 스튜디오에 연결](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "SSH 터널 만들기")

10. RStudio 설치의 성공 여부를 테스트하려면 클러스터에서 R 기반 MapReduce 및 Spark 작업을 실행하는 테스트 스크립트를 실행하면 됩니다. SSH 콘솔로 돌아가 다음 명령을 입력하여 RStudio에서 실행할 테스트 스크립트를 다운로드합니다.

	* R이 설치된 Hadoop 클러스터를 만든 경우 다음 명령을 사용합니다.
		
			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

	* R이 설치된 Spark 클러스터를 만든 경우 다음 명령을 사용합니다.

			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. RStudio에 다운로드한 테스트 스크립트가 표시됩니다. 파일을 두 번 클릭하여 열고 파일의 내용을 선택한 다음 **Run**을 클릭합니다. **Console** 창에 출력이 표시됩니다.
 
	![설치 테스트](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "설치 테스트")

또 다른 옵션은 `source(testhdi.r)` 또는 `source(testhdi_spark.r)`을(를) 입력하여 스크립트를 실행하는 것입니다.

## 참고 항목

- [HDInsight 클러스터의 R 서버에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)

- [HDInsight Premium의 R 서버에 대한 Azure 저장소 옵션](hdinsight-hadoop-r-server-storage.md)


 

<!---HONumber=AcomDC_0921_2016-->