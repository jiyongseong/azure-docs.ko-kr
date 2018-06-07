---
title: "Azure Application Insights의 Analytics로 데이터 가져오기 | Microsoft Docs"
description: "앱 원격 분석에 연결할 정적 데이터를 가져오거나 Analytics로 쿼리할 별도 데이터 스트림을 가져옵니다."
services: application-insights
keywords: "개방형 스키마, 데이터 가져오기"
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 963e5cfd929f57b34dcb045df82b64f870e897e2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="import-data-into-analytics"></a>Analytics로 데이터 가져오기

테이블 형식 데이터를 [Analytics](app-insights-analytics.md)로 가져와 앱의 [Application Insights](app-insights-overview.md) 원격 분석과 연결하거나 별도 스트림으로 분석할 수 있습니다. Analytics는 타임스탬프가 지정된 고용량 원격 분석 스트림을 분석하는 데 적합한 강력한 쿼리 언어입니다.

사용자 고유의 스키마를 사용하여 Analytics로 데이터를 가져올 수 있습니다. 요청 또는 추적과 같은 표준 Application Insights 스키마를 사용할 필요는 없습니다.

JSON 또는 DSV(구분 기호 쉼표, 세미콜론 또는 탭으로 구분된 값) 파일을 가져올 수 있습니다.

다음과 같은 세 가지 상황에서는 Analytics로 가져오는 것이 유용합니다.

* **앱 원격 분석에 연결** 예를 들어 웹 사이트의 URL을 좀 더 읽기 쉬운 페이지 제목에 매핑하는 테이블을 가져올 수 있습니다. Analytics에서 웹 사이트의 가장 인기 있는 10개 페이지를 표시하는 대시보드 차트 보고서를 만들 수 있습니다. 이제 URL 대신 페이지 제목을 표시할 수 있습니다.
* **응용 프로그램 원격 분석**과 네트워크 트래픽, 서버 데이터 또는 CDN 로그 파일 등의 다른 소스 간에 상관 관계를 형성합니다.
* **별도 데이터 스트림에 Analytics를 적용합니다.** Application Insights Analytics는 타임스탬프가 스파스 스트림에 잘 작동하는 강력한 도구로, 많은 경우에 SQL보다 훨씬 유용합니다. 다른 소스에서 이러한 스트림이 제공되면 Analytics에서 분석할 수 있습니다.

데이터 원본에 데이터를 보내는 것은 쉽습니다. 

1. (한 번) '데이터 원본'에 데이터의 스키마를 정의합니다.
2. (주기적으로) Azure Storage로 데이터를 업로드하고, REST API를 호출하여 새 데이터가 수집을 위해 대기 중임을 알립니다. 몇 분 내에 Analytics에서 해당 데이터를 쿼리할 수 있습니다.

업로드 빈도는 쿼리에 데이터를 얼마나 빠르게 사용하고 싶은지에 따라 사용자가 정의합니다. 더 큰 청크로 데이터를 업로드하는 것이 좀 더 효율적이지만 1GB보다 크지 않아야 합니다.

