<properties
	pageTitle="Microsoft Avro Library로 데이터 직렬화 | Microsoft Azure"
	description="Azure HDInsight에서 Avro를 사용하여 빅 데이터를 직렬화하는 방법에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian" 
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="jgao"/>


# Microsoft Avro 라이브러리로 Hadoop의 데이터 직렬화

이 항목에서는 <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro 라이브러리</a>를 사용하여 개체 및 기타 데이터 구조를 메모리, 데이터베이스 또는 파일에 보관하기 위해 스트림으로 직렬화하는 방법과 역직렬화하여 원본 개체를 복구하는 방법을 보여 줍니다.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a>는 Microsoft. NET 환경을 위한 Apache Avro 데이터 직렬화 시스템을 구현합니다. Apache Avro는 직렬화를 위한 압축 이진 데이터 교환 형식을 제공합니다. 또한 <a href="http://www.json.org" target="_blank">JSON</a>을 사용하여 언어 상호 운용성을 따르는 언어 중립적 스키마를 정의합니다. 한 언어로 직렬화된 데이터는 다른 언어로 읽을 수 있습니다. 현재 C, C++, C#, Java, PHP, Python 및 Ruby가 지원됩니다. 이 형식에 대한 자세한 내용은 <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro 사양</a>(영문)에서 확인할 수 있습니다. 현재 Microsoft Avro 라이브러리 버전에서는 이 사양의 RPC(원격 프로시저 호출)를 지원하지 않습니다.

Avro 시스템에서 직렬화된 개체의 표현은 스키마 및 실제 값 부분으로 구성됩니다. Avro 스키마는 JSON을 사용하여 직렬화된 데이터의 언어 독립적 데이터 모델을 설명합니다. 이 스키마는 데이터의 이진 표현 옆에 나란히 표시됩니다. 스키마를 이진 표현과 구분하면 값별로 오버헤드가 발생하지 않고 각 개체를 쓸 수 있으므로 직렬화는 빨라지고 표현이 차지하는 공간은 줄어듭니다.

##Hadoop 시나리오
Apache Avro 직렬화 형식은 Azure HDInsight 및 기타 Apache Hadoop 환경에서 널리 사용됩니다. Avro는 Hadoop MapReduce 작업 내에서 복잡한 데이터 구조를 나타내는 편리한 방법을 제공합니다. Avro 파일(Avro 개체 컨테이너 파일)의 형식은 분산 MapReduce 프로그래밍 모델을 지원하도록 디자인되었습니다. 이러한 분산을 가능하게 하는 핵심 기능은 파일이 “분할 가능"하여 파일의 임의 지점을 찾고 특정 블록부터 읽기 시작할 수 있다는 데 있습니다.

##Avro 라이브러리의 직렬화
.NET Library for Avro는 다음과 같은 두 가지 방식으로 개체를 직렬화할 수 있도록 지원합니다.

- **리플렉션** - 해당 형식에 대한 JSON 스키마는 직렬화될 수 있게 .NET 형식의 데이터 계약 특성에서 자동으로 빌드됩니다.
- **제네릭 레코드**:JSON 스키마는 직렬화할 데이터의 스키마를 설명하는 .NET 형식이 존재하지 않을 때 [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx)(영문) 클래스로 표현되는 레코드에 명시적으로 지정됩니다.

데이터 스키마가 스트림의 기록기 및 판독기 둘 다로 알려져 있으면 데이터를 스키마 없이 전송할 수 있습니다. Avro 개체 컨테이너 파일이 사용되는 경우 스키마는 파일 내에 저장됩니다. 데이터 압축에 사용되는 코덱과 같은 기타 매개 변수를 지정할 수 있습니다. 이러한 시나리오는 아래 코드 예에 좀 더 자세히 설명되어 있습니다.


## Avro 라이브러리 설치

라이브러리를 설치하기 전에 필요한 사항은 다음과 같습니다.

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a>(6.0.4 이상)

Newtonsoft.Json.dll 종속성은 Microsoft Avro 라이브러리의 설치와 함께 자동으로 다운로드됩니다. 이 절차는 다음 섹션에 제공됩니다.


Microsoft Avro 라이브러리는 다음 절차를 사용하여 Visual Studio에서 설치할 수 있는 NuGet 패키지로 배포됩니다.

1. **프로젝트** 탭 -> **NuGet 패키지 관리...**를 선택합니다.
2. **온라인 검색** 상자에서 "Microsoft.Hadoop.Avro"를 검색합니다.
3. **Microsoft Azure HDInsight Avro Library** 옆의 **설치** 단추를 클릭합니다.

