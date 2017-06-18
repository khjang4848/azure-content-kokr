<properties
	pageTitle="OOM(메모리 부족) 오류 - Hive 설정 | Microsoft Azure"
	description="HDInsight의 Hadoop에서 Hive 쿼리로 OOM(메모리 부족) 오류를 수정합니다. 고객 시나리오는 많은 대형 테이블 간 쿼리입니다."
	keywords="메모리 부족 오류, OOM, Hive 설정"
	services="hdinsight"
	documentationCenter=""
	authors="rashimg"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="09/02/2016"
	ms.author="rashimg;jgao"/>

# Azure HDInsight의 Hadoop에서 Hive 메모리 설정으로 OOM(메모리 부족) 오류 수정

고객이 직면하는 일반적인 문제 중 하나는 Hive를 사용할 때 OOM(메모리 부족) 오류가 발생하는 것입니다. 이 문서에서는 고객 시나리오 및 문제 해결을 위한 Hive 설정에 대해 설명합니다.

## 시나리오: 큰 테이블에서 Hive 쿼리

고객은 Hive를 사용하여 아래 쿼리를 실행했습니다.

	SELECT
		COUNT (T1.COLUMN1) as DisplayColumn1,
		…
		…
		….
	FROM
		TABLE1 T1,
		TABLE2 T2,
		TABLE3 T3,
		TABLE5 T4,
		TABLE6 T5,
		TABLE7 T6
	where (T1.KEY1 = T2.KEY1….
		…
		…

이 쿼리의 미묘한 차이는 다음과 같습니다.

* T1은 큰 테이블 TABLE1에 대한 별칭이고 여기에는 많은 STRING 열 형식이 있습니다.
* 다른 테이블은 크지는 않지만 많은 열을 포함합니다.
* 모든 테이블은 서로 조인되며 TABLE1 및 기타 테이블의 여러 열과 조인되기도 합니다.

고객이 24 노드 A3 클러스터에서 MapReduce에 대해 Hive를 사용하여 쿼리를 실행하면 쿼리는 약 26분 동안 실행됩니다. 고객은 MapReduce에서 Hive를 사용하여 쿼리를 실행할 때 다음과 같은 경고 메시지를 보게 됩니다.

	Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
	Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

쿼리는 약 26분만에 실행 완료되었으므로 고객은 이러한 경고를 무시하고 대신 이 쿼리를 성능을 개선하는 방법에 초점을 두기 시작합니다.

고객은 [HDInsight에서 Hadoop에 대한 Hive 쿼리 최적화](hdinsight-hadoop-optimize-hive-query.md)를 참조하여 Tez 실행 엔진을 사용하기로 합니다. Tez 설정을 사용하여 동일한 쿼리를 실행하자, 쿼리가 15분만에 실행되었고 다음과 같은 오류가 발생합니다.

	Status: Failed
	Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
	org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
	org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
	Caused by: java.lang.OutOfMemoryError: Java heap space

고객은 더 큰 VM을 사용하면 힙 공간이 클 것이라 생각하고 더 큰 VM(즉, D12)을 사용하기로 합니다. 이렇게 해도 오류가 계속 발생합니다. 고객은 이 문제를 디버깅하기 위해 HDInsight 팀에 연락합니다.

## OOM(메모리 부족) 오류 디버깅

당사의 지원 및 엔지니어링 팀은 OOM(메모리 부족) 오류를 발생시킨 문제 중 하나가 [Apache JIRA에 설명된 알려진 문제](https://issues.apache.org/jira/browse/HIVE-8306)라는 것을 발견했습니다. JIRA의 설명은 다음과 같습니다.

	When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

hive-site.xml 파일을 찾아본 결과 **hive.auto.convert.join.noconditionaltask**가 실제로 **true**로 설정된 것을 확인했습니다.

	<property>
    	<name>hive.auto.convert.join.noconditionaltask</name>
    	<value>true</value>
    	<description>
      		Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
      		If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
      		specified size, the join is directly converted to a mapjoin (there is no conditional task).
    	</description>
  	</property>

경고 메시지와 JIRA에 따라, Map Join이 Java 힙 공간 OOM 오류의 원인이라는 가설을 세웠습니다. 따라서 이 문제를 보다 자세히 살펴보았습니다.

블로그 게시물 [HDInsight에서 Hadoop Yarn 메모리 설정](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx)에 설명된 것처럼 Tez 실행 엔진을 사용할 때 사용된 힙 엔진은 실제로 Tez 컨테이너에 속합니다. Tez 컨테이너 메모리를 설명하는 아래 이미지를 참조하세요.

![Tez 컨테이너 메모리 다이어그램: Hive 메모리 부족 오류 OOM](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


블로그 게시물에서 알 수 있듯이 다음 두 메모리 설정은 **hive.tez.container.size** 및 **hive.tez.java.opts** 힙에 대한 컨테이너 메모리를 정의합니다. 경험에 따르면 OOM 예외가 발생했다고 해서 컨테이너 크기가 너무 작은 것은 아닙니다. Java 힙 크기(hive.tez.java.opts)가 너무 작은 것입니다. OOM이 표시될 때마다 **hive.tez.java.opts**를 늘려볼 수 있습니다. 필요한 경우 **hive.tez.container.size**를 늘려야 할 수도 있습니다. **java.opts** 설정은 **container.size**의 80% 정도여야 합니다.

> [AZURE.NOTE]  **hive.tez.java.opts** 설정은 **hive.tez.container.size**보다 항상 작아야 합니다.

D12 컴퓨터에 28GB 메모리가 있으므로 10GB(10240MB)의 컨테이너 크기를 사용하고 java.opts에 80%를 할당하기로 했습니다. 이 작업은 아래 설정을 사용하여 Hive 콘솔에서 수행했습니다.

	SET hive.tez.container.size=10240
	SET hive.tez.java.opts=-Xmx8192m

이러한 설정에 따라 쿼리는 10분 이내에 성공적으로 실행됩니다.

## 결론: OOM 오류 및 컨테이너 크기

OOM 오류가 발생했다고 해서 반드시 컨테이너 크기가 너무 작은 것은 아닙니다. 대신, 힙 크기가 컨테이너 메모리 크기의 80% 이상이 되도록 늘려서 메모리 설정을 구성해야 합니다.

<!---HONumber=AcomDC_0914_2016-->