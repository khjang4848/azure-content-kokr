<properties
	pageTitle="Hadoop 샌드박스를 사용하여 Hadoop에 대해 자세히 알아보기 | Microsoft Azure"
	description="Hadoop 에코 시스템을 사용하는 방법에 대해 알아보려면 Azure 가상 컴퓨터에서 Hortonworks의 Hadoop 샌드박스를 설정할 수 있습니다. "
	keywords="Hadoop 에뮬레이터, Hadoop 샌드박스"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# 가상 컴퓨터에서 Hadoop 샌드박스를 사용하여 Hadoop 에코 시스템 시작

Hadoop 에코 시스템에 대해 알아보기 위해 가상 컴퓨터에서 Hortonworks의 Hadoop 샌드박스를 설치하는 방법을 알아봅니다. 이 샌드박스는 Hadoop, HDFS(Hadoop Distributed File System) 및 작업 제출에 대해 알아보는 로컬 개발 환경을 제공합니다.

## 필수 조건

* [Oracle VirtualBox](https://www.virtualbox.org/)

Hadoop에 익숙해졌으면 HDInsight 클러스터를 만들어 Azure에서 Hadoop 사용을 시작할 수 있습니다. 시작 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

## 가상 컴퓨터 다운로드 및 설치

1. [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox)의 Hortonworks Sandbox에서 HDP 2.4에 대한 __VIRTUALBOX 다운로드__ 항목을 선택합니다. 다운로드가 시작되기 전에 Hortonworks를 등록하라는 메시지가 표시됩니다.

    ![VirtualBox에 대한 Hortonworks Sandbox를 다운로드용 링크 이미지](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. 동일한 웹 페이지에서 Hortonworks Sandbox에 대한 HDP 2.4의 __VirtualBox 설치 가이드__를 선택합니다. 가상 컴퓨터에 대한 설치 지침을 포함하는 PDF를 다운로드합니다.

    ![설치 가이드 보기](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## 가상 컴퓨터 시작합

1. VirtualBox를 시작하고 Hortonworks Sandbox를 선택한 다음 __시작__, __일반 시작__을 차례로 선택합니다.

    ![일반 시작](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. 가상 컴퓨터가 완료되면 부팅 프로세스를 완료하고 로그인 지침이 표시됩니다. 웹 브라우저를 열고 표시된 URL로 이동합니다(일반적으로 http://127.0.0.1:8888).

## 암호 설정

1. Hortonworks Sandbox 페이지의 __시작__ 단계에서 __보기 고급 옵션__을 선택합니다. SSH를 사용하여 샌드박스에 로그인하는 이 페이지에 대한 정보를 사용합니다. 제공된 이름 및 암호를 사용합니다.

    > [AZURE.NOTE] 대신 SSH 클라이언트를 설치하지 않은 경우 \_\_http://localhost:4200/__의 가상 컴퓨터에 제공된 웹 기반 SSH을 사용할 수 있습니다.

    처음으로 SSH를 사용하여 연결한 경우 루트 계정의 암호를 변경하라는 메시지가 표시됩니다. 새 암호를 입력하고 이 암호는 나중에 SSH를 사용하여 로그인할 때 사용됩니다.

2. 일단 로그인하면 다음 명령을 입력합니다.

        ambari-admin-password-reset
    
    대화 상자가 나타나면 Ambari 관리자 계정에 대한 암호를 입력합니다. Ambari 웹 UI에 액세스할 때 사용됩니다.

## Hive 명령 사용

1. 샌드박스에 대한 SSH 연결에서 다음 명령을 사용하여 Hive 셸을 시작합니다.

        hive

2. 셸이 시작되면 다음을 사용하여 샌드박스와 함께 제공되는 테이블을 봅니다.

        show tables;

3. 다음을 사용하여 `sample_07` 테이블에서 10개의 행을 검색합니다

        select * from sample_07 limit 10;

## 다음 단계

* [Hortonworks Sandbox와 Visual Studio를 사용하는 방법 알아보기](hdinsight-hadoop-emulator-visual-studio.md)
* [Hortonworks Sandbox의 방법 알아보기](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 자습서 - HDP 시작](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->