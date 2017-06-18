<properties
   pageTitle="HDinsight에서 Storm 토폴로지에 Python 구성 요소 사용 | Microsoft Azure"
   description="Azure HDInsight에서 Apache Storm과 함께 Python 구성 요소를 사용하는 방법에 대해 알아봅니다. Java 기반 및 Clojure 기반 Storm 토폴로지에서 Python 구성 요소를 사용하는 방법에 대해 알아보겠습니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/11/2016"
   ms.author="larryfr"/>

#HDInsight에서 Python을 사용하여 Apache Storm 토폴로지 개발

Apache Storm은 여러 언어를 지원하여 한 토폴로지에 여러 언어의 구성 요소를 결합할 수 있습니다. 이 문서에서는 HDInsight에서 Java 및 Clojure 기반 Storm 토폴로지에서 Python 구성 요소를 사용하는 방법을 배웁니다.

##필수 조건

* Python 2.7 이상

* Java JDK 1.7 이상

* [Leiningen](http://leiningen.org/)

##Storm 다중 언어 지원

Storm은 모든 프로그래밍 언어로 작성된 구성 요소와 함께 작동하도록 설계되었지만 이를 위해서는 구성 요소에서 [Storm에 대한 Thrift 정의](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)가 작동하는 방식을 이해해야 합니다. Python의 경우 모듈은 Apache Storm 프로젝트의 일부로 제공되므로 Storm과 쉽게 인터페이스할 수 있습니다. 이 모듈을 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)에서 찾을 수 있습니다.

Apache Storm은 JVM(Java Virtual Machine)에서 실행되는 Java 프로세스이므로 다른 언어로 작성된 구성 요소는 하위 프로세스로 실행됩니다. JVM에서 실행되는 Storm 비트는 stdin/stdout을 통해 전송되는 JSON 메시지를 사용하여 이러한 하위 프로세스와 통신합니다. 구성 요소 간의 통신에 대한 자세한 내용은 [다중 언어 프로토콜](https://storm.apache.org/documentation/Multilang-protocol.html)(영문) 설명서에서 확인할 수 있습니다.

###Storm 모듈

Storm 모듈(https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,)은 Storm과 함께 작동하는 Python 구성 요소를 만드는 데 필요한 비트를 제공합니다.

`storm.emit`를 제공하여 튜플을 내보내고 `storm.logInfo`를 제공하여 로그에 기록합니다. 이 파일을 검토하여 제공하는 내용을 확인해보세요.

##과제

__storm.py__ 모듈을 사용하면 데이터를 사용하는 Python Spout와 데이터를 처리하는 Bolt를 만들 수 있습니다. 하지만 구성 요소 간 통신을 연결하는 전반적인 Storm 토폴로지 정의는 여전히 Java 또는 Clojure로 작성됩니다. 또한 Java를 사용하면 Python 구성 요소에 대한 인터페이스 역할을 하는 Java 구성 요소도 만들어야 합니다.

또한 Storm 클러스터는 분산된 형태로 실행되므로 Python 구성 요소에 필요한 모든 모듈이 클러스터의 모든 작업자 노드에서 사용 가능한지 확인해야 합니다. Storm은 다중 언어 리소스에 대해 이 작업을 수행하는 간단한 방법을 제공하지 않으므로 토폴로지에 대한 jar 파일의 일부로 모든 종속성을 포함하거나 클러스터의 각 작업자 노드에 종속성을 직접 설치해야 합니다.

###Java 및 Clojure 토폴로지 정의

토폴로지를 정의하는 두 방법 중에서 Clojure는 토폴로지 정의에서 python 구성 요소를 직접 참조할 수 있으므로 가장 쉽고/명확한 방법입니다. Java 기반 토폴로지 정의인 경우, Python 구성 요소에서 반환되는 튜플에 필드 선언 등을 처리하는 Java 구성 요소도 정의해야 합니다.

두 방법 모두 이 문서에서 예제 프로젝트와 함께 설명됩니다.

##Java 토폴로지를 포함하는 Python 구성 요소

> [AZURE.NOTE] 이 예제는 __JavaTopology__ 디렉터리의 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)에서 사용할 수 있습니다. Maven 기반 프로젝트입니다. Maven에 대해 잘 모르는 경우 Storm 토폴로지에 대한 Maven 프로젝트를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm으로 Java 기반 토폴로지 개발](hdinsight-storm-develop-java-topology.md)을 참조하세요.

