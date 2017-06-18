<properties
pageTitle="SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스"
description="SSH 터널을 사용하여 Linux 기반 HDInsight 노드에 호스팅되는 웹 리소스를 안전하게 검색하는 방법에 알아봅니다."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/22/2016"
ms.author="larryfr"/>

#SSH 터널링을 사용하여 Ambari 웹 UI, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스

Linux 기반 HDInsight 클러스터는 인터넷을 통해 Ambari 웹 UI에 대한 액세스를 제공하지만 일부 UI의 기능은 없습니다. 예를 들면 Ambari를 통해 노출되는 다른 서비스에 대한 웹 UI입니다. Ambari 웹 UI의 모든 기능의 경우 SSH 터널을 클러스터 헤드에 사용해야 합니다.

##SSH 터널의 요구 사항은 무엇입니까?

Ambari의 여러 메뉴는 클러스터에서 실행되는 다른 Hadoop 서비스에 의해 노출되는 웹사이트 및 서비스에 의존하므로 SSH 터널 없이 완전히 채워지지 않습니다. 종종 이러한 웹사이트는 보안이 유지되지 않으므로 인터넷에 직접 노출하는 것은 안전하지 않습니다. 때때로 서비스는 Zookeeper 노드와 같은 다른 클러스터 노드의 웹 사이트를 실행합니다.

다음은 SSH 터널 없이 액세스할 수 없는 Ambari 웹 UI가 사용하는 서비스입니다.

* JobHistory,
* NameNode,
* Thread Stacks,
* Oozie web UI
* HBase Master and Logs UI

클러스터를 사용자 지정하는 스크립트 작업을 사용하는 경우 웹 UI를 노출하는 설치하는 모든 서비스 또는 유틸리티는 SSH 터널이 필요합니다. 예를 들어 스크립트 작업을 사용하여 Hue를 설치하는 경우 SSH 터널을 사용하여 Hue 웹 UI에 액세스해야 합니다.

##SSH 터널이란?

[SSH(보안 셸) 터널링](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)은 HDInsight 클러스터 헤드 노드에 대한 SSH 연결을 통해 로컬 워크스테이션의 포트로 전송되는 트래픽을 라우팅합니다. 여기에서 요청은 헤드 노드에서 발생하는 경우 해결됩니다. 그러면 응답은 워크스테이션에 대한 터널을 통해 다시 라우팅됩니다.

##필수 조건

웹 트래픽에 대해 SSH 터널을 사용하는 경우 다음이 있어야 합니다.

* SSH 클라이언트. Linux 및 Unix 배포 또는 Macintosh OS X의 경우 `ssh` 명령은 운영 체제에 제공됩니다. Windows의 경우 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)가 좋습니다.

	> [AZURE.NOTE] `ssh` 또는 PuTTY 이외의 다른 SSH 클라이언트를 사용하려는 경우 SSH 터널을 설정하는 방법에 대해 클라이언트를 위한 설명서를 참조하세요.

* SOCKS 프록시를 사용하도록 구성할 수 있는 웹 브라우저

