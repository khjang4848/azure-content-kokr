<properties
 pageTitle="HDInsight에서 WebHCat 오류를 해결하고 이해합니다."
 description="HDInsight의 WebHCat에서 반환된 일반 오류 및 문제를 해결하는 방법에 대해 알아봅니다."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/12/2016"
 ms.author="larryfr"/>

#HDInsightdml WebHCat(Templeton,)에서 받은 오류 이해 및 해결

WebHCat(이전의 Templeton)을 사용하여 HDInsight를 사용하는 경우 오류가 발생할 수 있습니다. 이 문서는 일반적인 오류 – 발생 이유 및 해결을 수행할 수 있는 지침을 제공합니다.

##WebHCat이란?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)은 [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog)용 REST API, Hadoop용 테이블 및 저장소 관리 계층입니다. WebHCat은 HDInsight 클러스터에서 기본적으로 활성화되어있으며 다양한 도구에서 사용되어 클러스터에 로그인하지 않고 작업을 제출하고, 작업 상태 등을 가져옵니다.

##구성 수정

> [AZURE.IMPORTANT] 구성된 최대값을 초과하기 때문에 이 문서에 나열된 몇몇 오류가 발생합니다. 해결 단계에서 값을 변경할 수 있음을 언급하면, 다음 중 하나를 사용하여 변경해야 합니다.

* **Windows** 클러스터: 클러스터를 만드는 동안 값을 구성하려면 스크립트 동작을 사용합니다. 자세한 내용은 [스크립트 동작 개발](hdinsight-hadoop-script-actions.md)을 참조하세요.

* **Linux** 클러스터: Ambari(웹 또는 REST API)를 사용하여 값을 수정합니다. 자세한 내용은 [Ambari를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

###기본 구성

다음은 WebHCat 성능에 영향을 주거나 이 값을 초과하는 경우 오류가 발생할 수 있는 기본 구성 값입니다.

| 설정 | 기능 | 기본값 |
| ------- | ------------ | ------------- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] | 동시에 활성화될 수 있는 최대 작업 수(보류 또는 실행 중인) | 10,000 |
| [templeton.exec.max-procs][max-procs] | 동시에 제공 될 수 있는 최대 요청 수 | 20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] | 작업 기록이 보존되는 기간(일) | 7 일 |

##너무 많은 요청

**HTTP 상태 코드**: 429

| 원인 | 해결 방법 |
| ----- | ---------- |
| 분당 WebHCat으로 제공되는 최대 동시 요청 수를 초과했습니다(기본값 20). | 작업을 줄여 동시 요청의 최대 수 이상으로 제출하지 않거나 `templeton.exec.max-procs`를 수정하여 동시 요청 제한을 늘립니다. 자세한 내용은 [구성 수정](#modifying-configuration)을 참조하세요. |

##서버 사용할 수 없음

**HTTP 상태 코드**: 503

| 원인 | 해결 방법 |
| ---------------- | ------------------- |
| 클러스터에 대한 기본 및 보조 헤드 노드 간 장애 조치 중에 발생합니다. | 2분을 기다린 후 작업을 다시 시도합니다. |

##잘못된 요청 콘텐츠: 작업을 찾을 수 없습니다.

**HTTP 상태 코드**: 400

| 원인 | 해결 방법 |
| ---------------- | ------------------- |
| 작업 세부 정보는 작업 기록 클리너에서 정리됩니다. | 작업 기록에 대한 기본 보존 기간은 7일입니다. `mapreduce.jobhistory.max-age-ms`을 수정하여 변경할 수 있습니다. 자세한 내용은 [구성 수정](#modifying-configuration)을 참조하세요. |
| 장애 조치때문에 작업이 중단되었습니다. | 최대 2분 동안 작업 제출을 다시 시도하세요. |
| 잘못된 작업 ID가 사용되었습니다. | 작업이 올바른지 확인하세요. |

##나쁜 게이트웨이

**HTTP 상태 코드**: 502

| 원인 | 해결 방법 |
| ---------------- | ------------------- |
| WebHCat 프로세스 내에서 내부 가비지 컬렉션이 발생합니다. | 가비지 컬렉션이 WebHCat 서비스 다시 시작 또는 완료를 대기합니다. |
| ResourceManager 서비스에서 응답을 기다리는 동안 시간이 초과되었습니다. 활성 응용 프로그램 수가 구성된 최대값(기본값 10, 000)이면 발생할 수 있습니다. | 현재 실행 중인 작업을 완료하거나 `yarn.scheduler.capacity.maximum-applications`을 수정하여 동시 작업 제한을 늘릴 때까지 대기합니다. 자세한 내용은 [구성 수정](#modifying-configuration)을 참조하세요. |
| [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) 호출을 통해 모든 작업을 검색하면 `Fields`가 `*`으로 설정됩니다. | *모든* 작업 정보를 검색하지 마십시오. 대신 `jobid`를 사용하여 특정 작업 ID보다 큰 작업에 대한 세부 정보만 검색합니다. 또는 `Fields`를 사용하지 마십시오. |
| 헤드 노드의 장애 조치 중 WebHCat 서비스가 종료됩니다. | 2분을 기다린 후 작업을 다시 시도합니다. |
| WebHCat을 통해 전송되는 500개 이상의 보류 중인 작업이 있습니다. | 더 많은 작업을 제출하기 전에 현재 보류 중인 작업이 완료될 때까지 대기합니다. |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 

<!---HONumber=AcomDC_0914_2016-->