Python(또는 기타 JVM 언어 구성 요소)을 사용하는 Java 기반 토폴로지는 Java 구성 요소를 사용하는 것으로 처음에 나타나지만 각 Java Spout/Bolt를 들여다 보면 다음과 유사한 코드가 표시됩니다.

    public SplitBolt() {
        super("python", "countbolt.py");
    }

여기서 Java는 Python을 호출하고 실제 Bolt 논리가 포함된 스크립트를 실행합니다. 이 예에서 Java Spout/Bolt는 기본 Python 구성 요소가 내보내는 튜플에 필드를 선언하기만 합니다.

실제 Python 파일은 이 예에서 `/multilang/resources` 디렉터리에 저장됩니다. `/multilang` 디렉터리는 __pom.xml__에서 참조됩니다.

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

여기에는 이 프로젝트에서 작성될 jar의 `/multilang` 폴더에 있는 모든 파일이 포함됩니다.

> [AZURE.IMPORTANT] 여기서는 `/multilang/resources`가 아닌 `/multilang` 디렉터리만 지정합니다. Storm은 `resources` 디렉터리에서 비JVM 리소스를 필요로 하므로 내부적으로 기존 항목을 검색합니다. 이 폴더에 구성 요소를 배치하면 Java 코드에서 이름만으로도 참조할 수 있습니다. 예: `super("python", "countbolt.py");` 이에 대한 다른 생각은 다중 언어 리소스에 액세스할 때 `resources` 디렉터리를 루트(/)로 여긴다는 점입니다.
>
> 이 예제 프로젝트에서 `storm.py` 모듈은 `/multilang/resources` 디렉터리에 포함됩니다.

###프로젝트 빌드 및 실행

이 프로젝트를 로컬로 실행하려면 다음 Maven 명령을 사용하여 로컬 모드에서 빌드 및 실행합니다.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Ctrl + c를 사용하여 해당 프로세스를 중지합니다.

Apache Storm을 실행하는 HDInsight 클러스터에\\로 프로젝트를 배포하려면 다음 단계를 수행합니다.

1. uber jar을 빌드합니다.

        mvn package

    그러면 __WordCount--1.0-SNAPSHOT.jar__이라는 파일이 이 프로젝트에 대한 `/target` 디렉터리에 만들어집니다.

