---
title: Azure에서 응용 프로그램 데이터의 고가용성 지원 | Microsoft Docs
description: 읽기 액세스 지역 중복 저장소를 사용하여 응용 프로그램 데이터의 고가용성을 지원하세요.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 03/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 8cf96059b1bbfbad24bf28fec9ddb0aa930adbad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Azure Storage를 통해 응용 프로그램 데이터의 고가용성 지원

이 자습서는 Azure에서 응용 프로그램 데이터의 고가용성을 높이는 방법을 보여 주는 시리즈 중 제1부입니다. 작업을 완료하면 [RA-GRS](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)(읽기 액세스 지역 중복 저장소) 계정으로 Blob을 업로드하고 검색하는 콘솔 응용 프로그램을 갖게 됩니다. RA-GRS는 주 지역에서 보조 지역으로 트랜잭션을 복제하는 방식으로 작동합니다. 복제 프로세스는 보조 지역의 데이터가 결과적으로 일치하도록 보장합니다. 이 응용 프로그램은 [회로 차단기](/azure/architecture/patterns/circuit-breaker) 패턴을 사용하여 연결할 끝점을 결정하고 오류가 시뮬레이션되면 보조 끝점으로 전환합니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 저장소 계정 만들기
> * 샘플 다운로드
> * 연결 문자열 설정
> * 콘솔 응용 프로그램 실행

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
  - **Azure 개발**

  ![Azure Development(웹 및 클라우드 아래)](media/storage-create-geo-redundant-storage/workloads.png)