Newtonsoft.Json.dll(>= 6.0.4) 종속성은 Microsoft Avro 라이브러리와 함께 자동으로 다운로드됩니다.

<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro 라이브러리 홈 페이지</a>를 방문하여 현재 릴리스 정보를 확인할 수 있습니다.


Microsoft Avro 라이브러리 소스 코드는 <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro 라이브러리 홈 페이지</a>에 있습니다.

##Avro 라이브러리를 사용하여 스키마 컴파일

Microsoft Avro 라이브러리에는 이전에 정의된 JSON 스키마에 따라 자동으로 C# 형식을 만들 수 있는 코드 생성 유틸리티가 있습니다. 코드 생성 유틸리티는 이진 실행 파일로 배포되지 않지만, 다음 절차에 따라 쉽게 빌드할 수 있습니다.

1. <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK For Hadoop</a>에서 HDInsight SDK 소스 코드의 최신 버전이 포함된 .zip 파일을 다운로드합니다. (**다운로드** 탭이 아니라 **다운로드** 아이콘을 클릭합니다.)

2. 필요한 종속성 NuGet 패키지를 다운로드하기 위해 .NET Framework 4가 설치되고 인터넷에 연결된 컴퓨터에 있는 디렉터리에 HDInsight SDK의 압축을 풉니다. 여기서는 소스 코드가 C:\\SDK에 추출된다고 간주합니다.

3. C:\\SDK\\src\\Microsoft.Hadoop.Avro.Tools 폴더로 이동하고 build.bat를 실행합니다. (이 파일은 .NET Framework 32비트 배포에서 MSBuild를 호출합니다. 64비트 버전을 사용하려는 경우 파일 내부의 주석에 따라 build.bat를 편집합니다.) 성공적으로 빌드되었는지 확인합니다. (일부 시스템에서는 MSBuild로 인해 경고가 발생할 수 있습니다. 빌드 오류가 없으면 이러한 경고가 유틸리티에 영향을 미치지 않습니다.)

4. 컴파일된 유틸리티는 C:\\SDK\\Bin\\Unsigned\\Release\\Microsoft.Hadoop.Avro.Tools에 있습니다.


명령줄 구문에 익숙해지려면 코드 생성 유틸리티가 있는 폴더에서 다음 명령을 실행합니다. `Microsoft.Hadoop.Avro.Tools help /c:codegen`

유틸리티를 테스트하기 위해 소스 코드와 함께 제공되는 샘플 JSON 스키마 파일에서 C# 클래스를 생성할 수 있습니다. 다음 명령을 실행합니다.

	Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

그러면 현재 디렉터리에 두 개의 C# 파일, 즉 SensorData.cs와 Location.cs가 생성됩니다.

JSON 스키마를 C# 형식으로 변환하는 동안 코드 생성 유틸리티에서 사용되는 논리를 이해하려면 C:\\SDK\\src\\Microsoft.Hadoop.Avro.Tools\\Doc에 있는 GenerationVerification.feature를 참조하세요.

네임스페이스는 이전 단락에서 언급한 파일에 설명된 논리를 사용하여 JSON 스키마에서 추출됩니다. 스키마에서 추출된 네임스페이스는 유틸리티 명령줄에서 /n 매개 변수로 제공되는 항목보다 우선 적용됩니다. 스키마 내에 포함된 네임스페이스를 재정의하려면 /nf 매개 변수를 사용합니다. 예를 들어 SampleJSONSchema.avsc에서 모든 네임스페이스를 my.own.nspace로 변경하려면 다음 명령을 실행합니다.

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## 샘플
이 항목에 제공되는 여섯 가지 예제는 Microsoft Avro 라이브러리에서 지원되는 다양한 시나리오를 보여 줍니다. Microsoft Avro 라이브러리는 어떤 스트림에서도 작동하도록 디자인되었습니다. 이러한 예에서는 간편성과 일관성을 위해 데이터가 파일 스트림이나 데이터베이스가 아닌 메모리 스트림을 사용하여 처리됩니다. 프로덕션 환경에서 수행하는 방법은 정확한 시나리오 요구 사항, 데이터 소스와 볼륨, 성능 제약 조건 및 기타 요인에 따라 좌우됩니다.

