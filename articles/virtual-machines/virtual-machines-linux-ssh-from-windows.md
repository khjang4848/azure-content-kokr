<properties 
	pageTitle="Windows의 SSH를 사용하여 Linux 가상 컴퓨터에 연결 | Microsoft Azure" 
description="Windows 컴퓨터에서 SSH 키를 생성하고 사용하여 Azure에서 Linux 가상 컴퓨터에 연결하는 방법에 대해 알아봅니다." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/29/2016" 
	ms.author="rasquill"/>

#Azure에서 Windows와 함께 SSH를 사용하는 방법

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)


이 항목에서는 **ssh-rsa** 및 **.pem** 형식의 공용 및 개인 키 파일을 Windows에 만들고 사용하여 **ssh** 명령으로 Azure에 Linux VM을 연결할 방법에 대해 설명합니다. **.pem** 파일이 이미 만들어져 있는 경우 해당 파일을 사용하여 Linux VM(**ssh**를 사용하여 연결)을 만듭니다. 일부 다른 명령은 **SSH** 프로토콜 및 키 파일을 사용하여 작업을 안전하게 수행하며 특히 **scp** 또는 [안전한 복사](https://en.wikipedia.org/wiki/Secure_copy)는 **SSH** 연결을 지원하는 컴퓨터에서 파일을 안전하게 복사할 수 있습니다.

> [AZURE.NOTE] 잠시 시간을 내어 사용 환경에 대한 [간단한 설문](https://aka.ms/linuxdocsurvey)에 응답하여 Azure Linux VM 설명서를 개선하는 데 도움을 주세요. 모든 답변은 작업 수행에 도움이 될 것입니다.

## 어떤 SSH 및 키 생성 프로그램이 필요한가요?

**SSH** &#8212; 또는 [secure shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212;은 안전하지 않은 연결을 통해 안전하게 로그인할 수 있도록 하는 암호화된 연결 프로토콜입니다. Linux VM이 다른 연결 메커니즘을 사용하도록 구성하지 않는 한 Azure에 호스트되는 Linux VM에 대한 기본 연결 프로토콜입니다. Windows 사용자는 **ssh** 클라이언트 구현을 사용하여 Azure에서 Linux VM을 연결하고 관리할 수도 있지만 일반적으로 Windows 컴퓨터는 **ssh** 클라이언트와 함께 제공되지 않으므로 하나를 선택해야 합니다.

설치할 수 있는 일반적인 클라이언트는 다음과 같습니다.

- [puTTY 및 puTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- 환경 및 도구와 함께 제공되는 [Git For Windows](https://git-for-windows.github.io/)

충분한 식견이 있다면 [새 포트의 **OpenSSH** 도구 집합을 Windows에](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx) 사용해 볼 수 있습니다. 그러나 이 코드는 현재 개발 중이므로 프로덕션 시스템에 사용하기 전에 코드베이스를 검토해야 합니다.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 어떤 키 파일을 만들어야 하나요?

Azure에 대한 기본 SSH 설정은 2048비트(기본적으로 **ssh-keygen**은 기본값을 변경하지 않는 한 이러한 파일을 **~/.ssh/id\_rsa** 및 **~/.ssh/id-rsa.pub**으로 저장)의 **ssh-rsa** 공용 및 개인 키 쌍뿐만 아니라 클래식 포털의 클래식 배포 모델과 함께 사용하기 위한 **id\_rsa** 개인 키 파일에서 생성된 `.pem` 파일을 포함합니다.

다음은 배포 시나리오 및 각각에 사용되는 파일 형식입니다.

1. **ssh-rsa** 키는 배포 모델과 관계없이 [Azure 포털](https://portal.azure.com)을 사용하는 모든 배포에 필요합니다.
2. [클래식 포털](https://manage.windowsazure.com)을 사용하는 VM을 만들려면 .pem 파일이 필요합니다. .pem 파일은 [Azure CLI](../xplat-cli-install.md)를 사용하는 클래식 배포에서도 지원됩니다.

> [AZURE.NOTE] 클래식 배포 모델로 배포되는 서비스를 관리하려는 경우 **.cer** 서식 파일을 만들어 포털에 업로드하려고 할 수 있습니다. 여기엔 이 문서의 주제인 Linux VM에 연결 또는 **ssh**는 포함되어 있지 않습니다. Windows에서 이러한 파일을 만들려면 <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer을 입력합니다.

## Windows에서 ssh-keygen 및 openssl 가져오기 ##

Windows용 `ssh-keygen` 및 `openssl`을 포함한 여러 유틸리티가 [이 섹션](#What-SSH-and-key-creation-programs-do-you-need) 위에 나열되어 있습니다. 몇 가지 예가 아래 나열되어 있습니다.

###Windows용 Git 사용###

1.	[https://git-for-windows.github.io/](https://git-for-windows.github.io/)에서 Windows용 Git를 다운로드하여 설치합니다.
2.	시작 메뉴 > 모든 앱 > Git Shell에서 Git Bash를 실행합니다.

> [AZURE.NOTE] 위의 `openssl` 명령을 실행할 때 다음 오류가 발생할 수 있습니다.

        Unable to load config info from /usr/local/ssl/openssl.cnf

이를 해결하는 가장 쉬운 방법은 `OPENSSL_CONF` 환경 변수를 설정하는 것입니다. 이 변수를 설정하기 위한 프로세스는 Github에 구성된 셸에 따라 다를 수 있습니다.

**Powershell:**

        $Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

**CMD:**

        set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

**Git Bash:**

        export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf
	

###Cygwin 사용###

1.	다음 위치에서 Cygwin 다운로드 및 설치: [http://cygwin.com/](http://cygwin.com/)
2.	OpenSSL 패키지와 모든 종속성이 설치되었는지 확인합니다.
3.	`cygwin` 실행

## 개인 키 만들기##

1.	위의 지침 집합 중 하나를 따라 `openssl.exe`를 실행합니다.
2.	다음 명령을 입력합니다.

  ```
  openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  ```
3.	화면이 다음과 같이 표시됩니다.

  ```
  $ openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  Generating a 2048 bit RSA private key
  .......................................+++
  .......................+++
  writing new private key to 'myPrivateKey.key'
  -----
  You are about to be asked to enter information that will be incorporated
  into your certificate request.
  What you are about to enter is what is called a Distinguished Name or a DN.
  There are quite a few fields but you can leave some blank
  For some fields there will be a default value,
  If you enter '.', the field will be left blank.
  -----
  Country Name (2 letter code) [AU]:
  ```

4.	표시되는 질문에 대답합니다.
5.	`myPrivateKey.key` 및 `myCert.pem`, 2개의 파일을 만들었습니다.
6.	API를 직접 사용하고 관리 포털을 사용하지 않으려는 경우 다음 명령을 사용하여 `myCert.pem`을 `myCert.cer`(DER 인코딩된 X509 인증서)로 변환합니다.

  ```
  openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
  ```

## Putty용 PPK 만들기 ##

1. 다음 위치에서 Puttygen 다운로드 및 설치: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen은 앞에서 만든 개인 키(`myPrivateKey.key`)를 읽지 못할 수도 있습니다. 이 경우 다음 명령을 실행하여 해당 키를 Puttygen이 이해할 수 있는 RSA 개인 키로 변환합니다.

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	위의 명령을 실행하면 새 개인 키 myPrivateKey\_rsa가 생성됩니다.

3. `puttygen.exe` 실행

4. 메뉴 클릭: 파일 > 개인 키 로드

5. 위에서 이름을 `myPrivateKey_rsa`로 지정한 개인 키를 찾습니다. **모든 파일(*.*)**을 표시하도록 파일 필터를 변경해야 합니다.

6. **열기**를 클릭합니다. 다음과 같은 프롬프트가 표시됩니다.

	![linuxgoodforeignkey](./media/virtual-machines-linux-ssh-from-windows/linuxgoodforeignkey.png)

7. **확인**을 클릭합니다.

8. 아래 스크린샷에서 강조 표시된 **개인 키 저장**을 클릭합니다.

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-windows/linuxputtygenprivatekey.png)

9. 파일을 PPK로 저장합니다.


## Putty를 사용하여 Linux 컴퓨터에 연결 ##

1.	다음 위치에서 putty 다운로드 및 설치: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	putty.exe를 실행합니다.
3.	관리 포털의 IP를 사용하여 호스트 이름을 입력합니다.

	![linuxputtyconfig](./media/virtual-machines-linux-ssh-from-windows/linuxputtyconfig.png)

4.	**열기**를 선택하기 전에 연결 > SSH > 인증 탭을 클릭하여 개인 키를 선택합니다. 내용을 입력할 필드는 아래 스크린샷을 참조하세요.

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-windows/linuxputtyprivatekey.png)

5.	**열기**를 클릭하여 가상 컴퓨터에 연결합니다.
 

<!---HONumber=AcomDC_0914_2016-->