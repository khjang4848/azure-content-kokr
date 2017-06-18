<properties
	pageTitle="HDInsight에서 Hadoop 서비스에 힙 덤프 사용 | Microsoft Azure"
	description="디버깅 및 분석을 위해 Linux 기반 HDInsight 클러스터에서 Hadoop 서비스에 힙 덤프를 사용하도록 설정합니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>


#Linux 기반 HDInsight(미리 보기)에서 Hadoop 서비스에 힙 덤프 사용

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

힙 덤프는 덤프가 만들어질 당시의 변수 값을 비롯해 응용 프로그램의 메모리에 대한 스냅숏을 포함합니다. 따라서 런타임에 발생하는 문제를 진단하는 데 매우 유용합니다.

> [AZURE.NOTE] 이 문서의 정보는 Linux 기반 HDInsight에만 적용됩니다. Windows 기반 HDInsight에 대한 자세한 내용은 [Windows 기반 HDInsight에서 Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dumps.md)을 참조하세요.

## <a name="whichServices"></a>Services

다음 서비스에 힙 덤프를 사용할 수 있습니다.

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

HDInsight에서 실행하는 map 및 reduce프로세스에 힙 덤프를 사용할 수도 있습니다.

## <a name="configuration"></a>힙 덤프 구성 이해

힙 덤프를 사용하려면 서비스를 시작할 때 JVM으로 옵션(opts 또는 매개 변수라고도 함)을 전달합니다. 대부분의 Hadoop 서비스는 서비스를 시작하는 데 사용되는 셸 스크립트를 수정하여 이 작업을 수행할 수 있습니다.

각 스크립트에는 JVM으로 전달되는 옵션이 포함된 ***\_OPTS**에 대한 내보내기가 있습니다. 예를 들어 **hadoop env.sh** 스크립트에는 `export HADOOP_NAMENODE_OPTS=`로 시작하는 줄에 NameNode 서비스에 대한 옵션이 포함되어 있습니다.