처음 2개 예는 리플렉션과 제네릭 레코드를 사용하여 데이터를 메모리 스트림 버퍼로 직렬화 및 역직렬화하는 방법을 보여 줍니다. 이 두 가지 경우의 스키마는 판독기와 기록기 간에 공유되는 것으로 간주됩니다.

세 번째 및 네 번째 예는 Avro 개체 컨테이너 파일을 사용하여 데이터를 직렬화 및 역직렬화하는 방법을 보여 줍니다. 데이터가 Avro 컨테이너 파일에 저장되는 경우 역직렬화를 위해 스키마를 공유해야 하므로 스키마도 항상 이 파일에 저장됩니다.

처음 4개의 예를 포함하는 샘플은 <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">Azure 코드 샘플</a> 사이트에서 다운로드할 수 있습니다.

5번째 예는 Avro 개체 컨테이너 파일에 대해 사용자 지정 압축 코덱을 사용하는 방법을 보여 줍니다. 이 예에 대한 코드를 포함하는 샘플은 <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">Azure 코드 샘플</a>(영문) 사이트에서 다운로드할 수 있습니다.

6번째 샘플은 Avro 직렬화를 사용하여 데이터를 Azure Blob 저장소에 업로드하고 HDInsight(Hadoop) 클러스터에서 Hive를 사용하여 데이터를 분석하는 방법을 보여 줍니다. <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Azure 코드 샘플</a> 사이트에서 다운로드할 수 있습니다.

이 항목에서 설명된 6개 샘플의 링크는 다음과 같습니다.

 * <a href="#Scenario1">**리플렉션을 사용한 직렬화**</a> - 직렬화할 형식에 대한 JSON 스키마는 데이터 계약 특성에서 자동으로 빌드됩니다.
 * <a href="#Scenario2">**제네릭 레코드로 직렬화**</a> - JSON 스키마는 리플렉션에 사용할 수 있는 .NET 형식이 없을 때 레코드에 명시적으로 지정됩니다.
 * <a href="#Scenario3">**리플렉션과 함께 개체 컨테이너 파일을 사용하여 직렬화**</a> - JSON 스키마는 자동으로 빌드되고 Avro 개체 컨테이너 파일을 통해 직렬화된 데이터와 함께 공유됩니다.
 * <a href="#Scenario4">**제네릭 레코드와 함께 개체 컨테이너 파일을 사용하여 직렬화**</a> - JSON 스키마는 직렬화되기 전에 명시적으로 지정되고 Avro 개체 컨테이너 파일을 통해 데이터와 함께 공유됩니다.
 * <a href="#Scenario5">**사용자 지정 압축 코덱과 함께 개체 컨테이너 파일을 사용하여 직렬화**</a> - 이 예에서는 수축 데이터 압축 코덱의 .NET 구현을 사용자 지정하여 Avro 개체 컨테이너 파일을 만드는 방법을 보여 줍니다.
 * <a href="#Scenario6">**Avro를 사용하여 Microsoft Azure HDInsight 서비스에 대한 데이터 업로드**</a> - 이 예에서는 Avro 직렬화가 HDInsight 서비스와 상호 작용하는 방법을 보여 줍니다. 이 예를 실행하려면 활성 Azure 구독 및 Azure HDInsight 클러스터에 대한 액세스 권한이 필요합니다.

###<a name="Scenario1"></a>샘플 1: 리플렉션을 사용한 직렬화

해당 형식에 대한 JSON 스키마는 직렬화될 수 있게 C# 개체의 데이터 계약 특성을 통한 리플렉션을 사용하여 Microsoft Avro 라이브러리에서 자동으로 빌드될 수 있습니다. Microsoft Avro 라이브러리에서는 [**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx)를 만들어 직렬화할 필드를 식별합니다.

이 예에서 개체(멤버 **Location** 구조체를 갖는**SensorData** 클래스)가 메모리 스트림으로 직렬화된 후 이 스트림이 역직렬화됩니다. 그런 다음 초기 인스턴스와 결과를 비교하여 복구된 **SensorData** 개체가 원본과 동일한지 확인합니다.

이 예의 스키마는 Avro 개체 컨테이너 형식이 필요 없도록 판독기와 기록기 간에 공유되는 것으로 간주됩니다. 스키마를 데이터와 공유해야 할 때 개체 컨테이너 형식과 리플렉션을 사용하여 데이터를 메모리 버퍼로 직렬화 및 역직렬화하는 방법의 예를 보려면 <a href="#Scenario3">개체 컨테이너 파일과 리플렉션을 사용한 직렬화</a>를 참조하세요.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
		using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###샘플 2: 제네릭 레코드로 직렬화

