---
title: '자습서: Azure HDInsight의 Apache Spark로 Azure Event Hubs에서 데이터 처리 | Microsoft Docs'
description: Azure HDInsight의 Apache Spark를 Azure Event Hubs에 연결하고 스트리밍 데이터를 처리합니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>자습서: HDInsight에서 Azure Event Hubs 및 Spark를 사용하여 트윗 처리

이 자습서에서는 Apache Spark 스트리밍 응용 프로그램을 만들어 Azure 이벤트 허브로 트윗을 보내고, 다른 응용 프로그램을 만들어 이벤트 허브에서 트윗을 읽는 방법을 알아봅니다. Spark 스트리밍에 대한 자세한 내용은 [Apache Spark 스트리밍 개요](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)를 참조하세요. HDInsight는 Azure에서 Spark 클러스터에 동일한 스트리밍 기능을 제공합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure 이벤트 허브에 메시지 전송
> * Azure 이벤트 허브에서 메시지 읽기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **문서 [자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md)을 완료합니다**.

## <a name="create-a-twitter-application"></a>Twitter 응용 프로그램 만들기

트윗 스트림을 받으려면 Twitter에 응용 프로그램을 만듭니다. 다음 지침에 따라 Twitter 응용 프로그램을 만들고 이 자습서를 완료하는 데 필요한 값을 기록합니다.