map 프로세스와 reduce 프로세스는 MapReduce 서비스의 자식 프로세스이므로 서로 약간 다릅니다. 각 map 또는 reduce 프로세스는 자식 컨테이너에서 실행되며, 각각에 대한 JVM 옵션을 포함하는 두 가지 항목이 있습니다. **mapred-site.xml**에 포함된 두 항목은 다음과 같습니다.

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [AZURE.NOTE] Ambari는 클러스터의 노드 간에 변경 내용 복제를 처리하므로 Ambari를 사용하여 스크립트 및 mapred-site.xml 설정을 모두 수정하는 것이 좋습니다. 구체적인 단계는 [Ambari 사용](#using-ambari) 섹션을 참조하세요.

###힙 덤프 사용

다음 옵션은 OutOfMemoryError가 발생한 경우 힙 덤프를 사용하도록 설정합니다.

    -XX:+HeapDumpOnOutOfMemoryError

**+**는 이 옵션이 사용하도록 설정되었음을 나타냅니다. 기본적으로 이 옵션은 사용하지 않도록 설정됩니다.

> [AZURE.WARNING] 덤프 파일은 용량이 클 수 있기 때문에 HDInsight의 Hadoop 서비스에는 기본적으로 힙 덤프가 사용되지 않습니다. 문제 해결을 위해 사용하도록 설정한 경우에는 문제를 재현하고 덤프 파일을 수집한 후 사용하지 않도록 설정해야 합니다.

###덤프 위치

덤프 파일의 기본 위치는 현재 작업 디렉터리입니다. 다음 옵션을 사용하여 파일이 저장되는 위치를 제어할 수 있습니다.

    -XX:HeapDumpPath=/path

예를 들어 `-XX:HeapDumpPath=/tmp`를 사용하면 덤프가 /tmp 디렉터리에 저장됩니다.

###스크립트

**OutOfMemoryError**가 발생한 경우 스크립트를 트리거할 수도 있습니다. 예를 들어 오류가 발생했음을 알 수 있도록 알림을 트리거할 수 있습니다. 이는 다음 옵션을 사용하여 제어합니다.

    -XX:OnOutOfMemoryError=/path/to/script

> [AZURE.NOTE] Hadoop은 분산 시스템이므로 사용되는 모든 스크립트는 서비스가 실행되는 클러스터의 모든 노드에 배치되어야 합니다.
>
> 또한 스크립트는 서비스 실행 계정에서 액세스할 수 있는 위치에 있어야 하며, 실행 권한을 제공해야 합니다. 예를 들어 `/usr/local/bin`에 스크립트를 저장하고 `chmod go+rx /usr/local/bin/filename.sh`를 사용하여 읽기 및 실행 권한을 부여할 수 있습니다.

##Ambari 사용

서비스에 대한 구성을 수정하려면 다음 단계를 사용합니다.

1. 클러스터에 대한 Ambari 웹 UI를 엽니다. URL은 https://YOURCLUSTERNAME.azurehdinsight.net입니다.

    메시지가 나타나면 클러스터의 HTTP 계정 이름(기본값: admin) 및 암호를 사용하여 사이트를 인증합니다.

    > [AZURE.NOTE] Ambari에서 사용자 이름 및 암호를 묻는 메시지가 다시 나타날 수 있습니다. 이 경우 동일한 계정 이름 및 암호를 다시 입력하면 됩니다.

2. 왼쪽의 목록을 사용하여 수정할 서비스 영역을 선택합니다. 예를 들어 **HDFS**를 선택합니다. 가운데 영역에서 **Configs** 탭을 선택합니다.

    ![HDFS Configs 탭이 선택된 Ambari 웹의 이미지](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. **Filter...** 항목에 **opts**를 입력합니다. 그러면 이 텍스트가 포함된 항목으로만 구성 항목 목록이 필터링되고, 셸 스크립트 또는 이러한 옵션을 설정하는 데 사용할 수 있는 **템플릿**을 신속하게 찾을 수 있습니다.

    ![필터링된 목록](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. 힙 덤프를 사용할 서비스에 대한 ***\_OPTS** 항목을 찾아서 사용할 옵션을 추가합니다. 다음 그림에서는 **HADOOP\_NAMENODE\_OPTS** 항목에 `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/`를 추가했습니다.

    ![-XX가 포함된 HADOOP\_NAMENODE\_OPTS:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

	> [AZURE.NOTE] map 또는 reduce 자식 프로세스에 힙 덤프를 사용할 때는 대신 **mapreduce.admin.map.child.java.opts** 및 **mapreduce.admin.reduce.child.java.opts**라는 레이블이 지정된 필드를 찾습니다.

    **Save** 단추를 사용하여 변경 내용을 저장합니다. 변경 내용을 설명하는 간단한 메모를 입력할 수 있습니다.

5. 변경 내용이 적용되면 하나 이상의 서비스 옆에 **Restart required** 아이콘이 표시됩니다.

    ![restart required 단추 및 restart 단추](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. 다시 시작해야 하는 각 서비스를 선택하고 **Service Actions** 단추를 사용하여 **Turn On Maintenance Mode**를 지정합니다. 이렇게 하면 다시 시작할 때 이 서비스에서 경고가 생성되지 않습니다.

    ![Turn on maintenance mode 메뉴](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. 유지 관리 모드를 설정한 후에는 **Restart** 단추를 사용하여 서비스에 대해 **Restart All Effected**를 지정합니다.

    ![Restart All Affected 항목](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

    > [AZURE.NOTE] **Restart** 단추의 항목은 서비스마다 다를 수 있습니다.

8. 서비스가 다시 시작되면 **Service Actions** 단추를 사용하여 **Turn Off Maintenance Mode**를 지정합니다. 그러면 Ambari에서 서비스에 대한 경고 모니터링을 재개합니다.

<!---HONumber=AcomDC_0914_2016-->