2. 다음 방법 중 하나를 사용하여 Hadoop 클러스터에 jar 파일을 업로드합니다.

    * __Linux 기반__ HDInsight 클러스터: `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar`을 사용하여 jar 파일을 클러스터로 복사합니다. 이때 USERNAME은 SSH 사용자 이름으로, CLUSTERNAME은 HDInsight 클러스터 이름으로 바꿉니다.

        파일 업로드가 완료되면 SSH를 사용하여 클러스터에 연결하고 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`를 사용하여 토폴로지를 시작합니다.

    * __Windows 기반__ HDInsight 클러스터: 브라우저에서 HTTPS://CLUSTERNAME.azurehdinsight.net/으로 이동하여 Storm 대시보드에 연결합니다. CLUSTERNAME을 HDInsight 클러스터 이름으로 바꾸고 메시지가 표시되면 관리자 이름 및 암호를 제공합니다.

        양식을 사용하여 다음 작업을 수행합니다.

        * __Jar 파일__: __찾아보기__를 선택한 후 __WordCount-1.0-SNAPSHOT.jar__을 선택합니다.
        * __클래스 이름__: `com.microsoft.example.WordCount`를 입력합니다.
        * __추가 매개 변수__: `wordcount`와 같은 토폴로지를 식별하는 친숙한 이름을 입력합니다.

        마지막으로, __제출__을 선택하여 토폴로지를 시작합니다.

> [AZURE.NOTE] 토폴로지가 시작되면 Storm 토폴로지가 중지될 때까지 실행됩니다. 토폴로지를 중지하려면 명령줄(예를 들어 Linux 클러스터에 대한 SSH 세션)에서 `storm kill TOPOLOGYNAME` 명령을 사용하거나 Storm UI를 사용하여 토폴로지를 선택한 후 __중지__ 단추를 선택합니다.

##Clojure 토폴로지를 포함하는 Python 구성 요소

> [AZURE.NOTE] 이 예제는 __ClojureTopology__ 디렉터리의 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)에서 사용할 수 있습니다.

이 토폴로지는 [새 Clojure 프로젝트를 만드는](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project) [Leiningen](http://leiningen.org)을 사용하여 만듭니다. 이후, 스캐폴드 프로젝트에 수정된 내용은 다음과 같습니다.

* __project.clj__: Storm에 대한 종속성과 HDInsight 서버에 배포할 때 문제를 일으킬 수 있는 항목에 대한 제외 사항이 추가되었습니다.
* __resources/resources__: Leiningen에서 기본 `resources` 디렉터리를 생성하지만 여기에 저장된 파일이 이 프로젝트에서 생성된 jar 파일의 루트에 추가된 것으로 나타나며 Storm은 `resources`라는 하위 디렉터리의 파일을 필요로 합니다. 따라서 하위 디렉터리가 추가되고 Python 파일이 `resources/resources`에 저장됩니다. 런타임 시 이 디렉터리는 Python 구성 요소에 액세스하기 위한 루트(/)로 처리됩니다.
* __src/wordcount/core.clj__: 이 파일은 토폴로지 정의를 포함하며 __project.clj__ 파일에서 참조됩니다. Clojure를 사용하여 Storm 토폴로지를 정의하는 방법에 대한 자세한 내용은 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)을 참조하세요.

###프로젝트 빌드 및 실행

__프로젝트를 로컬로 빌드 및 실행하려면__ 다음 명령을 사용합니다.

    lein clean, run

토폴로지를 중지하려면 __Ctrl+C__를 사용합니다.

__uberjar을 빌드하고 HDInsight에 배포하려면__ 다음 단계를 수행합니다.

1. 토폴로지 및 필요한 종속성을 포함하는 uberjar을 만듭니다.

        lein uberjar

    `target\uberjar+uberjar` 디렉터리에 `wordcount-1.0-SNAPSHOT.jar`이라는 새 파일이 생성됩니다.
    
2. 다음 방법 중 하나를 사용하여 토폴로지를 HDInsight 클러스터에 배포하고 실행합니다.

    * __Linux 기반 HDInsight__
    
        1. `scp`를 사용하여 파일을 HDInsight 클러스터 헤드 노드에 복사합니다. 예:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            USERNAME을 클러스터에 대한 SSH 사용자로 바꾸고 CLUSTERNAME을 HDInsight 클러스터의 이름으로 바꿉니다.
            
        2. 파일이 클러스터에 복사된 후에는 SSH를 사용하여 클러스터에 연결하고 작업을 제출합니다. HDInsight에서 SSH 사용에 대한 자세한 내용은 다음을 참조하세요.
        
            * [Linux, Unix 또는 OS X에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Windows에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. 연결되면 다음을 사용하여 토폴로지를 시작합니다.
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __Windows 기반 HDInsight__
    
        1. 브라우저에서 HTTPS://CLUSTERNAME.azurehdinsight.net/으로 이동하여 Storm 대시보드에 연결합니다. CLUSTERNAME을 HDInsight 클러스터 이름으로 바꾸고 메시지가 표시되면 관리자 이름 및 암호를 제공합니다.

        2. 양식을 사용하여 다음 작업을 수행합니다.

            * __Jar 파일__: __찾아보기__를 선택한 후 __wordcount-1.0-SNAPSHOT.jar__을 선택합니다.
            * __클래스 이름__: `wordcount.core`를 입력합니다.
            * __추가 매개 변수__: `wordcount`와 같은 토폴로지를 식별하는 친숙한 이름을 입력합니다.

            마지막으로, __제출__을 선택하여 토폴로지를 시작합니다.

> [AZURE.NOTE] 토폴로지가 시작되면 Storm 토폴로지가 중지될 때까지 실행됩니다. 토폴로지를 중지하려면 명령줄(Linux 클러스터에 대한 SSH 세션)에서 `storm kill TOPOLOGYNAME` 명령을 사용하거나 Storm UI를 사용하여 토폴로지를 선택한 후 __중지__ 단추를 선택합니다.

##다음 단계

이 문서에서는 Storm 토폴로지에서 Python 구성 요소를 사용하는 방법을 알아보았습니다. HDInsight와 함께 Python을 사용하는 다른 방법은 다음 문서를 참조하세요.

* [MapReduce 작업을 스트리밍하는 데 Python을 사용하는 방법](hdinsight-hadoop-streaming-python.md)
* [Pig 및 Hive에서 UDF(사용자 정의 함수)를 사용하는 방법](hdinsight-python.md)

<!---HONumber=AcomDC_0914_2016-->