> [!NOTE]
> *분석할 많은 데이터 원본이 있나요?* [logstash*를 사용하여 Application Insights로 데이터를 전달하는 것을 고려하세요.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>시작하기 전에

다음 작업을 수행해야 합니다.

1. Microsoft Azure의 Application Insights 리소스.

 * 다른 원격 분석과 별도로 데이터를 분석하려는 경우 [새 Application Insights 리소스를 만듭니다](app-insights-create-new-resource.md).
 * 이미 Application Insights로 설정된 앱에서 원격 분석에 데이터를 연결하거나 데이터를 비교하는 경우 해당 앱의 리소스를 사용할 수 있습니다.
 * 해당 리소스에 대한 참가자 또는 소유자 액세스 권한
 
2. Azure 저장소의 Blob을 나타냅니다. Azure Storage에 업로드하면 Analytics가 데이터를 가져옵니다. 

 * Blob에 대한 전용 저장소 계정을 만드는 것이 좋습니다. Blob이 다른 프로세스와 공유되면 프로세스에서 blob을 읽는 데 더 오래 걸립니다.


## <a name="define-your-schema"></a>스키마 정의

데이터를 가져오려면 데이터 스키마를 지정하는 *데이터 원본*를 정의해야 합니다.
단일 Application Insights 리소스에 최대 50개의 데이터 원본을 유지할 수 있습니다.

1. 데이터 원본 마법사를 시작합니다. "새 데이터 원본 추가" 버튼을 사용합니다. 또는 오른쪽 위 모서리에서 설정 단추를 클릭하고 드롭다운 메뉴에서 "데이터 원본"을 선택합니다.

    ![새 데이터 원본 추가](./media/app-insights-analytics-import/add-new-data-source.png)

    새 데이터 원본의 이름을 제공합니다.

2. 업로드할 파일의 형식을 정의합니다.

    형식을 수동으로 정의하거나 샘플 파일을 업로드할 수 있습니다.

    데이터가 CSV 형식인 경우 이 샘플의 첫 번째 행은 열 머리글일 수 있습니다. 다음 단계에서 필드 이름을 변경할 수 있습니다.

    샘플은 10개 이상의 데이터 행 또는 레코드를 포함해야 합니다.

    열 또는 필드 이름은 공백이나 문장 부호 없는 영숫자 이름이어야 합니다.

    ![샘플 파일 업로드](./media/app-insights-analytics-import/sample-data-file.png)


3. 마법사가 제공하는 스키마를 검토합니다. 샘플에서 형식을 유추한 경우 유추된 열 형식을 조정해야 할 수 있습니다.

    ![유추된 스키마 검토](./media/app-insights-analytics-import/data-source-review-schema.png)

 * 선택 사항입니다. 스키마 정의를 업로드합니다. 아래 형식을 참조하세요.

 * 타임스탬프를 선택합니다. Analytics의 모든 데이터에는 타임스탬프 필드가 있어야 합니다. `datetime` 형식이어야 하지만 이름이 'timestamp'일 필요는 없습니다. 데이터에 ISO 형식의 날짜 및 시간을 포함하는 열이 있는 경우 이 열을 타임스탬프 열로 선택합니다. 그렇지 않으면 "데이터 도착 시"를 선택합니다. 그러면 가져오기 프로세스가 타임스탬프 필드를 추가합니다.

5. 데이터 원본을 만듭니다.

### <a name="schema-definition-file-format"></a>스키마 정의 파일 형식

UI에서 스키마를 편집하는 대신 파일에서 스키마 정의를 로드할 수 있습니다. 스키마 정의 형식은 다음과 같습니다. 

구분된 형식 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

JSON 형식 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
각 열은 위치, 이름 및 형식으로 식별됩니다. 

* 위치 – 구분된 파일 형식인 경우 매핑된 값의 위치입니다. JSON 형식인 경우 매핑된 키의 jpath입니다.
* 이름 - 열의 표시 이름입니다.
* 유형 – 해당 열의 데이터 형식입니다.
 
샘플 데이터가 사용되었고 파일 형식이 구분된 경우 스키마 정의에서 모든 열을 매핑하고 마지막에 새 열을 추가해야 합니다. 

JSON은 데이터의 부분 매핑을 허용합니다. 따라서 JSON 형식의 스키마 정의가 샘플 데이터에서 발견되는 모든 키를 매핑할 필요가 없습니다. 샘플 데이터에 포함되지 않은 열도 매핑할 수 있습니다. 

## <a name="import-data"></a>데이터 가져오기

데이터를 가져오려면 Azure Storage로 업로드하고, 액세스 키를 만든 다음 REST API 호출을 수행합니다.

![새 데이터 원본 추가](./media/app-insights-analytics-import/analytics-upload-process.png)

다음 프로세스를 수동으로 수행하거나 정기적으로 수행하도록 자동화 시스템을 설정할 수 있습니다. 가져올 데이터의 각 블록에 대해 이러한 단계를 수행해야 합니다.

1. [Azure Blob Storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md)에 데이터를 업로드합니다. 

 * Blob은 압축하지 않은 상태로 최대 1GB 크기일 수 있습니다. 성능 측면에서는 수백 MB 단위의 큰 blob이 이상적입니다.
 * 데이터를 쿼리에 사용하기 위해 Gzip으로 압축하여 업로드 시간 및 대기 시간을 향상시킬 수 있습니다. `.gz` 파일 이름 확장명을 사용합니다.
 * 여러 서비스의 호출로 인해 성능이 저하되는 것을 방지하려면 이 용도에 별도의 저장소 계정을 사용하는 것이 가장 좋습니다.
 * 데이터를 몇 초 단위로 매우 자주 전송할 때는 성능상의 이유로 둘 이상의 저장소 계정을 사용하는 것이 좋습니다.

 
2. [Blob에 대한 공유 액세스 서명 키를 생성합니다](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). 이 키는 만료 기간이 1일이고 읽기 액세스 권한을 부여해야 합니다.
3. 데이터가 대기 중임을 Application Insights에 알리기 위해 REST 호출을 수행합니다.

 * 끝점: `https://dc.services.visualstudio.com/v2/track`
 * HTTP 메서드: POST
 * 페이로드:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

자리 표시자:

* `Blob URI with Shared Access Key`: 키를 만들기 위한 절차에서 가져옵니다. Blob에 국한됩니다.
* `Schema ID`: 정의된 스키마에 대해 생성된 스키마 ID입니다. 이 blob의 데이터는 스키마를 준수해야 합니다.
* `DateTime`: 요청이 제출된 시간(UTC). 허용되는 형식: ISO8601(예: "2016-01-01 13:45:01"), RFC822 ("Wed, 14 Dec 16 14:57:01 +0000"), RFC850 ("Wednesday, 14-Dec-16 14:57:00 UTC"), RFC1123 ("Wed, 14 Dec 2016 14:57:00 +0000").
* Application Insights 리소스의 `Instrumentation key`

몇 분 후에 Analytics에서 데이터를 사용할 수 있습니다.

## <a name="error-responses"></a>오류 응답

* **400 잘못된 요청**: 요청 페이로드가 잘못되었음을 나타냅니다. 다음을 확인하세요.
 * 올바른 계측 키
 * 유효한 시간 값. 현재 시간(UTC)이어야 합니다.
 * 이벤트의 JSON이 스키마를 준수합니다.
* **403 사용 권한 없음**: 전송한 Blob에 액세스할 수 없습니다. 공유 액세스 키가 유효한지와 만료되지 않았는지 확인합니다.
* **404 찾을 수 없음**:
 * Blob이 존재하지 않습니다.
 * SourceId가 잘못되었습니다.

응답 오류 메시지에서 좀 더 자세한 정보를 사용할 수 있습니다.


## <a name="sample-code"></a>샘플 코드

이 코드는 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet 패키지를 사용합니다.

### <a name="classes"></a>클래스

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>데이터 수집

각 blob에 대한 이 코드를 사용합니다. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>다음 단계

* [Log Analytics 쿼리 언어 둘러보기](app-insights-analytics-tour.md)
* Logstash를 사용 중인 경우 [Logstash 플러그 인을 사용하여 Application Insights로 데이터를 보냅니다](https://github.com/Microsoft/logstash-output-application-insights).
