<properties 
	pageTitle="HBase를 사용하여 Twitter 데이터 실시간 분석 | Microsoft Azure" 
	description="이 문서에서는 HDInsight(Hadoop) 클러스터에서 HBase를 사용하여 Twitter의 빅 데이터를 실시간으로 데이터 분석하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2016" 
	ms.author="jgao"/>

# HDInsight에서 HBase를 사용하여 Twitter 데이터 실시간 분석

이 문서에서는 HDInsight(Hadoop) 클러스터에서 HBase를 사용하여 Twitter의 빅 데이터를 실시간으로 [데이터 분석](http://en.wikipedia.org/wiki/Sentiment_analysis)하는 방법에 대해 알아봅니다.


소셜 웹 사이트는 빅데이터 채택의 주요 추진력 중 하나입니다. Twitter와 같은 사이트에서 제공하는 공개 API는 대중적인 추세를 분석하고 이해하는 데 유용한 데이터 원본입니다. 이 자습서에는 다음을 수행하기 위한 콘솔 스트리밍 서비스 응용 프로그램과 ASP.NET 웹 응용 프로그램을 개발합니다.

![HDInsight HBase에서 Twitter 데이터 분석][img-app-arch]

- 스트리밍 응용 프로그램
	- Twitter 스트리밍 API를 사용하여 실시간으로 지역 태그가 적용된 트윗 가져오기
	- 이러한 트윗의 데이터 평가
	- Microsoft HBase SDK를 사용하여 HBase에 데이터 정보 저장
- Azure 웹 사이트 응용 프로그램
	- ASP.NET 웹 응용 프로그램을 사용하여 Bing 지도에 실시간 통계 결과를 그림으로 나타내기 트윗이 시각화된 모양은 다음과 같습니다.

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	특정 키워드를 사용해 트윗을 쿼리하여 트윗에 표현된 의견이 긍정적인지 부정적인지 아니면 중립적인지를 파악할 수 있습니다.

전체 Visual Studio 솔루션 샘플은 GitHub [실시간 소셜 데이터 분석 앱](https://github.com/maxluk/tweet-sentiment)에서 확인할 수 있습니다.































### 필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **HDInsight의 HBase 클러스터**. 클러스터 만들기에 대한 지침은 [HDInsight에서 Hadoop을 통해 HBase 사용 시작][hbase-get-started]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.


	<table border="1">
	<tr><th>클러스터 속성</th><th>설명</th></tr>
	<tr><td>HBase 클러스터 이름</td><td>HDInsight HBase 클러스터 이름입니다. 예를 들면 https://myhbase.azurehdinsight.net/과 같습니다.</td></tr>
	<tr><td>클러스터 사용자 이름</td><td>Hadoop 사용자 계정 이름입니다. 기본 Hadoop 사용자 이름은 <strong>admin</strong>입니다.</td></tr>
	<tr><td>클러스터 사용자 암호</td><td>Hadoop 클러스터 사용자 암호입니다.</td></tr>
	</table>

- Visual Studio 2013이 설치된 **워크스테이션**. 관련 지침은 [Visual Studio 설치](http://msdn.microsoft.com/library/e2h7fzkw.aspx)를 참조하세요.





## Twitter 응용 프로그램 ID 및 암호 만들기

Twitter 스트리밍 API는 [OAuth](http://oauth.net/)를 사용하여 요청 권한을 부여합니다. OAuth를 사용하는 첫 단계는 Twitter 개발자 사이트에서 새 응용 프로그램을 만드는 것입니다.

**Twitter 응용 프로그램 ID 및 암호를 만들려면**

1. [Twitter Apps](https://apps.twitter.com/)에 로그인합니다. Twitter 계정이 없는 경우 **Sign up now** 링크를 클릭합니다.
2. **Create New App**을 클릭합니다.
3. **Name**, **Description** 및 **Website**를 입력합니다. Twitter 응용 프로그램 이름은 고유해야 합니다. 웹 사이트 필드는 실제로 사용되지는 않으므로 유효한 URL을 입력하지 않아도 됩니다.
4. **Yes, I agree**를 선택한 후 **Create your Twitter application**을 클릭합니다.
5. **Permissions** 탭을 클릭합니다. 기본 권한은 **Read only**입니다. 이 자습서에는 이 권한이면 충분합니다.
6. **Keys and Access Tokens** 탭을 클릭합니다.
7. **Create my access token**을 클릭합니다.
8. 페이지의 오른쪽 위에서 **Test OAuth**를 클릭합니다.
9. **Consumer key**, **Consumer secret**, **Access token** 및 **Access token secret** 값을 복사합니다. 이러한 값은 자습서의 뒷부분에서 필요합니다.

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























## Twitter 스트리밍 서비스 만들기

트윗을 가져오고 트윗의 데이터 점수를 계산한 다음 처리된 트윗 단어를 HBase로 보내는 응용 프로그램을 만들어야 합니다.

**스트리밍 응용 프로그램을 만들려면**

1. **Visual Studio**를 열고 **TweetSentimentStreaming**이라는 Visual C# 콘솔 응용 프로그램을 만듭니다.
2. **패키지 관리자 콘솔**에서 다음 명령을 실행합니다.

		Install-Package Microsoft.HBase.Client -version 0.4.2.0
		Install-Package TweetinviAPI -version 1.0.0.0

	이러한 명령으로 [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) 패키지(HBase 클러스터에 액세스하기 위한 클라이언트 라이브러리) 및 [Tweetinvi API](https://www.nuget.org/packages/TweetinviAPI/) 패키지(Twitter API에 액세스하는 데 사용)를 설치합니다.

	> [AZURE.NOTE] 이 문서에서 사용된 샘플은 위에 지정 된 버전을 사용하여 테스트되었습니다. -version 스위치를 제거하면 최신 버전을 설치할 수 있습니다.

3. **솔루션 탐색기**에서 **System.Configuration**을 참조에 추가합니다.
4. 새로운 **HBaseWriter.cs** 클래스 파일을 프로젝트에 추가하고 다음과 같은 코드로 바꿉니다.

		using System;
		using System.Collections.Generic;
		using System.IO;
		using System.Linq;
		using System.Text;
		using System.Threading;
		using Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		using Microsoft.HBase.Client;
		using Tweetinvi.Models;

        namespace TweetSentimentStreaming
        {
            class HBaseWriter
            {
                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "https://<Enter Your Cluster Name>.azurehdinsight.net/";
                const string HADOOPUSERNAME = "admin"; //the default name is "admin"
                const string HADOOPUSERPASSWORD = "<Enter the Hadoop User Password>";

                const string HBASETABLENAME = "tweets_by_words";
				const string COUNT_ROW_KEY = "~ROWCOUNT";
				const string COUNT_COLUMN_NAME = "d:COUNT";
        		
				long rowCount = 0;

                // Sentiment dictionary file and the punctuation characters
                const string DICTIONARYFILENAME = @"..\..\dictionary.tsv";
                private static char[] _punctuationChars = new[] {
            ' ', '!', '"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

                // For writting to HBase
                HBaseClient client;

                // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
                Dictionary<string, DictionaryItem> dictionary;

                // use multithread write
                Thread writerThread;
                Queue<ITweet> queue = new Queue<ITweet>();
                bool threadRunning = true;

                // This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
                public HBaseWriter()
                {
                    ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
                    client = new HBaseClient(credentials);

                    // create the HBase table if it doesn't exist
                    if (!client.ListTablesAsync().Result.name.Contains(HBASETABLENAME))
                    {
                        TableSchema tableSchema = new TableSchema();
                        tableSchema.name = HBASETABLENAME;
                        tableSchema.columns.Add(new ColumnSchema { name = "d" });
						client.CreateTableAsync(tableSchema).Wait();
                        Console.WriteLine("Table "{0}" is created.", HBASETABLENAME);
                    }

					// Read current row count cell
            		rowCount = GetRowCount();

                    // Load sentiment dictionary from a file
                    LoadDictionary();

                    // Start a thread for writting to HBase
                    writerThread = new Thread(new ThreadStart(WriterThreadFunction));
                    writerThread.Start();
                }

                ~HBaseWriter()
                {
                    threadRunning = false;
                }

				private long GetRowCount()
				{
					try
					{
						RequestOptions options = RequestOptions.GetDefaultOptions();
						options.RetryPolicy = RetryPolicy.NoRetry;
						var cellSet = client.GetCellsAsync(HBASETABLENAME, COUNT_ROW_KEY, null, null, options).Result;
						if (cellSet.rows.Count != 0)
						{
							var countCol = cellSet.rows[0].values.Find(cell => Encoding.UTF8.GetString(cell.column) == COUNT_COLUMN_NAME);
							if (countCol != null)
							{
								return Convert.ToInt64(Encoding.UTF8.GetString(countCol.data));
							}
						}
					}
					catch(Exception ex)
					{
						if (ex.InnerException.Message.Equals("The remote server returned an error: (404) Not Found.", StringComparison.OrdinalIgnoreCase))
						{
							return 0;
						}
						else
						{
							throw ex;
						}
						
					}

					return 0;
				}

                // Enqueue the Tweets received
                public void WriteTweet(ITweet tweet)
                {
                    lock (queue)
                    {
                        queue.Enqueue(tweet);
                    }
                }

                // Load sentiment dictionary from a file
                private void LoadDictionary()
                {
                    List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
                    var items = lines.Select(line =>
                    {
                        var fields = line.Split('\t');
                        var pos = 0;
                        return new DictionaryItem
                        {
                            Type = fields[pos++],
                            Length = Convert.ToInt32(fields[pos++]),
                            Word = fields[pos++],
                            Pos = fields[pos++],
                            Stemmed = fields[pos++],
                            Polarity = fields[pos++]
                        };
                    });

                    dictionary = new Dictionary<string, DictionaryItem>();
                    foreach (var item in items)
                    {
                        if (!dictionary.Keys.Contains(item.Word))
                        {
                            dictionary.Add(item.Word, item);
                        }
                    }
                }

                // Calculate sentiment score
                private int CalcSentimentScore(string[] words)
                {
                    Int32 total = 0;
                    foreach (string word in words)
                    {
                        if (dictionary.Keys.Contains(word))
                        {
                            switch (dictionary[word].Polarity)
                            {
                                case "negative": total -= 1; break;
                                case "positive": total += 1; break;
                            }
                        }
                    }
                    if (total > 0)
                    {
                        return 1;
                    }
                    else if (total < 0)
                    {
                        return -1;
                    }
                    else
                    {
                        return 0;
                    }
                }

                // Popular a CellSet object to be written into HBase
                private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
                {
                    // Split the Tweet into words
                    string[] words = tweet.Text.ToLower().Split(_punctuationChars);

                    // Calculate sentiment score base on the words
                    int sentimentScore = CalcSentimentScore(words);
                    var word_pairs = words.Take(words.Length - 1)
                                        .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
                    var all_words = words.Concat(word_pairs).ToList();

                    // For each word in the Tweet add a row to the HBase table
                    foreach (string word in all_words)
                    {
                        string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                        string key = word + "_" + time_index;

                        // Create a row
                        var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                        // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                        var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                        row.values.Add(value);

                        value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                        row.values.Add(value);

                        if (tweet.Coordinates != null)
                        {
                            var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                            value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                            row.values.Add(value);
                        }

                        value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                        row.values.Add(value);

                        set.rows.Add(row);
                    }
                }

                // Write a Tweet (CellSet) to HBase
                public void WriterThreadFunction()
                {
                    try
                    {
                        while (threadRunning)
                        {
                            if (queue.Count > 0)
                            {
                                CellSet set = new CellSet();
                                lock (queue)
                                {
                                    do
                                    {
                                        ITweet tweet = queue.Dequeue();
                                        CreateTweetByWordsCells(set, tweet);
                                    } while (queue.Count > 0);
                                }

                                // Write the Tweet by words cell set to the HBase table
								client.StoreCellsAsync(HBASETABLENAME, set).Wait();
                                Console.WriteLine("\tRows written: {0}", set.rows.Count);
                            }
                            Thread.Sleep(100);
                        }
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Exception: " + ex.Message);
                    }
                }
            }
            public class DictionaryItem
            {
                public string Type { get; set; }
                public int Length { get; set; }
                public string Word { get; set; }
                public string Pos { get; set; }
                public string Stemmed { get; set; }
                public string Polarity { get; set; }
            }
        }

6. 이전 코드에서 **CLUSTERNAME**, **HADOOPUSERNAME**, **HADOOPUSERPASSWORD** 및 DICTIONARYFILENAME 등의 상수를 설정합니다. DICTIONARYFILENAME은 파일 이름 및 direction.tsv의 위치입니다. 파일은 **https://hditutorialdata.blob.core.windows.net/twittersentiment/dictionary.tsv**에서 다운로드할 수 있습니다. HBase 테이블 이름을 변경하려면 웹 응용 프로그램에서 테이블 이름을 적절하게 변경해야 합니다.

7. **Program.cs**를 열고 다음과 같은 코드로 바꿉니다.

        using System;
        using System.Diagnostics;
        using Tweetinvi;
        using Tweetinvi.Models;

        namespace TweetSentimentStreaming
        {
            class Program
            {
                const string TWITTERAPPACCESSTOKEN = "<Enter Twitter App Access Token>";
                const string TWITTERAPPACCESSTOKENSECRET = "<Enter Twitter Access Token Secret>";
                const string TWITTERAPPAPIKEY = "<Enter Twitter App API Key>";
                const string TWITTERAPPAPISECRET = "<Enter Twitter App API Secret>";

                static void Main(string[] args)
                {
                    Auth.SetUserCredentials(TWITTERAPPAPIKEY, TWITTERAPPAPISECRET, TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET);

                    Stream_FilteredStreamExample();
                }

                private static void Stream_FilteredStreamExample()
                {
                    for (;;)
                    {
                        try
                        {
                            HBaseWriter hbase = new HBaseWriter();
                            var stream = Stream.CreateFilteredStream();
                            stream.AddLocation(new Coordinates(-180, -90), new Coordinates(180, 90)); 

                            var tweetCount = 0;
                            var timer = Stopwatch.StartNew();

                            stream.MatchingTweetReceived += (sender, args) =>
                            {
                                tweetCount++;
                                var tweet = args.Tweet;

                                // Write Tweets to HBase
                                hbase.WriteTweet(tweet);

                                if (timer.ElapsedMilliseconds > 1000)
                                {
                                    if (tweet.Coordinates != null)
                                    {
                                        Console.ForegroundColor = ConsoleColor.Green;
                                        Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                        Console.ForegroundColor = ConsoleColor.White;
                                        Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                                    }

                                    timer.Restart();
                                    Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                                    tweetCount = 0;
                                }
                            };

                            stream.StartStreamMatchingAllConditions();
                        }
                        catch (Exception ex)
                        {
                            Console.WriteLine("Exception: {0}", ex.Message);
                        }
                    }
                }

            }
        }

8. **TWITTERAPPACCESSTOKEN**, **TWITTERAPPACCESSTOKENSECRET**, **TWITTERAPPAPIKEY** 및 **TWITTERAPPAPISECRET** 등의 상수를 설정합니다.

스트리밍 서비스를 실행하려면 **F5** 키를 누릅니다. 다음은 콘솔 응용 프로그램의 스크린샷입니다.

![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
    
웹 응용 프로그램을 개발하는 동안 더 많은 데이터를 사용할 수 있도록 스트리밍 콘솔 응용 프로그램을 실행 상태로 유지합니다. HBase 셸을 사용하여 테이블에 삽입된 데이터를 검사할 수 있습니다. [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-started.md#create-tables-and-insert-data)을 참조하세요.


## 실시간 데이터 시각화

이 섹션에서는 HBase에서 실시간 데이터를 읽은 다음 Bing 지도에 플로팅하는 ASP.NET MVC 웹 응용 프로그램을 만듭니다.

**ASP.NET MVC 웹 응용 프로그램을 만들려면**

1. Visual Studio를 엽니다.
2. **파일**, **새로 만들기**를 클릭한 후 **프로젝트**를 클릭합니다.
3. 다음 정보를 입력합니다.

	- 템플릿 범주: **Visual C#/웹**
	- 템플릿: **ASP.NET 웹 응용 프로그램**
	- 이름: **TweetSentimentWeb**
	- 위치: **C:\\Tutorials**
4. **확인**을 클릭합니다.
5. **템플릿 선택**에서 **MVC**를 클릭합니다.
6. **Microsoft Azure**에서 **구독 관리**를 클릭합니다.
7. **Microsoft Azure 구독 관리**에서 **로그인**을 클릭합니다.
8. Azure 자격 증명을 입력합니다. 그러면 **계정** 탭에서 Azure 구독 정보가 표시됩니다.
9. **닫기**를 클릭하여 **Microsoft Azure 구독 관리** 창을 닫습니다.
10. **새 ASP.NET 프로젝트 - TweetSentimentWeb**에서 **확인**을 클릭합니다.
11. **Microsoft Azure 사이트 설정 구성**에서 현재 위치와 가장 인접한 **지역**을 선택합니다. 데이터베이스 서버는 지정하지 않아도 됩니다.
12. **확인**을 클릭합니다.

**Nuget 패키지를 설치하려면**

1. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 페이지 아래쪽에 콘솔 패널이 열립니다.
2. 다음 명령을 사용하여 HBase 클러스터에 액세스하기 위한 클라이언트 라이브러리인 [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) 패키지를 설치합니다.

		Install-Package Microsoft.HBase.Client 

**HBaseReader 클래스를 추가하려면**

1. **솔루션 탐색기**에서 **TweetSentiment**를 확장합니다.
2. **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다.
3. **이름** 필드에 **HBaseReader.cs**를 입력하고 **추가**를 클릭합니다.
4. 코드를 다음으로 바꿉니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		
		using System.Configuration;
		using System.Threading.Tasks;
		using System.Text;
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		
		namespace TweetSentimentWeb.Models
		{
		    public class HBaseReader
		    {
		        // For reading Tweet sentiment data from HDInsight HBase
		        HBaseClient client;
		
		        // HDinsight HBase cluster and HBase table information
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // The constructor
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Query Tweets sentiment data from the HBase table asynchronously 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Demonstrate Filtering the data from the past 6 hours the row key
		            string timeIndex = (ulong.MaxValue -
		                (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
		            string startRow = keyword + "_" + timeIndex;
		            string endRow = keyword + "|";
		            Scanner scanSettings = new Scanner
		            {
		                batch = 100000,
		                startRow = Encoding.UTF8.GetBytes(startRow),
		                endRow = Encoding.UTF8.GetBytes(endRow)
		            };
		
		            // Make async scan call
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // find the cell with string pattern "d:coor" 
		                    var coordinates =
		                        row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		
		                        var sentimentField =
		                            row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
		                        Int32 sentiment = 0;
		                        if (sentimentField != null)
		                        {
		                            sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
		                        }
		
		                        list.Add(new Tweet
		                        {
		                            Longtitude = Convert.ToDouble(lonlat[0]),
		                            Latitude = Convert.ToDouble(lonlat[1]),
		                            Sentiment = sentiment
		                        });
		                    }
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		                    }
		                }
		            }
		
		            return list;
		        }
		    }
		
		    public class Tweet
		    {
		        public string IdStr { get; set; }
		        public string Text { get; set; }
		        public string Lang { get; set; }
		        public double Longtitude { get; set; }
		        public double Latitude { get; set; }
		        public int Sentiment { get; set; }
		    }
		}

4. **HBaseReader** 클래스 내에서 상수 값을 다음과 같이 변경합니다.

	- **CLUSTERNAME**: HBase 클러스터 이름입니다(예: *https://<HBaseClusterName>.azurehdinsight.net/*).
    - **HADOOPUSERNAME**: HBase 클러스터 Hadoop 사용자의 사용자 이름입니다. 기본 이름은 *admin*입니다.
    - **HADOOPUSERPASSWORD**: HBase 클러스터 Hadoop 사용자의 암호입니다.
    - **HBASETABLENAME** = "tweets\_by\_words";

	HBase 테이블 이름은 **"tweets\_by\_words";**입니다. 값은 스트리밍 서비스에서 전송한 값과 일치해야 합니다. 그래야 웹 응용 프로그램이 같은 HBase 테이블에서 데이터를 읽을 수 있습니다.




**TweetsController 컨트롤러를 추가하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**을 확장합니다.
2. **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러**를 클릭합니다.
3. **웹 API 2 컨트롤러 - 비어 있음**을 클릭한 다음 **추가**를 클릭합니다.
4. **컨트롤러 이름** 필드에 **TweetsController**를 입력하고 **추가**를 클릭합니다.
5. **솔루션 탐색기**에서 TweetsController.cs를 두 번 클릭하여 해당 파일을 엽니다.
5. 파일을 다음과 같이 수정합니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		
		using System.Threading.Tasks;
		using TweetSentimentWeb.Models;
		
		namespace TweetSentimentWeb.Controllers
		{
		    public class TweetsController : ApiController
		    {
		        HBaseReader hbase = new HBaseReader();
		
		        public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
		        {
		            return await hbase.QueryTweetsByKeywordAsync(query);
		        }
		    }
		}

**heatmap.js를 추가하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**을 확장합니다.
2. **스크립트**를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **JavaScript 파일**을 클릭합니다.
3. **항목 이름** 필드에 **heatmap.js**를 입력합니다.
4. 다음 코드를 파일에 붙여 넣습니다. 이 코드는 Alastair Aitchison이 작성한 것입니다. 자세한 내용은 [Bing Maps AJAX v7 HeatMap 라이브러리](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/)를 참조하세요.

		/*******************************************************************************
		* Author: Alastair Aitchison
		* Website: http://alastaira.wordpress.com
		* Date: 15th April 2011
		* 
		* Description: 
		* This JavaScript file provides an algorithm that can be used to add a heatmap
		* overlay on a Bing Maps v7 control. The intensity and temperature palette
		* of the heatmap are designed to be easily customisable.
		*
		* Requirements:
		* The heatmap layer itself is created dynamically on the client-side using
		* the HTML5 <canvas> element, and therefore requires a browser that supports
		* this element. It has been tested on IE9, Firefox 3.6/4 and 
		* Chrome 10 browsers. If you can confirm whether it works on other browsers or
		* not, I'd love to hear from you!
		
		* Usage:
		* The HeatMapLayer constructor requires:
		* - A reference to a map object
		* - An array or Microsoft.Maps.Location items
		* - Optional parameters to customise the appearance of the layer
		*  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Private Properties */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Set default options
		    var _options = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 1000,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'meters',
		
		        // Colour temperature gradient of the map
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // Magenta
		            "0.25": 'rgba(0,0,255,40)',    // Blue
		            "0.50": 'rgba(0,255,0,80)',    // Green
		            "0.75": 'rgba(255,255,0,120)', // Yellow
		            "1.00": 'rgba(255,0,0,150)'    // Red
		        },
		
		        // Callback function to be fired after heatmap layer has been redrawn 
		        callback: null
		    };
		
		    /* Private Methods */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Create the canvas element
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Override defaults with any options passed in the constructor
		            _setOptions(options);
		
		            // Load array of location data
		            _setPoints(locations);
		
		            // Create a colour gradient from the suppied colourstops
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Wire up the event handler to redraw heatmap canvas
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Resets the heat map
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Creates a colour gradient from supplied colour stops on initialisation
		    function _createColourGradient(colourstops) {
		        var ctx = document.createElement('canvas').getContext('2d');
		        var grd = ctx.createLinearGradient(0, 0, 256, 0);
		        for (var c in colourstops) {
		            grd.addColorStop(c, colourstops[c]);
		        }
		        ctx.fillStyle = grd;
		        ctx.fillRect(0, 0, 256, 1);
		        return ctx.getImageData(0, 0, 256, 1).data;
		    }
		
		    // Applies a colour gradient to the intensity map
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // get the alpha of this pixel
		            if (a != 0) { // If there is any data to plot
		                pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
		                pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
		                pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
		            }
		            p += 4; // Move on to the next pixel
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Sets any options passed in
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Sets the heatmap points from an array of Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Main method to draw the heatmap
		    function _createHeatMap() {
		        // Ensure the canvas matches the current dimensions of the map
		        // This also has the effect of resetting the canvas
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Calculate the pixel radius of each heatpoint at the current map zoom
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Convert lat/long to pixel location
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Create the Intensity Map by looping through each location
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Create radial gradient centred on this point
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Draw the heatpoint onto the canvas
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Apply the specified colour gradient to the intensity map
		        _colouriseHeatMap();
		
		        // Call the callback function, if specified
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Public Methods */
		
		    this.Show = function () {
		        if (_canvas) {
		            _canvas.style.display = '';
		        }
		    };
		
		    this.Hide = function () {
		        if (_canvas) {
		            _canvas.style.display = 'none';
		        }
		    };
		
		    // Sets options for intensity, radius, colourgradient etc.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
		    this.SetPoints = function (locations) {
		        // Reset the existing heatmap layer
		        _clearHeatMap();
		        // Pass in the new set of locations
		        _setPoints(locations);
		        // Recreate the layer
		        _createHeatMap();
		    }
		
		    // Removes the heatmap layer from the DOM
		    this.Remove = function () {
		        _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
		
		        if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
		        if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
		
		        _locations = null;
		        _temperaturemap = null;
		        _canvas = null;
		        _options = null;
		        _viewchangestarthandler = null;
		        _viewchangeendhandler = null;
		    }
		
		    // Call the initialisation routine
		    _init();
		};
		
		// Call the Module Loaded method
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**twitterStream.js를 추가하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**을 확장합니다.
2. **스크립트**를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **JavaScript 파일**을 클릭합니다.
3. **항목 이름** 필드에 **twitterStream.js**를 입력합니다.
4. 다음 코드를 복사하여 파일에 붙여 넣습니다.

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // Initialize the map
		    var options = {
		        credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Heatmap options for positive, neutral and negative layers
		
		    var heatmapOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 255, 191, 1)',
		            0.3: 'rgba(0, 255, 127, 1)',
		            0.4: 'rgba(0, 255, 63, 1)',
		            0.5: 'rgba(0, 127, 0, 1)',
		            0.7: 'rgba(0, 159, 0, 1)',
		            0.8: 'rgba(0, 191, 0, 1)',
		            0.9: 'rgba(0, 223, 0, 1)',
		            1.0: 'rgba(0, 255, 0, 1)'
		        }
		    };
		
		    var heatmapNegOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 191, 255, 1)',
		            0.3: 'rgba(0, 127, 255, 1)',
		            0.4: 'rgba(0, 63, 255, 1)',
		            0.5: 'rgba(0, 0, 127, 1)',
		            0.7: 'rgba(0, 0, 159, 1)',
		            0.8: 'rgba(0, 0, 191, 1)',
		            0.9: 'rgba(0, 0, 223, 1)',
		            1.0: 'rgba(0, 0, 255, 1)'
		        }
		    };
		
		    // Register and load the Client Side HeatMap Module
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback: function () {
		            // Create heatmap layers for positive, neutral and negative tweets
		            heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
		            heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
		            heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
		        }
		    });
		
		    $("#searchbox").val("xbox");
		    $("#searchBtn").click(onsearch);
		    $("#positiveBtn").click(onPositiveBtn);
		    $("#negativeBtn").click(onNegativeBtn);
		    $("#neutralBtn").click(onNeutralBtn);
		    $("#neutralBtn").button("toggle");
		}
		
		function onsearch() {
		    var uri = 'api/tweets?query=';
		    var query = $('#searchbox').val();
		    $.getJSON(uri + query)
		        .done(function (data) {
		            liveTweetsPos = [];
		            liveTweets = [];
		            liveTweetsNeg = [];
		
		            // On success, 'data' contains a list of tweets.
		            $.each(data, function (key, item) {
		                addTweet(item);
		            });
		
		            if (!$("#neutralBtn").hasClass('active')) {
		                $("#neutralBtn").button("toggle");
		            }
		            onNeutralBtn();
		        })
		        .fail(function (jqXHR, textStatus, err) {
		            $('#statustext').text('Error: ' + err);
		        });
		}
		
		function addTweet(item) {
		    //Add tweet to the heat map arrays.
		    var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
		    if (item.Sentiment > 0) {
		        liveTweetsPos.push(tweetLocation);
		    } else if (item.Sentiment < 0) {
		        liveTweetsNeg.push(tweetLocation);
		    } else {
		        liveTweets.push(tweetLocation);
		    }
		}
		
		function onPositiveBtn() {
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmapPos.SetPoints(liveTweetsPos);
		    heatmapPos.Show();
		    heatmapNeg.Hide();
		    heatmap.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
		}
		
		function onNeutralBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmap.SetPoints(liveTweets);
		    heatmap.Show();
		    heatmapNeg.Hide();
		    heatmapPos.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
		}
		
		function onNegativeBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		
		    heatmapNeg.SetPoints(liveTweetsNeg);
		    heatmapNeg.Show();
		    heatmap.Hide();;
		    heatmapPos.Hide();;
		
		    $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
		}
		
		function getPosNegRatio() {
		    if (liveTweetsNeg.length == 0) {
		        return "";
		    }
		    else {
		        var ratio = liveTweetsPos.length / liveTweetsNeg.length;
		        var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
		        return "Positive/Negative Ratio: " + str;
		    }
		}


