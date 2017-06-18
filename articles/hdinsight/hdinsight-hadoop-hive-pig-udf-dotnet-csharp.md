<properties
	pageTitle="HDInsight의 Hadoop에서 Hive 및 Pig와 함께 C# 사용 | Microsoft Azure"
	description="Azure HDInsight에서 Hive 및 Pig 스트림과 함께 C# UDF(사용자 정의 함수)를 사용하는 방법에 대해 알아봅니다."
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
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>


#HDInsight에서 Hive 및 Pig 스트림과 함께 C# UDF(사용자 정의 함수) 사용

Hive 및 Pig는 Azure HDInsight의 데이터 작업에 적합하지만 보다 일반적인 언어가 필요한 경우도 있습니다. Hive 및 Pig 모두 사용자 정의 함수(UDF) 또는 스트리밍을 통해 외부 코드를 호출할 수 있습니다.

이 문서에서 Hive 및 Pig와 함께 C#를 사용 하는 방법에 대해 알아봅니다.

##필수 조건

* Windows 7 이후 버전

* 다음 버전의 Visual Studio:

	* Visual Studio 2012 Professional/Premium/Ultimate [업데이트 4](http://www.microsoft.com/download/details.aspx?id=39305)

	* Visual Studio 2013 Community/Professional/Premium/Ultimate [업데이트 4](https://www.microsoft.com/download/details.aspx?id=44921)

	* Visual Studio 2015

* HDInsight 클러스터의 Hadoop - 클러스터를 만드는 단계는 [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

* Visual Studio용 Hadoop 도구 도구 설치 및 구성에 대한 단계는 [Visual Studio용 HDInsight Hadoop 도구를 사용하여 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

##HDInsight에서.NET

.NET 공용 언어 런타임(CLR) 및 프레임 워크는 Windows 기반 HDInsight 클러스터에 기본적으로 설치됩니다. 이렇게 하면 Hive 및 Pig 스트리밍과 함께 C# 응용 프로그램을 사용할 수 있습니다 (stdout/stdin을 통해 Hive/Pig 및 C# 응용 프로그램 간 데이터가 전달됨).

현재 Linux 기반 HDInsight 클러스터에서 .NET Framework 응용 프로그램 실행에 대 한 지원은 없습니다.

##.NET 및 스트리밍

스트리밍은 stdout을 통해 외부 응용 프로그램에 데이터를 전달하고 stdin을 통해 결과를 받는 Hive 및 Pig 데이터를 포함합니다. C# 응용 프로그램의 경우, `Console.ReadLine()` 및 `Console.WriteLine()`을 통해 가장 쉽게 수행됩니다.

Hive 및 Pig가 런타임에 응용 프로그램을 호출해야 하므로 **콘솔 응용 프로그램** 템플릿은 C# 프로젝트에 대해 사용되어야 합니다.

##Hive 및 C&#35;

###C# 프로젝트 만들기

1. Visual Studio를 열고 새 솔루션을 만듭니다. 프로젝트 형식의 경우, **콘솔 응용 프로그램**을 선택하고 새 프로젝트의 이름을 **HiveCSharp**로 지정합니다.

2. **Program.cs**의 내용을 다음으로 바꿉니다.

        using System;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;

        namespace HiveCSharp
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Parse the string, trimming line feeds
                        // and splitting fields at tabs
                        line = line.TrimEnd('\n');
                        string[] field = line.Split('\t');
                        string phoneLabel = field[1] + ' ' + field[2];
                        // Emit new data to stdout, delimited by tabs
                        Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                    }
                }
                /// <summary>
                /// Returns an MD5 hash for the given string
                /// </summary>
                /// <param name="input">string value</param>
                /// <returns>an MD5 hash</returns>
                static string GetMD5Hash(string input)
                {
                    // Step 1, calculate MD5 hash from input
                    MD5 md5 = System.Security.Cryptography.MD5.Create();
                    byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                    byte[] hash = md5.ComputeHash(inputBytes);

                    // Step 2, convert byte array to hex string
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < hash.Length; i++)
                    {
                        sb.Append(hash[i].ToString("x2"));
                    }
                    return sb.ToString();
                }
            }
        }

3. 프로젝트를 빌드합니다.

###저장소에 업로드

1. Visual Studio에서 **서버 탐색기**를 엽니다.

3. **Azure**를 확장한 다음 **HDInsight**를 확장합니다.

4. 메시지가 표시되면 Azure 구독 자격 증명을 입력한 다음 **로그인**을 클릭합니다.