1. [Twitter 응용 프로그램 관리](https://apps.twitter.com/)로 이동합니다.
2. **새 앱 만들기**를 선택합니다.
3. 다음 값을 제공합니다.

    - 이름: 응용 프로그램 이름을 제공합니다. 이 자습서에 사용되는 값은 **HDISparkStreamApp0423**입니다. 이 이름은 고유한 이름이어야 합니다.
    - 설명: 응용 프로그램에 대한 간단한 설명을 제공합니다. 이 자습서에 사용되는 값은 **간단한 HDInsight Spark 스트리밍 응용 프로그램**입니다.
    - 웹 사이트: 응용 프로그램의 웹 사이트를 제공합니다. 유효한 웹 사이트를 입력하지 않아도 됩니다.  이 자습서에 사용되는 값은 **http://www.contoso.com**입니다.
    - 콜백 URL: 비워 둘 수 있습니다.

4. **예, Twitter 개발자 계약을 읽고 동의합니다**를 선택한 다음, **Twitter 응용 프로그램 만들기**를 선택합니다.
5. **Keys and Access Tokens** 탭을 선택합니다.
6. 페이지의 끝에서 **내 액세스 토큰 만들기**를 선택합니다.
7. 페이지에서 다음 값을 기록합니다.  자습서의 뒷부분에서 이러한 값이 필요합니다.

    - **소비자 키(API 키)**    
    - **소비자 비밀(API 비밀)**  
    - **액세스 토큰**
    - **액세스 토큰 비밀**   

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기

이 이벤트 허브를 사용하여 트윗을 저장합니다.

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.
2. 화면 왼쪽 뒤에서 **리소스 만들기**를 선택합니다.
3. **사물 인터넷**을 선택한 다음, **Event Hubs**를 선택합니다.

    ![Spark 스트리밍 예제에 대한 이벤트 허브 만들기](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark 스트리밍 예제에 대한 이벤트 허브 만들기")
4. 새 이벤트 허브 네임스페이스에 다음 값을 입력합니다.

    - **이름**: 이벤트 허브의 이름을 입력합니다.  이 자습서에 사용되는 값은 **myeventhubns20180403**입니다.
    - **가격 계층**: **표준**을 선택합니다.
    - **리소스 그룹**: 새로 만들거나 Spark 클러스터에 대한 리소스 그룹을 선택하는 옵션이 있습니다. 
    - **위치**: 대기 시간 및 비용을 줄이려면 HDInsight의 Apache Spark 클러스터와 동일한 **위치**를 선택합니다.

    ![Spark 스트리밍 예제에 대한 이벤트 허브 이름 제공](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Spark 스트리밍 예제에 대한 이벤트 허브 이름 제공")
5. **만들기**를 선택하여 네임스페이스를 만듭니다.

6. 다음 지침을 사용하여 이벤트 허브 네임스페이스를 엽니다.

    1. 포털에서 **모든 서비스**를 선택합니다.
    2. 필터 상자에 **이벤트 허브**를 입력합니다.
    3. 만든 네임스페이스를 두 번 클릭합니다.
    4. **+ 이벤트 허브**를 선택합니다.

6. Event Hubs 네임스페이스 목록에서 새로 만든 네임스페이스를 선택합니다.      
5. **Event Hubs**를 선택한 다음, **+ 이벤트 허브**를 선택하여 새 이벤트 허브를 만듭니다.
  

6. 다음 값을 입력합니다.

    - 이름: 이벤트 허브의 이름을 지정합니다.
    - 파티션 수: 10
    - 메시지 보존기간: 1 
   
    ![Spark 스트리밍 예제에 대한 이벤트 허브 세부 정보 제공](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark 스트리밍 예제에 대한 이벤트 허브 세부 정보 제공")

7. **만들기**를 선택합니다.
8. 네임스페이스에 대해 **공유 액세스 정책**을 선택한 다음(이벤트 허브 공유 액세스 정책이 아님), **RootManageSharedAccessKey**를 선택합니다.
    
     ![Spark 스트리밍 예제에 대한 이벤트 허브 정책 설정](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Spark 스트리밍 예제에 대한 이벤트 허브 정책 설정")

9. **기본 키** 및 **연결 문자열-기본 키**의 값을 저장하여 자습서의 뒷부분에서 사용합니다.

     ![Spark 스트리밍 예제에 대한 이벤트 허브 정책 키 보기](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Spark 스트리밍 예제에 대한 이벤트 허브 정책 키 보기")


## <a name="send-tweets-to-the-event-hub"></a>이벤트 허브에 트윗 보내기

Jupyter 노트북을 만들고, **SendTweetsToEventHub**라는 이름을 지정해야 합니다. 

1. 다음 코드를 실행하여 외부 Maven 라이브러리를 추가합니다.

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. 다음 코드를 실행하여 이벤트 허브에 트윗을 보냅니다.

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Azure Portal에서 이벤트 허브를 엽니다.  **개요**에 이벤트 허브로 전송된 메시지를 보여주는 일부 차트가 표시됩니다.

## <a name="read-tweets-from-the-event-hub"></a>이벤트 허브에서 트윗 읽기

다른 Jupyter 노트북을 만들고, **ReadTweetsFromEventHub**라는 이름을 지정해야 합니다. 

1. 다음 코드를 실행하여 외부 Maven 라이브러리를 추가합니다.

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. 다음 코드를 실행하여 이벤트 허브에서 트윗을 읽습니다.

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>리소스 정리

HDInsight를 사용하면 데이터가 Azure Storage 또는 Azure Data Lake Store에 저장되므로 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 다음 자습서의 작업을 바로 수행하려는 경우 클러스터를 유지할 수 있습니다.

Azure Portal에서 클러스터를 열고, **삭제**를 선택합니다.

![HDInsight 클러스터 삭제](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

또한 리소스 그룹 이름을 선택하여 리소스 그룹 페이지를 연 다음, **리소스 그룹 삭제**를 선택할 수도 있습니다. 리소스 그룹을 삭제하여 HDInsight Spark 클러스터와 기본 저장소 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* 이벤트 허브에서 메시지를 읽습니다.
기계 학습 응용 프로그램을 만들 수 있는지 보려면 다음 문서를 진행합니다. 

> [!div class="nextstepaction"]
> [기계 학습 응용 프로그램 만들기](./apache-spark-ipython-notebook-machine-learning.md)