* __(선택 사항)__: 터널을 통해 특정 요청을 라우팅하는 규칙을 적용할 수 있는 [FoxyProxy](http://getfoxyproxy.org/,)와 같은 플러그인

	> [AZURE.WARNING] FoxyProxy와 같은 플러그인 없이 브라우저를 통해 만들어진 모든 요청은 터널을 통해 라우팅될 수 있습니다. 이 경우 브라우저에서 웹 페이지의 로딩 속도가 느릴 수 있습니다.

##<a name="usessh"></a>SSH 명령을 사용하여 터널 만들기

`ssh` 명령을 사용하여 SSH 터널을 만들려면 다음 명령을 사용합니다. __USERNAME__을 HDInsight 클러스터에 대한 SSH 사용자로 바꾸고 __CLUSTERNAME__을 HDInsight 클러스터의 이름으로 바꿉니다.

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

로컬 포트 9876에서 SSH를 통해 클러스터에 트래픽을 라우팅하는 연결을 만듭니다. 옵션은 다음과 같습니다.

* **D 9876** - 터널을 통해 로컬 포트에서 트래픽 라우팅

* **C** - 웹 트래픽은 대부분 텍스트이므로 모든 데이터 압축

* **2** - SSH가 프로토콜 버전 2만 시도하도록 강요

* **q** - 자동 모드

* **T** - 포트 전달 후 허위 tty 할당 비활성화

* **n** - 포트 전달 후 STDIN 읽지 않음

* **N** - 포트 전달 후 원격 명령 실행 안 함

* **f** - 백그라운드에서 실행

SSH 키를 사용하여 클러스터를 구성한 경우에는 `-i` 매개 변수를 사용하여 개인 SSH 키에 대한 경로를 지정할 수 있습니다.

명령이 완료되면 로컬 컴퓨터에서 9876 포트로 전송되는 트래픽이 SSL(Secure Sockets Layer)을 통해 클러스터 헤드 노드에 라우팅되며 생성된 곳에 나타납니다.

##<a name="useputty"></a>PuTTY를 사용하여 터널 만들기

PuTTY를 사용하여 SSH 터널을 만들려면 다음 단계를 사용합니다.

1. PuTTY를 열고 연결 정보를 입력합니다. PuTTY를 잘 알고 있지 않다면 HDInsight와 함께 사용하는 방법에 대한 정보에 대해 [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조합니다.

2. 대화 상자의 왼쪽에 있는 **Category** 섹션에서 **Connection**, **SSH**를 차례로 확장한 다음 **Tunnels**를 선택합니다.

3. **Options controlling SSH port forwarding** 양식에 다음 정보를 제공합니다.

	* **Source port** - 전달하려는 클라이언트의 포트입니다. 예를 들면 **9876**과 같습니다.

	* **Destination** - Linux 기반 HDInsight 클러스터의 SSH 주소입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.

	* **Dynamic** - 동적 SOCKS 프록시 라우팅을 활성화합니다.

	![터널링 옵션 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. **Add**를 클릭하여 설정을 추가한 다음 **Open**을 클릭하여 SSH 연결을 엽니다.

5. 메시지가 표시되면 서버에 로그인합니다. 이 SSH 세션을 설정하고 터널을 사용하도록 설정합니다.

##브라우저에서 터널 사용

> [AZURE.NOTE] 이 섹션의 단계는 Linux, Unix, Macintosh OS X 및 Windows 시스템에 자유롭게 사용할 수 있으므로 FireFox 브라우저를 사용합니다. Google Chrome, Microsoft Edge 또는 Apple Safari와 같은 다른 최신 브라우저도 제대로 작동하지만 일부 단계에 사용된 FoxyProxy 플러그인은 모든 브라우저에 대해 사용할 수 없습니다.

1. 브라우저를 구성하여 **SOCKS v5** 프록시로 **localhost:9876**을 사용합니다. Firefox 설정은 다음과 같습니다. 9876이 아닌 다른 포트를 사용한 경우 포트를 사용한 포트로 변경합니다.

	![Firefox 설정 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE] **Remote DNS**를 선택하면 HDInsight 클러스터를 통해 DNS(Domain Name System) 요청이 확인됩니다. 선택하지 않은 경우 DNS가 로컬로 확인됩니다.

2. Firefox에서 프록시 설정을 사용하거나 사용하지 않도록 설정한 상태에서 [http://www.whatismyip.com/](http://www.whatismyip.com/)과 같은 사이트를 방문하여 트래픽이 터널을 통해 라우팅되는지 확인합니다. 설정이 활성화되어 있는 동안 IP 주소는 Microsoft Azure 데이터 센터에 있는 컴퓨터의 주소입니다.

###브라우저 확장

작동하는 터널을 사용하여 브라우저를 구성하는 동안 보통 터널을 통해 모든 트래픽을 라우팅하지 않으려고 할 수 있습니다. [FoxyProxy](http://getfoxyproxy.org/)와 같은 브라우저 확장 프로그램이 URL 요청에 대해 일치하는 패턴을 지원하므로(FoxyProxy Standard 또는 Plus만 해당) 특정 URL에 대한 요청만 터널을 통해 전송됩니다.

FoxyProxy 표준을 설치한 경우 터널을 통해 HDInsight에 대한 트래픽을 전달만 하도록 구성하려면 다음 단계를 따르세요.

1. 브라우저에서 FoxyProxy 확장 프로그램을 엽니다. 예를 들어, Firefox에서 주소 필드 옆에 있는 FoxyProxy 아이콘을 선택합니다.

	![foxyproxy 아이콘](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. **Add New Proxy**를 선택하고 **General** 탭을 선택한 다음 **HDInsightProxy**의 프록시 이름을 입력합니다.

	![foxyproxy 일반](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. **Proxy Details** 탭을 선택하고 다음 필드를 입력합니다.

	* **Host or IP Address** - 로컬 컴퓨터에서 SSH 터널을 사용한 이후의 localhost입니다.

	* **Port** - SSH 터널에 사용한 포트입니다.

	* **SOCKS proxy** - 브라우저에서 터널을 프록시로 사용할 수 있도록 하려면 선택합니다.

	* **SOCKS v5** - 필요한 버전의 프록시를 설정하려면 선택합니다.

	![foxyproxy 프록시](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. **URL Patterns** 탭을 선택한 다음 **Add New Pattern**을 선택합니다. 다음을 사용하여 패턴을 정의하고 **OK**를 클릭합니다.

	* **Pattern Name** - **clusternodes** - 패턴의 이름입니다.

	* **URL 패턴** - ***internal.cloudapp.net*** - 클러스터 노드의 내부 정규화된 도메인 이름과 일치하는 패턴을 정의합니다.

	![foxyproxy 패턴](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

    설정에 대해 다음 정보를 사용하여 다른 패턴을 추가합니다.

    * __패턴 이름__ - headnode
    * __URL 패턴__ - *headnodehost*

    OK를 선택하여 이 패턴을 저장합니다.

4. **OK**를 클릭하여 프록시를 추가하고 **Proxy Settings**를 닫습니다.

5. FoxyProxy 대화 상자의 위쪽에서 **Select Mode**를 **Use proxies based on their pre-defined patterns and priorities**로 변경한 다음 **Close**를 클릭합니다.

	![foxyproxy 선택 모드](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

이러한 단계를 다른 후에는 __internal.cloudapp.net__ 문자열이 포함된 URL에 대한 요청만 SSL 터널을 통해 라우팅됩니다.

##Ambari 웹 UI 통해 확인

클러스터를 설정한 후에 Ambari 웹에서 서비스 웹 UI에 액세스할 수 있는지 확인하려면 다음 단계를 따릅니다.

1. 브라우저에서 http://headnodehost:8080으로 이동합니다. `headnodehost` 주소는 터널을 통해 클러스터로 전송되며 Ambari가 실행 중인 헤드 노드를 확인합니다. 메시지가 표시되면 클러스터의 관리자 사용자 이름(관리자) 및 암호를 입력합니다. Ambari 웹 UI에서 두 번째로 메시지가 표시될 수 있습니다. 그럴 경우 정보를 다시 입력합니다.
    
    > [AZURE.NOTE] http://headnodehost:8080 주소를 사용하여 클러스터에 연결하는 경우 터널을 통해 Ambari가 실행 중인 헤드 노드에 HTTP를 사용하여 직접 연결되고 통신은 SSH 터널을 사용하여 보안 설정됩니다. 터널을 사용하지 않고 인터넷을 통해 연결하는 경우 터널은 HTTPS를 사용하여 보안 설정됩니다. HTTPS를 사용하여 인터넷을 통해 연결하려면 https://CLUSTERNAME.azurehdinsight.net을 사용합니다. 여기서 __CLUSTERNAME__은 클러스터의 이름입니다.

2. Ambari 웹 UI에서 페이지의 왼쪽 목록에서 HDFS를 선택합니다.

	![HDFS가 선택된 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. HDFS 서비스 정보가 표시되면 __빠른 링크__를 선택합니다. 클러스터 헤드 노드 목록이 표시됩니다. 헤드 노드 중 하나를 선택한 다음 __NameNode UI__를 선택합니다.

	![확장된 빠른 링크 메뉴의 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

	> [AZURE.NOTE] 인터넷 연결이 느리거나 헤드 노드의 사용량이 많은 경우 __빠른 링크__를 선택하면 메뉴 대신에 대기 표시기가 나타납니다. 그럴 경우 데이터를 서버에서 받을 때까지 기다렸다가 목록을 다시 시도하세요.
    >
	> 해상도 모니터가 낮거나 브라우저 창이 최대화되지 않은 경우 __빠른 링크__ 메뉴의 일부 항목이 화면 오른쪽으로 잘릴 수 있습니다. 그럴 경우 마우스를 사용하여 메뉴를 확장한 다음 오른쪽 화살표 키를 사용하여 메뉴의 나머지 부분을 볼 수 있도록 오른쪽으로 화면을 스크롤합니다.

4. 다음과 유사한 페이지가 표시됩니다.

	![NameNode UI의 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

	> [AZURE.NOTE] 이 페이지에 대한 URL은 \_\_http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__와(과) 유사해야 합니다. 노드의 내부 정규화된 도메인 이름(FQDN)을 사용하며 SSH 터널을 사용하지 않고 액세스할 수 없습니다.

##다음 단계

이제 SSH 터널을 만들고 사용하는 방법을 배웠으므로 Ambari를 사용하여 클러스터를 모니터링 및 관리하는 정보에 대해 다음을 참조하세요.

* [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)

HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=AcomDC_0914_2016-->