.NET 클래스와 데이터 계약을 사용하여 데이터를 표현할 수 없으므로 리플렉션을 사용할 수 없는 경우 제네릭 레코드에 JSON 스키마를 명시적으로 지정할 수 있습니다. 일반적으로 이 방법은 리플렉션 사용보다 더 느립니다. 이러한 경우 데이터의 스키마가 동적이므로 컴파일 시간에 알려지지 않을 수 있습니다. 해당 스키마가 런타임에 Avro 형식으로 변환될 때까지 알려지지 않는 CSV(쉼표로 구분한 값) 파일로 표현된 데이터가 이러한 종류의 동적 시나리오의 예입니다.

이 예에서는 [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx)(영문)를 만들고 사용하여 JSON 스키마를 명시적으로 지정하는 방법과 데이터로 채운 후 직렬화 및 역직렬화하는 방법을 보여 줍니다. 그런 다음 초기 인스턴스와 결과를 비교하여 복구된 레코드가 원본과 동일한지 확인합니다.

이 예의 스키마는 Avro 개체 컨테이너 형식이 필요 없도록 판독기와 기록기 간에 공유되는 것으로 간주됩니다. 스키마를 직렬화된 데이터에 포함해야 할 경우 제네릭 레코드와 개체 컨테이너 형식을 사용하여 데이터를 메모리 버퍼로 직렬화 및 역직렬화하는 방법의 예를 보려면 <a href="#Scenario4">개체 컨테이너 파일과 제네릭 레코드를 사용한 직렬화</a>를 참조하세요.


	namespace Microsoft.Hadoop.Avro.Sample
	{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
	using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
	}
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###샘플 3: 개체 컨테이너 파일을 사용한 직렬화 및 리플렉션을 사용한 직렬화

이 예는 리플렉션을 통해 스키마를 내재적으로 지정하는 <a href="#Scenario1"> 첫 번째 예</a>의 시나리오와 비슷합니다. 여기에서는 역직렬화하는 판독기에서 스키마를 알지 못한다는 점이 다릅니다. 직렬화할**SensorData** 개체와 암시적으로 지정된 해당 스키마는 [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) 클래스로 표현되는 개체 컨테이너 파일에 저장됩니다.

이 예에서 데이터는 [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx)를 사용하여 직렬화되고 [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx)를 사용하여 역직렬화됩니다. 그런 후 초기 인스턴스와 결과를 비교하여 ID를 확인합니다.

개체 컨테이너 파일의 데이터는 .NET Framework 4의 기본 [**Deflate**][deflate-100] 압축 코덱을 사용하여 압축됩니다. .NET Framework 4.5에 제공되는 더 나은 최신 버전의 [**Deflate**][deflate-110] 압축 코덱을 사용하는 방법은 이 항목의 <a href="#Scenario5"> 다섯 번째 예</a>를 참조하세요.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
		using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file "{0}"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file "{0}". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file "{0}"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file "{0}"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file "{0}". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###샘플 4: 개체 컨테이너 파일을 사용한 직렬화 및 일반 레코드를 사용한 직렬화

이 예는 JSON을 사용하여 명시적으로 스키마를 지정하는 <a href="#Scenario2"> 두 번째 예</a>의 시나리오와 비슷합니다. 여기에서는 역직렬화하는 판독기에서 스키마를 알지 못한다는 점이 다릅니다.

테스트 데이터 집합은 명시적으로 정의된 JSON 스키마를 사용하여 [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) 개체 목록으로 수집된 후 [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) 클래스로 표시되는 개체 컨테이너 파일에 저장됩니다. 이 컨테이너 파일은 압축 해제된 데이터를 메모리 스트림으로 직렬화하여 파일에 저장하는 데 사용되는 기록기를 만듭니다. 판독기를 만드는 데 사용한 [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) 매개 변수를 통해 이 데이터가 압축되지 않음을 지정합니다.

그런 후 파일에서 이 데이터를 읽은 다음 개체 컬렉션으로 역직렬화합니다. 이 컬렉션은 Avro 레코드 초기 목록과 비교되어 동일한지 확인됩니다.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
		using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file "{0}"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file "{0}". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file "{0}"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file "{0}"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file "{0}". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###샘플 5: 사용자 지정 압축 코덱과 함께 개체 컨테이너 파일을 사용한 직렬화