**layout.cshtml을 수정하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**, **뷰**, **공유**를 차례로 확장하고 \_**Layout.cshtml**을 두 번 클릭합니다.
2. 파일의 내용을 다음으로 바꿉니다.

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		    <!-- Bing Maps -->
		    <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
		    <!-- Spatial Dashboard JavaScript -->
		    <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
		</head>
		<body onload="initialize()">
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		            </div>
		            <div class="navbar-collapse collapse">
		                <div class="row">
		                    <ul class="nav navbar-nav col-lg-5">
		                        <li class="col-lg-12">
		                            <div class="navbar-form">
		                                <input id="searchbox" type="search" class="form-control">
		                                <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
		                            </div>
		                        </li>
		                    </ul>
		                    <ul class="nav navbar-nav col-lg-7">
		                        <li>
		                            <div class="navbar-form">
		                                <div class="btn-group" data-toggle="buttons-radio">
		                                    <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
		                                    <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
		                                    <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
		                                </div>
		                            </div>
		                        </li>
		                        <li><span id="statustext" class="navbar-text"></span></li>
		                    </ul>
		                </div>
		            </div>
		        </div>
		    </div>
		    <div class="map_container">
		        @RenderBody()
		    </div>
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>



**Index.cshtml을 수정하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**, **뷰**, **홈**을 차례로 확장하고 \_**Index.cshtml**을 두 번 클릭합니다.
2. 파일의 내용을 다음으로 바꿉니다.

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**site.css 파일을 수정하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**, **콘텐츠**를 차례로 확장하고 \_**Site.css**를 두 번 클릭합니다.
2. 파일에 다음 코드를 추가합니다.
		
		/* make container, and thus map, 100% width */
		.map_container {
			width: 100%;
			height: 100%;
		}
		
		#map_canvas{
		  height:100%;
		}
		
		#tweets{
		  position: absolute;
		  top: 60px;
		  left: 75px;
		  z-index:1000;
		  font-size: 30px;
		}

