<properties
   pageTitle="Linux, Unix 또는 OS X에서 Linux 기반 HDInsight의 SSH 키 사용 | Microsoft Azure"
   description=" SSH(보안 셸)를 사용하여 Linux 기반 HDInsight에 액세스할 수 있습니다. 이 문서에서는 Linux, Unix 또는 OS X 클라이언트에서 HDInsight와 함께 SSH를 사용하는 방법을 설명합니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH를 사용합니다.

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[SSH(보안 셸)](https://en.wikipedia.org/wiki/Secure_Shell)를 통해 명령줄 인터페이스를 사용하여 Linux 기반 HDInsight 클러스터에 대한 작업을 원격으로 수행할 수 있습니다. 이 문서에서는 Linux, Unix 또는 OS X 클라이언트에서 HDInsight와 함께 SSH를 사용하는 방법을 설명합니다.

> [AZURE.NOTE] 이 문서의 단계에서는 Linux, Unix 또는 OS X 클라이언트를 사용한다고 가정합니다. [Windows에서 Ubuntu의 Bash](https://msdn.microsoft.com/commandline/wsl/about)와 같은 `ssh` 및 `ssh-keygen`를 제공하는 패키지를 설치한 경우 Windows 기반 클라이언트에서 이 단계를 수행할 수 있습니다.
>
> SSH를 Windows 기반 클라이언트에 설치하지 않은 경우 PuTTY를 설치하고 사용하는 내용은 [Windows에서 Linux 기반 HDInsight(Hadoop)와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)의 단계를 사용합니다.

##필수 조건

* Linux, Unix 및 OS X 클라이언트용 **ssh-keygen** 및 **ssh**. 이 유틸리티는 일반적으로 운영 체제와 함께 제공되거나 패키지 관리 시스템을 통해 사용할 수 있습니다.

* HTML5를 지원하는 최신 웹 브라우저

또는

* [Azure CLI](../xplat-cli-install.md)

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##SSH 정의

SSH는 원격 서버에서 로그인 및 원격으로 실행하는 명령 유틸리티입니다. Linux 기반 HDInsight와 함께 SSH는 클러스터 헤드 노드에 대한 암호화된 연결을 설정하고 명령에서 입력하는 데 사용하는 명령줄을 제공합니다. 명령은 서버에서 직접 실행됩니다.

###SSH 사용자 이름

SSH 사용자 이름은 HDInsight 클러스터에 인증하는데 사용하는 이름입니다. 클러스터를 만드는 동안 SSH 사용자 이름을 지정하는 경우 해당 사용자는 클러스터의 모든 노드에 대해 만들어집니다. 클러스터를 만든 후 해당 사용자 이름을 사용하여 HDInsight 클러스터 헤드 노드에 연결할 수 있습니다. 그러면 헤드 노드에서 개별 작업자 노드에 연결할 수 있습니다.

###SSH 암호 또는 공개 키

SSH 사용자는 인증에 암호 또는 공개 키를 사용할 수 있습니다. 암호는 구성한 텍스트 스트링인 반면 공용 키는 고유하게 사용자를 식별하기 위해 생성된 암호화 키 쌍의 일부입니다.

키는 암호보다 안전하지만 키를 생성하기 위해 추가 단계가 필요하며 키가 포함된 파일을 안전한 위치에 유지 관리해야 합니다. 키 파일에 대한 액세스를 얻으면 계정에 대한 액세스를 얻게 됩니다. 또는 키 파일이 손실되면 계정에 로그인할 수 없게 됩니다.

키 쌍은 공개 키(HDInsight 서버에 전송됨)와 개인 키(클라이언트 컴퓨터에 있음)로 구성됩니다. SSH를 사용하여 HDInsight 서버에 연결하는 경우 SSH 클라이언트는 서버를 인증하기 위해 컴퓨터의 개인 키를 사용합니다.

##SSH 키 만들기

클러스터와 SSH 키를 사용하려는 경우에 다음 정보를 사용합니다. 암호를 사용하려는 경우 이 섹션을 건너뛸 수 있습니다.

1. 터미널 세션을 열고 다음 명령을 사용하여 기존 SSH 키가 있는지 확인합니다.

		ls -al ~/.ssh

	디렉터리 목록에서 다음 파일을 찾습니다. 이들은 공개 SSH 키에 대한 일반 이름입니다.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. 기존 파일을 사용하지 않으려는 경우 또는 기존 SSH 키가 없는 경우 다음을 사용하여 새 파일을 생성합니다.

		ssh-keygen -t rsa

	다음 정보를 묻는 메시지가 나타납니다.

	* 파일 위치 - 위치는 기본적으로 ~/.ssh/id\\_rsa로 설정됩니다.
	* 암호 - 이것을 다시 입력하라는 메시지가 표시됩니다.

		> [AZURE.NOTE] 키에 대한 보안 암호를 사용하는 것이 좋습니다. 그러나 암호를 잊은 경우 복구할 수 있는 방법이 없습니다.

	명령이 완료되면 개인 키(예: **id\_rsa**)와 공개 키(예: **id\_rsa.pub**), 이렇게 두 개의 새 파일이 생성됩니다.

##Linux 기반 HDInsight 클러스터 만들기

Linux 기반 HDInsight 클러스터를 만들 때 이전에 생성한 공개 키를 제공해야 합니다. Linux, Unix 또는 OS X 클라이언트에서 HDInsight 클러스터를 만들 수 있는 두 가지 방법이 있습니다.

* **Azure 포털** - 웹 기반 포털을 사용하여 클러스터를 만듭니다.

* **Mac, Linux 및 Windows용 Azure CLI** - 명령줄 명령을 사용하여 클러스터를 만듭니다.

이러한 각 메서드는 각각의 암호 또는 공개 키가 필요합니다. Linux 기반 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

###Azure 포털

[Azure 포털][preview-portal]을 사용하여 Linux 기반 HDInsight 클러스터를 만들 때 **SSH 사용자 이름**을 입력해야 하고 **암호** 또는 **SSH 공개 키**를 선택하여 입력해야 합니다.

**SSH 공개 키**를 선택하는 경우 확장명이 **.pub**인 파일에 포함된 공개 키를 __SSH 공개 키__ 필드에 붙여 넣거나 __파일 선택__을 선택하여 공개 키 파일을 찾아 선택할 수 있습니다.

![공개 키를 묻는 양식의 이미지](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] 키 파일은 단순한 텍스트 파일입니다. 내용은 다음과 유사합니다.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

이렇게 하면 제공한 암호 또는 공개 키를 사용하여 특정 사용자 로그인을 생성합니다.

###Mac, Linux 및 Windows용 Azure 명령줄 인터페이스

[Mac, Linux 및 Windows용 Azure CLI](../xplat-cli-install.md)를 사용하여 `azure hdinsight cluster create` 명령을 통해 새 클러스터를 만들 수 있습니다.

이 명령 사용에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight에서 Hadoop Linux 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

##Linux 기반 HDInsight 클러스터에 연결

터미널 세션에서 SSH 명령을 통해 주소 및 사용자 이름을 제공하여 클러스터 헤드 노드에 연결합니다.

* **SSH 주소** - SSH를 사용하여 클러스터에 연결하는 데 사용할 수 있는 두 개의 주소가 있습니다.

    * **헤드 노드에 연결**: **-ssh.azurehdinsight.net**이 뒤에 오는 클러스터 이름입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.
    
    * **에지 노드에 연결**: 클러스터가 HDInsight에서 R 서버인 경우 클러스터도 **RServer.CLUSTERNAME.ssh.azurehdinsight.net**를 사용하여 액세스할 수 있는 에지 노드를 포함합니다. 여기서 __CLUSTERNAME__은 클러스터의 이름입니다.

* **사용자 이름** - 클러스터를 만들 때 제공한 SSH 사용자 이름입니다.

다음 예제에서는 **mycluster**의 기본 헤드 노드에 **me** 사용자로 연결합니다.

	ssh me@mycluster-ssh.azurehdinsight.net

사용자 계정에 대한 암호를 사용하는 경우 암호를 입력하라는 메시지가 나타납니다.

암호로 보호된 SSH 키를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 그렇지 않은 경우 SSH에서 클라이언트의 로컬 개인 키 중 하나를 사용하여 자동으로 인증을 시도합니다.

> [AZURE.NOTE] SSH가 자동으로 올바른 개인 키로 인증하지 않는 경우, **-i** 매개 변수를 사용하고 개인 키에 대한 경로를 지정합니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

헤드 노드에 대한 주소를 사용하여 연결하고 지정된 포트가 없는 경우, SSH가 기본적으로 22 포트로 설정되며 이는 HDInsight 클러스터의 기본 헤드 노드에 연결됩니다. 23 포트를 사용하는 경우, 보조 헤드 노드에 연결됩니다. 헤드 노드에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)을 참조하세요.

###작업자 노드에 연결

작업자 노드는 Azure 데이터 센터 외부에서 직접 액세스할 수 없으며, SSH를 통해 클러스터 헤드 노드에서 액세스할 수 있습니다.

SSH 키를 사용하여 사용자 계정을 인증하는 경우 클라이언트에서 다음 단계를 완료해야 합니다.

1. 텍스트 편집기를 사용하여 `~/.ssh/config`를 엽니다. 이 파일이 존재하지 않으면 터미널에 `touch ~/.ssh/config`를 입력하여 만들 수 있습니다.

2. 파일에 다음을 추가합니다. *CLUSTERNAME*을 HDInsight 클러스터의 이름으로 바꿉니다.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    그러면 HDInsight 클러스터에 대한 SSH 에이전트 전달이 구성됩니다.

3. 터미널에서 다음 명령을 사용하여 SSH 에이전트 전달을 테스트합니다.

        echo "$SSH_AUTH_SOCK"

    다음과 유사한 정보가 반환됩니다.

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    아무 것도 반환되지 않으면 **ssh-agent**가 실행되고 있지 않음을 나타냅니다. 운영 체제 설명서에서 **ssh-agent**를 설치 및 구성하는 특정 단계를 참조하거나 [ssh와 함께 ssh-agent 사용](http://mah.everybody.org/docs/ssh)을 참조하세요.

4. **ssh-agent**가 실행 중인지 확인한 후에는 다음을 사용하여 SSH 개인 키를 에이전트에 추가합니다.

        ssh-add ~/.ssh/id_rsa

    개인 키가 다른 파일에 저장된 경우 `~/.ssh/id_rsa`를 해당 파일의 경로로 바꿉니다.

다음 단계를 사용하여 클러스터의 작업자 노드에 연결합니다.

> [AZURE.IMPORTANT] SSH 키를 사용하여 계정을 인증하는 경우 이전 단계를 완료하여 에이전트 전달이 작동하는지 확인해야 합니다.

1. 앞에서 설명한 대로 SSH를 사용하여 HDInsight 클러스터에 연결합니다.

2. 연결되면 다음을 사용하여 클러스터의 노드 목록을 검색합니다. *ADMINPASSWORD*를 클러스터 관리 계정의 암호로 바꿉니다. *CLUSTERNAME*을 클러스터의 이름으로 바꿉니다.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    `host_name`을 포함하여 클러스터의 노드에 대한 정보가 JSON 형식으로 반환되며, 여기에는 각 노드의 FQDN(정규화된 도메인 이름)이 포함됩니다. 다음은 **curl** 명령에서 반환되는 `host_name` 항목의 예입니다.

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. 연결하려는 작업자 노드 목록을 가져왔으면 서버의 SSH 세션에서 다음 명령을 사용하여 작업자 노드에 대한 연결을 엽니다.

        ssh USERNAME@FQDN

    *USERNAME*을 SSH 사용자 이름으로, *FQDN*을 작업자 노드의 FQDN으로 바꿉니다. 예를 들면 `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`과 같습니다.

    > [AZURE.NOTE] 암호를 사용하여 SSH 세션을 인증하는 경우 암호를 입력하라는 메시지가 다시 표시됩니다. SSH 키를 사용하는 경우에는 아무 메시지 없이 연결이 완료됩니다.

4. 세션이 설정되면 작업자 노드에 연결되었음을 나타내도록 터미널 프롬프트가 `username@hn#-clustername`에서 `username@wk#-clustername`로 변경됩니다. 이 시점부터 실행하는 모든 명령은 작업자 노드에서 실행됩니다.

4. 작업자 노드에 대한 작업 수행을 마쳤으면 `exit` 명령을 사용하여 작업자 노드의 세션을 닫습니다. 그러면 `username@hn#-clustername` 프롬프트가 반환됩니다.

##계정 추가

1. [SSH 키 만들기](#create-an-ssh-key-optional) 섹션에서 설명된 것처럼 새로운 사용자 계정에 대한 새로운 공개 키 및 개인 키를 생성합니다.

	> [AZURE.NOTE] 개인 키는 사용자가 클러스터에 연결하는 데 사용하거나 생성 후 이러한 클라이언트에 안전하게 전송하는 클라이언트에서 생성되어야 합니다.

1. SSH 세션에서 클러스터로 다음 명령을 사용하여 새 사용자를 추가합니다.

		sudo adduser --disabled-password <username>

	이렇게 하면 새 사용자 계정이 생성되지만 암호 인증이 비활성화됩니다.

2. 다음 명령을 사용하여 키를 보유하는 파일 및 디렉터리를 만듭니다.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Nano 편집기를 열면 새로운 사용자 계정에 대한 공개 키 콘텐츠를 복사하고 붙여 넣습니다. 마지막으로 **Ctrl-X**를 사용하여 파일을 저장하고 편집기를 종료합니다.

	![예제 키가 표시된 nano 편집기의 이미지](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. 새 사용자 계정에 .ssh 폴더 및 내용의 소유권을 변경하려면 다음 명령을 사용합니다.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 이제 새 사용자 계정 및 개인 키를 사용하여 서버에 인증할 수 있습니다.

##<a id="tunnel"></a>SSH 터널링

SSH는 웹 요청과 같은 로컬 요청을 HDInsight 클러스터에 터널링하는 데 사용할 수 있습니다. HDInsight 클러스터 헤드 노드에서 발생하는 경우 요청이 요청된 리소스에 라우팅됩니다.

> [AZURE.IMPORTANT] SSH 터널은 일부 Hadoop 서비스의 웹 UI에 액세스하기 위한 요구 사항입니다. 예를 들어 작업 기록 UI와 리소스 관리자 UI는 둘 다 SSH 터널을 사용해서만 액세스할 수 있습니다.

SSH 터널의 생성 및 사용에 대한 자세한 내용은 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

##다음 단계

이제 SSH 키를 사용하여 인증하는 방법을 배웠으므로 HDInsight에서 Hadoop과 MapReduce를 함께 사용하는 방법에 알아봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0921_2016-->