5번째 예는 Avro 개체 컨테이너 파일에 대해 사용자 지정 압축 코덱을 사용하는 방법을 보여 줍니다. 이 예에 대한 코드를 포함하는 샘플은 [Azure 코드 샘플](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111)(영문) 사이트에서 다운로드할 수 있습니다.

[Avro 사양](http://avro.apache.org/docs/current/spec.html#Required+Codecs)에서는 기본값인 **Null** 및 **Deflate** 외에도 선택적 압축 코덱을 사용할 수 있게 허용합니다. 이 예는 Snappy([Avro 사양](http://avro.apache.org/docs/current/spec.html#snappy)에 지원되는 선택적 코덱으로 명시) 같은 완전히 새로운 코덱을 구현하지 않습니다. 여기서는 기본 .NET Framework 4 버전보다 더 나은 [zlib](http://zlib.net/) 압축 라이브러리 기반의 압축 알고리즘을 제공하는 .NET Framework 4.5에서 구현된 [**Deflate**][deflate-110] 코덱 사용 방법을 보여줍니다.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
		using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file "{0}"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file "{0}". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file "{0}"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file "{0}"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file "{0}". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###샘플 6: Avro를 사용하여 Microsoft Azure HDInsight 서비스에 대한 데이터 업로드

6번째 예는 Azure HDInsight 서비스 조작과 관련된 몇 가지 프로그래밍 기법을 보여줍니다. 이 예에 대한 코드를 포함하는 샘플은 [Azure 코드 샘플](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3)(영문) 사이트에서 다운로드할 수 있습니다.

이 샘플은 다음을 수행합니다.

* 기존 HDInsight 서비스 클러스터에 연결합니다.
* 여러 CSV 파일을 직렬화하고 결과를 Azure Blob 저장소에 업로드합니다. (CSV 파일은 샘플과 함께 배포되고 1970-2010 기간에 [Infochimps](http://www.infochimps.com/)로 배포된 AMEX 증권 기록 데이터의 추출을 나타냅니다. 이 샘플은 CSV 파일 데이터를 읽고, 레코드를 **Stock** 클래스 인스턴스로 변환하고, 리플렉션을 사용하여 직렬화합니다. Stock 형식 정의는 Microsoft Avro 라이브러리 코드 생성 유틸리티를 사용하여 JSON 스키마에서 만들어집니다.
* Hive에서 **Stocks**라는 새 외부 테이블을 만들고 이전 단계에서 업로드한 데이터에 연결합니다.
* Hive를 사용하여 **Stocks** 테이블에 대해 쿼리를 실행합니다.

또한 주요 작업을 수행하기 전과 수행한 후에 정리 절차를 수행합니다. 정리하는 동안 모든 관련된 Azure Blob 데이터 및 폴더가 제거되고 Hive 테이블이 삭제됩니다. 샘플 명령줄에서 정리 프로시저를 호출할 수도 있습니다.

이 샘플을 사용하려면 다음 필수 조건이 필요합니다.

* 활성 Microsoft Azure 구독 및 해당 구독 ID입니다.
* 해당 개인 키가 포함된 구독에 대한 관리 인증서입니다. 인증서는 샘플을 실행하는 데 사용되는 컴퓨터에 있는 현재 사용자 개인 저장소에 설치해야 합니다.
* 활성 HDInsight 클러스터입니다.
* 해당 주 또는 보조 액세스 키와 함께 이전 필수 조건에서 HDInsight 클러스터에 연결된 Azure 저장소 계정입니다.

샘플을 실행하기 전에 샘플 구성 파일에 필수 조건의 모든 정보를 입력해야 합니다. 입력하는 방법에는 다음 두 가지가 있습니다.

* 샘플 루트 디렉터리에서 app.config 파일을 편집하고 샘플 빌드
* 먼저 샘플을 빌드하고 빌드 디렉터리에서 AvroHDISample.exe.config 편집

두 경우 모두, 모든 편집을 **<appSettings>** 설정 섹션에서 수행해야 합니다. 파일의 주석을 따르세요. 샘플을 실행하려면 명령줄에서 다음 명령을 실행합니다. 여기서는 샘플이 포함된 ZIP 파일은 C:\\AvroHDISample로 추출된다고 간주했고, 이외의 경우에는 상대 파일 경로를 사용합니다.

    AvroHDISample run C:\AvroHDISample\Data

클러스터를 정리하려면 다음 명령을 실행합니다.

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx

<!---HONumber=AcomDC_0914_2016-->