**global.asax 파일을 수정하려면**

1. **솔루션 탐색기**에서 **TweetSentimentWeb**을 확장하고 **Global.asax**를 두 번 클릭합니다.
2. 다음 **using** 문을 추가합니다.

		using System.Web.Http;

2. **Application\_Start()** 함수 내에 다음 줄을 추가합니다.

		// Register API routes
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	웹 API 컨트롤러가 MVC 응용 프로그램 내부에서 작동하도록 API 경로의 등록을 수정합니다.

**웹 응용 프로그램을 실행하려면**

1. 스트리밍 서비스 콘솔 응용 프로그램이 계속 실행 중인지 확인합니다. 그래야 실시간 변경 내용을 확인할 수 있습니다.
2. **F5** 키를 눌러 웹 응용 프로그램을 실행합니다.

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. 텍스트 상자에 키워드를 입력하고 **검색**을 클릭합니다. HBase 테이블에 수집된 데이터에 따라 일부 키워드는 검색되지 않을 수도 있습니다. "love," "xbox," "playstation" 등의 일반적인 키워드를 사용해 보세요.
3. **긍정적**, **중립**, **부정적** 간을 전환하여 주제에 대한 데이터를 비교합니다.
4. 다른 시간에 스트리밍 서비스를 실행한 다음 같은 키워드를 검색하여 결과를 비교해 봅니다.

 
필요한 경우 Azure 웹 사이트에 응용 프로그램을 배포할 수 있습니다. 관련 지침은 [Azure 웹 사이트 및 ASP.NET 시작][website-get-started]을 참조하세요.
 
## 다음 단계

이 자습서에서는 트윗을 가져와서 트윗 데이터를 분석하고 데이터를 HBase에 저장한 다음 실시간 Twitter 데이터를 Bing 지도에 표시하는 방법을 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight 시작][hdinsight-get-started]
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md)
- [HDInsight에서 Hadoop으로 Twitter 데이터 분석][hdinsight-analyze-twitter-data]
- [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-delay-data]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]


[hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[website-get-started]: ../app-service-web/web-sites-dotnet-get-started.md



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-analyze-twitter-data]: hdinsight-analyze-twitter-data.md




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
 

<!---HONumber=AcomDC_0914_2016-->