5. 이 응용 프로그램을 배포하려는 HDInsight 클러스터를 확장한 다음 **기본 저장소 계정**을 확장합니다.

	![클러스터에 대한 저장소 계정을 보여주는 서버 탐색기](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. 클러스터의 경우 **기본 컨테이너**를 두 번 클릭합니다. 기본 컨테이너의 내용을 표시하는 새 창이 열립니다.

7. 업로드 아이콘을 클릭한 다음**HiveCSharp** 프로젝트에 대한 **bin\\debug** 폴더로 이동합니다. 마지막으로 **HiveCSharp.exe** 파일을 선택하고 **확인**을 클릭합니다.

	![업로드 아이콘](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. 업로드가 완료되면 Hive 쿼리에서 응용 프로그램을 사용할 수 있습니다.

###Hive 쿼리

1. Visual Studio에서 **서버 탐색기**를 엽니다.

2. **Azure**를 확장한 다음 **HDInsight**를 확장합니다.

5. **HiveCSharp** 응용 프로그램을 배포한 클러스터를 마우스 오른쪽 단추로 클릭하고 **Hive 쿼리 작성**을 선택합니다.

6. Hive 쿼리로 다음을 사용합니다.

		add file wasbs:///HiveCSharp.exe;

		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		USING 'HiveCSharp.exe' AS
		(clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

    `hivesampletable`에서 `clientid`, `devicemake` 및 `devicemodel` 필드를 선택하고 해당 필드를 HiveCSharp.exe 응용 프로그램으로 전달합니다. 쿼리는 응용 프로그램이 3개의 필드를 반환할 것을 예상하며 `clientid`, `phoneLabel` 및 `phoneHash`로 저장됩니다. 또한 쿼리는 기본 저장소 컨테이너(`add file wasbs:///HiveCSharp.exe`)의 루트에서 HiveCSharp.exe를 찾는다고 예상합니다.

5. **제출**을 클릭하여 HDInsight 클러스터에 작업을 제출합니다. **Hive 작업 요약** 창이 열립니다.

6. **새로 고침**을 클릭하여 **작업 상태**가 **Completed**로 변경될 때까지 요약을 새로 고칩니다. 작업 출력을 보려면 **작업 출력**을 클릭합니다.

##Pig 및 C&#35;

###C# 프로젝트 만들기

1. Visual Studio를 열고 새 솔루션을 만듭니다. 프로젝트 형식의 경우, **콘솔 응용 프로그램**을 선택하고 새 프로젝트의 이름을 **PigUDF**로 지정합니다.

2. **Program.cs** 파일의 내용을 다음으로 바꿉니다.

		using System;

		namespace PigUDF
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            string line;
				    // Read stdin in a loop
				    while ((line = Console.ReadLine()) != null)
		            {
		                // Fix formatting on lines that begin with an exception
		                if(line.StartsWith("java.lang.Exception"))
		                {
		                    // Trim the error info off the beginning and add a note to the end of the line
		                    line = line.Remove(0, 21) + " - java.lang.Exception";
		                }
		                // Split the fields apart at tab characters
		                string[] field = line.Split('\t');
		                // Put fields back together for writing
		                Console.WriteLine(String.Join("\t",field));
		            }
		        }
		    }
		}

	이 응용 프로그램은 Pig에서 보낸 줄을 구문 분석하고 `java.lang.Exception`로 시작하는 줄의 형식을 변경합니다.

3. **Program.cs**를 저장한 다음 프로젝트를 빌드합니다.

###응용 프로그램 업로드

1. Pig 스트리밍은 응용 프로그램이 클러스터 파일 시스템에서 로컬일 것이라고 예상합니다. HDInsight 클러스터용 원격 데스크톱을 사용하도록 설정한 다음, [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#rdp)의 지침에 따라 연결합니다.

2. 복사되면, 로컬 컴퓨터에서 PigUDF 프로젝트용 **bin/debug** 디렉터리에서 **PigUDF.exe**를 복사하고 클러스터의 **%PIG\_HOME%** 디렉터리에 붙여넣습니다.

###Pig Latin에서 응용 프로그램 사용

1. 원격 데스크톱 세션에서, 데스크톱의 **Hadoop 명령줄** 아이콘을 사용하여 Hadoop 명령줄을 시작합니다.

2. Pig 명령줄을 시작하려면 다음을 사용하세요.

		cd %PIG_HOME%
		bin\pig

	`grunt>` 프롬프트가 나타납니다.

3. .NET Framework 응용 프로그램을 사용하여 간단한 Pig 작업을 실행하려면 다음을 입력합니다.

		DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
		LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
		LOG = FILTER LOGS by LINE is not null;
		DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
		DUMP DETAILS;

	`DEFINE` 문은 pigudf.exe 응용 프로그램에 대한 `streamer`의 별칭을 만들고 `SHIP` 클러스터의 노드를 분산합니다. 나중에 `streamer`는 `STREAM` 연산자와 함께 사용되어 로그에 포함된 단일 줄을 처리하고 일련의 열로 데이터를 반환합니다.

> [AZURE.NOTE] 별칭이 지정된 경우 스트리밍에 사용되는 응용 프로그램 이름은 '(기호) 문자로 묶어야 하며 `SHIP`와 함께 사용된 경우 '(작은따옴표)로 묶어야 합니다.

3. 마지막 줄을 입력한 후 작업을 시작해야 합니다. 결국 다음과 유사한 출력이 반환됩니다.

		(2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
		(2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
		(2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##요약

이 문서에서는 HDInsight의 Hive 및 Pig에서 .NET Framework 응용 프로그램을 사용하는 방법에 대해 배웠습니다. Python을 Hive 및 Pig와 함께 사용하는 방법에 대해 알고 싶으면 [HDInsight에서 Hive 및 Pig와 함께 Python 사용](hdinsight-python.md)을 참조하세요.

Pig 및 Hive를 사용하고 MapReduce 사용에 대해 배우는 다른 방법은 다음을 참조하세요.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->