<properties
pageTitle="HDInsight에서 Pig와 함께 DataFu 사용"
description="DataFu는 Hadoop과 함께 사용하기 위한 라이브러리의 컬렉션입니다. HDInsight 클러스터에서 Pig와 함께 DataFu를 사용하는 방법에 대해 알아봅니다."
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
ms.date="08/23/2016"
ms.author="larryfr"/>

#HDInsight에서 pig와 함께 DataFu 사용

DataFu은 Hadoop과 함께 사용하기 위해 공개 소스 라이브러리의 컬렉션입니다. 이 문서에서는 HDInsight 클러스터에서 DataFu를 사용하는 방법과 Pig와 함께 DataFu 사용자 정의 함수(UDF)를 사용하는 방법을 배웁니다.

##필수 조건

* Azure 구독.

* Azure HDInsight 클러스터(Linux 또는 Windows 기반)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)과 기본적으로 비슷

##Linux 기반 HDInsight에 DataFu 설치

> [AZURE.NOTE] DataFu는 Linux 기반 클러스터 버전 3.3 이상 및 Windows 기반 클러스터에 설치됩니다. 3.3 이전 버전의 Linux 기반 클러스터에 설치되지 않습니다.
>
> Linux 기반 클러스터 버전 3.3 이상 또는 Windows 기반 클러스터를 사용하는 경우 이 섹션을 건너뛸 수 있습니다.

Maven 리포지토리에서 DataFu를 다운로드하고 설치할 수 있습니다. 다음 단계를 따라 DataFu를 HDInsight 클러스터에 추가합니다.

1. SSH를 사용하여 Linux 기반 HDInsight 클러스터에 연결합니다. HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

    * [Linux, OS X 및 Unix의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. 다음 명령을 사용하여 wget 유틸리티를 사용하여 DataFu jar 파일을 다운로드하거나 브라우저에 링크를 복사하여 붙여 넣여 다운로드를 시작합니다.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. 그런 다음 HDInsight 클러스터의 기본 저장소에 파일을 업로드합니다. 이렇게 하면 클러스터의 모든 노드에서 파일을 사용할 수 있으며, 클러스터를 삭제하고 다시 만들더라도 파일이 저장소에 남아 있습니다.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] 위의 예제에서는 이 디렉터리가 클러스터 저장소에 이미 존재하기 때문에 jar 파일을 `wasbs:///example/jars`에 저장합니다. HDInsight 클러스터 저장소에 원하는 모든 위치를 사용할 수 있습니다.

##Pig와 함께 DataFu 사용

이 섹션의 단계에서는 HDInsight에서 Pig를 사용하는 것에 익숙하고 Pig Latin 문만 제공하고 클러스터와 함께 사용하는 방법에 대한 단계는 제공하지 않는 것을 가정합니다. HDInsight와 함께 Pig 사용에 대한 자세한 내용은 [Pig와 함께 HDInsight 사용](hdinsight-use-pig.md)을 참조하세요.

> [AZURE.IMPORTANT] Linux 기반 HDInsight 클러스터의 Pig에서 DataFu를 사용하는 경우 다음 Pig Latin 문을 사용하여 jar 파일을 먼저 등록해야 합니다.
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu는 기본적으로 Windows 기반 HDInsight 클러스터에 등록됩니다.

일반적으로 DataFu 함수에 대한 별칭을 정의합니다. 예:

    DEFINE SHA datafu.pig.hash.SHA();
    
이는 SHA 해시 함수에 대해 `SHA`라는 별칭을 정의합니다. 그러면 이를 Pig Latin 스크립트에서 사용하여 입력 데이터에 대한 해시를 생성할 수 있습니다. 예를 들어, 다음은 입력 데이터의 이름을 해시 값으로 대체합니다.

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

다음과 같은 입력 데이터와 함께 사용하는 경우

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
다음 출력이 표시됩니다.

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##다음 단계

DataFu 또는 Pig에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Apache DataFu Pig 가이드](http://datafu.incubator.apache.org/docs/datafu/guide.html)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0914_2016-->