* (선택 사항) [Fiddler](https://www.telerik.com/download/fiddler) 다운로드 및 설치
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* [Python 설치](https://www.python.org/downloads/)
* [Python용 Azure Storage SDK](https://github.com/Azure/azure-storage-python) 다운로드 및 설치
* (선택 사항) [Fiddler](https://www.telerik.com/download/fiddler) 다운로드 및 설치

# <a name="java-tabjava"></a>[Java](#tab/java)

* 명령줄에서 작동할 수 있게 [Maven](http://maven.apache.org/download.cgi) 설치 및 구성
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 설치 및 구성

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

저장소 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위한 고유한 네임스페이스를 제공합니다.

아래 단계에 따라 읽기 액세스 지역 중복 저장소 계정을 만듭니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 단추를 선택합니다.

2. **새로 만들기** 페이지에서 **저장소**를 선택하고 **추천**에서 **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
3. 다음 정보로 저장소 계정 양식을 작성하고(아래 이미지 참조) **만들기**를 선택합니다.

   | 설정       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | 저장소 계정의 고유한 값 |
   | **배포 모델** | 리소스 관리자  | 리소스 관리자에는 최신 기능이 포함되어 있습니다.|
   | **계정 종류** | StorageV2 | 계정 유형에 대한 세부 정보는 [저장소 계정 유형](../common/storage-introduction.md#types-of-storage-accounts)을 참조하세요. |
   | **성능** | Standard | Standard는 샘플 시나리오에 충분합니다. |
   | **복제**| RA-GRS(읽기 액세스 지역 중복 저장소) | 샘플 작동에 필요합니다. |
   |**보안 전송 필요** | 사용 안 함| 보안 전송은 이 시나리오에 필요하지 않습니다. |
   |**구독** | 사용자의 구독 |구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   |**ResourceGroup** | myResourceGroup |유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   |**위치**: | 미국 동부 | 위치를 선택합니다. |

![저장소 계정 만들기](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>샘플 다운로드

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)하고, storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 파일의 압축을 풉니다. 또한 [git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드할 수 있습니다. 샘플 프로젝트는 콘솔 응용 프로그램을 포함합니다.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)하고, storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 파일의 압축을 풉니다. 또한 [git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드할 수 있습니다. 샘플 프로젝트에는 기본 Python 응용 프로그램이 포함되어 있습니다.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-tabjava"></a>[Java](#tab/java)
[샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/storage-java-ha-ra-grs)하고 storage-java-ragrs.zip 파일의 압축을 풉니다. 또한 [git](https://git-scm.com/)을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드할 수 있습니다. 샘플 프로젝트에는 기본 Java 응용 프로그램이 포함되어 있습니다.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>연결 문자열 설정

응용 프로그램에서 저장소 계정에 대한 연결 문자열을 제공해야 합니다. 이 연결 문자열은 응용 프로그램을 실행하는 로컬 컴퓨터의 환경 변수 내에 저장하는 것이 좋습니다. 운영 체제에 따라 아래 예제 중 하나를 따라 환경 변수를 만듭니다.

Azure Portal에서 저장소 계정으로 이동합니다. 저장소 계정의 **설정** 아래에서 **액세스 키**를 선택합니다. 기본 또는 보조 키에서 **연결 문자열**을 복사합니다. 운영 체제에 따라 다음 명령 중 하나를 실행하여 \<yourconnectionstring\>을 실제 연결 문자열로 바꿉니다. 이 명령은 로컬 컴퓨터에 환경 변수를 저장합니다. Windows에서 사용 중인 **명령 프롬프트** 또는 셸을 다시 로드할 때까지 환경 변수를 사용할 수 없습니다. 다음 샘플에서 **\<storageConnectionString\>** 을 바꿉니다.

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>콘솔 응용 프로그램 실행

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
Visual Studio에서 **F5** 키를 누르거나 **시작**을 클릭하여 응용 프로그램 디버깅을 시작합니다. Visual Studio는 구성된 경우 누락된 NuGet 패키지를 자동으로 복원합니다. 자세한 내용은 [패키지 복원으로 패키지 설치 및 다시 설치](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)에서 확인하세요.

콘솔 창에서 시작하고 응용 프로그램이 실행을 시작합니다. 응용 프로그램은 **HelloWorld.png** 이미지를 솔루션에서 저장소 계정으로 업로드합니다. 응용 프로그램은 해당 이미지를 보조 RA-GRS 끝점으로 복제했는지 확인합니다. 그런 다음, 이미지를 최대 999회까지 다운로드를 시작합니다. 읽기는 각각 **P** 또는 **S**로 나타납니다. 여기서 **P**는 기본 끝점을 나타내고 **S**는 보조 끝점을 나타냅니다.

![콘솔 앱 실행](media/storage-create-geo-redundant-storage/figure3.png)

샘플 코드에서 `Program.cs` 파일의 `RunCircuitBreakerAsync` 작업은 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) 메서드를 사용하여 저장소 계정에서 이미지를 다운로드하는 데 사용합니다. 다운로드하기 전에 [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)가 정의됩니다. 작업 컨텍스트는 다운로드가 성공적으로 완료되거나, 다운로드가 실패하고 다시 시도하는 경우 생성되는 이벤트 처리기를 정의합니다.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
터미널 또는 명령 프롬프트에서 응용 프로그램을 실행하려면 **circuitbreaker.py** 디렉터리로 이동한 다음, `python circuitbreaker.py`를 입력합니다. 응용 프로그램은 **HelloWorld.png** 이미지를 솔루션에서 저장소 계정으로 업로드합니다. 응용 프로그램은 해당 이미지를 보조 RA-GRS 끝점으로 복제했는지 확인합니다. 그런 다음, 이미지를 최대 999회까지 다운로드를 시작합니다. 읽기는 각각 **P** 또는 **S**로 나타납니다. 여기서 **P**는 기본 끝점을 나타내고 **S**는 보조 끝점을 나타냅니다.

![콘솔 앱 실행](media/storage-create-geo-redundant-storage/figure3.png)

샘플 코드에서 `circuitbreaker.py` 파일의 `run_circuit_breaker` 메서드는 [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) 메서드를 사용하여 저장소 계정에서 이미지를 다운로드하는 데 사용됩니다. 

Storage 개체 retry 함수는 선형 다시 시도 정책으로 설정됩니다. retry 함수는 요청을 다시 시도할지 여부를 결정하고, 요청을 다시 시도할 때까지 전에 대기할 시간(초)을 지정합니다. 1차 시도에 대한 초기 요청이 실패했을 때 2차 시도에 대해 다시 요청해야 하는 경우 **retry\_to\_secondary** 값을 true로 설정합니다. 샘플 응용 프로그램에서 사용자 지정 다시 시도 정책은 저장소 개체의 `retry_callback` 함수에 정의되어 있습니다.
 
다운로드하기 전에 Service 개체 [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 및 [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 함수가 정의됩니다. 이러한 함수는 다운로드가 성공적으로 완료되거나, 다운로드가 실패하고 다시 시도할 때 발생하는 이벤트 처리기를 정의합니다.  

# <a name="java-tabjava"></a>[Java](#tab/java)
다운로드한 응용 프로그램 폴더로 범위가 지정된 터미널 또는 명령 프롬프트를 열어 응용 프로그램을 실행할 수 있습니다. 여기에서 `mvn compile exec:java`를 입력하여 응용 프로그램을 실행합니다. 그런 다음, 응용 프로그램은 **HelloWorld.png** 이미지를 디렉터리에서 저장소 계정으로 업로드하고, 이미지가 보조 RA-GRS 끝점에 복제되었는지 확인합니다. 검사가 완료되면 다운로드하는 끝점을 다시 보고하는 동안 응용 프로그램은 이미지를 반복해서 다운로드하기 시작합니다.

Storage 개체 retry 함수는 선형 다시 시도 정책을 사용하도록 설정됩니다. retry 함수는 요청을 다시 시도할지 여부를 결정하고, 각 다시 시도 사이에 대기할 시간(초)을 지정합니다. **BlobRequestOptions**의 **LocationMode** 속성은 **PRIMARY\_THEN\_SECONDARY**로 설정됩니다. 이렇게 하면 **HelloWorld.png**를 다운로드하려고 할 때 기본 위치에 도달하지 못한 경우 보조 위치로 자동으로 전환할 수 있습니다.

---

## <a name="understand-the-sample-code"></a>샘플 코드 이해

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

### <a name="retry-event-handler"></a>이벤트 처리기 다시 시도

이미지 다운로드가 실패하고 다시 시도하도록 설정된 경우 `OperationContextRetrying` 이벤트 처리기가 호출됩니다. 응용 프로그램에 정의된 최대 다시 시도 횟수에 도달하면 요청의 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode)가 `SecondaryOnly`로 변경됩니다. 이 설정을 사용하면 응용 프로그램이 보조 끝점에서 이미지 다운로드를 강제로 시도합니다. 이 구성은 기본 끝점이 무한으로 다시 시도되지 않으므로 이미지를 요청하는 데 소요되는 시간이 줄여줍니다.
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>완료된 이미지 처리기 요청

이미지 다운로드가 성공하면 `OperationContextRequestCompleted` 이벤트 처리기가 호출됩니다. 응용 프로그램에서 보조 끝점을 사용하고 있는 경우 응용 프로그램은 최대 20회까지 이 끝점을 계속 사용합니다. 20회 후에 이 응용 프로그램은 [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode)를 `PrimaryThenSecondary`로 다시 설정하고 기본 끝점을 다시 반복합니다. 요청이 성공하면 응용 프로그램은 기본 끝점에서 읽기를 계속합니다.
 
```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-tabpython"></a>[Python] (#tab/python) 

### <a name="retry-event-handler"></a>이벤트 처리기 다시 시도

이미지 다운로드가 실패하고 다시 시도하도록 설정된 경우 `retry_callback` 이벤트 처리기가 호출됩니다. 응용 프로그램에 정의된 최대 다시 시도 횟수에 도달하면 요청의 [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python)가 `SECONDARY`로 변경됩니다. 이 설정을 사용하면 응용 프로그램이 보조 끝점에서 이미지 다운로드를 강제로 시도합니다. 이 구성은 기본 끝점이 무한으로 다시 시도되지 않으므로 이미지를 요청하는 데 소요되는 시간이 줄여줍니다.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>완료된 이미지 처리기 요청

이미지 다운로드가 성공하면 `response_callback` 이벤트 처리기가 호출됩니다. 응용 프로그램에서 보조 끝점을 사용하고 있는 경우 응용 프로그램은 최대 20회까지 이 끝점을 계속 사용합니다. 20회 후에 이 응용 프로그램은 [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python)를 `PRIMARY`로 다시 설정하고 기본 끝점을 다시 반복합니다. 요청이 성공하면 응용 프로그램은 기본 끝점에서 읽기를 계속합니다.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-tabjava"></a>[Java](#tab/java)

**BlobRequestOptions**의 **LocationMode** 속성이 **PRIMARY\_THEN\_SECONDARY**로 설정된 경우 Java를 사용하여 콜백 처리기를 정의할 필요가 없습니다. 이렇게 하면 **HelloWorld.png**를 다운로드하려고 할 때 기본 위치에 도달하지 못한 경우 보조 위치로 자동으로 전환할 수 있습니다.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```
---


## <a name="next-steps"></a>다음 단계

시리즈의 파트 1에서는 다음과 같이 RA-GRS 저장소 계정으로 응용 프로그램의 고가용성을 지원하는 방법에 대해 알아봤습니다.

> [!div class="checklist"]
> * 저장소 계정 만들기
> * 샘플 다운로드
> * 연결 문자열 설정
> * 콘솔 응용 프로그램 실행

시리즈의 파트 2로 진행하여 오류를 시뮬레이션하고 보조 RA-GRS 끝점을 사용하도록 응용 프로그램을 강제하는 방법을 알아 보세요.

> [!div class="nextstepaction"]
> [기본 저장소 끝점 연결 오류 시뮬레이션](storage-simulate-failure-ragrs-account-app.md)
