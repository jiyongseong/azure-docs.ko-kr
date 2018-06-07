---
title: '자습서: Azure Portal을 사용하여 Stream Analytics 작업을 만들고 관리 | Microsoft Docs'
description: 이 자습서에서는 Azure Stream Analytics를 사용하여 전화 통화 스트림의 사기성 호출을 분석하는 방법에 대한 종단 간 일러스트레이션을 제공합니다.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 524b15747a275c76fec6c529e4f00d0da1b41420
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "32778192"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Power BI 대시보드에서 전화 통화 데이터를 분석하고 결과를 시각화하는 Stream Analytics 작업 만들기

이 자습서에는 Azure Stream Analytics를 사용하여 클라이언트 응용 프로그램에서 생성한 샘플 전화 통화를 분석하는 방법을 보여줍니다. 클라이언트 응용 프로그램에서 생성한 전화 통화 데이터에는 일부 사기성 호출이 포함되어 있으며 이러한 호출을 필터링하는 Stream Analytics 작업을 정의하겠습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 샘플 전화 통화 데이터를 생성하여 Azure Event Hubs로 보내기  
> * Stream Analytics 작업 만들기   
> * 작업의 입력 및 출력 구성  
> * 사기성 호출을 필터링하는 쿼리 정의  
> * 작업을 테스트 및 시작  
> * Power BI에서 결과 시각화 

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.  
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.  
* Microsoft 다운로드 센터에서 전화 통화 이벤트 생성기 앱 [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)을 다운로드하거나 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)에서 소스 코드를 가져올 수 있습니다.  

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기 

Stream Analytics가 사기성 호출 데이터 스트림을 분석하려면 먼저 Azure로 데이터를 보내야 합니다. 이 자습서에서는 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)를 사용하여 Azure로 데이터를 보낼 것입니다. 이 자습서에서는 이벤트 허브를 만든 후 호출 이벤트 생성기 앱에서 해당 이벤트 허브로 호출 데이터를 보내도록 합니다. 다음 단계를 실행하여 이벤트 허브를 만듭니다.

1. Azure 포털에 로그인합니다.  
2. **리소스 만들기** > **사물 인터넷** > **Event Hubs**를 선택합니다.  

   ![이벤트 허브 찾기](media/stream-analytics-manage-job/find-eh.png)
3. 다음 값으로 **네임스페이스 만들기** 창을 채웁니다.  

   |**설정**  |**제안 값** |**설명**  |
   |---------|---------|---------|
   |Name     | myEventHubNS        |  이벤트 허브 네임스페이스를 식별하는 고유 이름입니다.       |
   |구독     |   \<사용자의 구독\>      |   이벤트 허브를 만들 Azure 구독을 선택합니다.      |
   |리소스 그룹     |   MyASADemoRG      |  **새로 만들기**를 선택하고 계정의 새로운 리소스 그룹 이름을 입력합니다.       |
   |위치     |   미국 서부2      |    이벤트 허브 네임스페이스를 배포할 수 있는 위치입니다.     |

4. 나머지 설정에서는 기본 옵션을 사용하고 **만들기**를 선택합니다.  

   ![이벤트 허브 네임스페이스 만들기](media/stream-analytics-manage-job/create-ehns.png)

5. 네임스페이스가 배포를 마치면 **모든 리소스**로 이동하여 Azure 리소스 목록에서 "myEventHubNS"를 찾아 선택하여 엽니다.  
6. 다음으로 **+이벤트 허브** > **이름**을 선택하여 이벤트 허브 이름을 "MyEventHub"로 지정합니다. 다른 이름을 사용할 수 있습니다. 나머지 설정에서는 기본 옵션을 사용하고 **만들기**를 선택한 후 배포가 완료될 때까지 기다립니다.

   ![이벤트 허브 만들기](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>이벤트 허브에 대한 액세스 부여 및 연결 문자열 가져오기

응용 프로그램에서 Event Hubs로 데이터를 보낼 수 있으려면 이벤트 허브에 적절한 액세스 권한을 허용하는 정책이 있어야 합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1. 이전 단계에서 만든 **Event Hubs**인 "MyEventHub"로 이동하여 이벤트 허브 창에서 **공유 액세스 정책을** 선택하고 **+추가**를 선택합니다.  
2. 정책 이름을 **Mypolicy**로 설정하고 **관리** > **만들기**를 선택합니다.  

   ![이벤트 허브 공유 액세스 정책 만들기](media/stream-analytics-manage-job/create-ehpolicy.png)

3. 정책이 배포된 후에는 정책을 선택하여 열고, **연결 문자열-기본 키**를 찾아서 연결 문자열 옆에 있는 **복사**를 선택합니다.  
4. 연결 문자열을 텍스트 편집기에 붙여넣습니다. 그 다음 섹션에서 이 연결 문자열이 필요합니다.  

   연결 문자열은 다음과 비슷합니다.

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   연결 문자열에는 세미콜론으로 구분된 여러 키-값 쌍이 포함되어 있습니다(Endpoint, SharedAccessKeyName, SharedAccessKey 및 EntityPath).  

5. 연결 문자열에서 EntityPath 쌍을 제거합니다(앞에 나오는 세미콜론을 반드시 제거).

## <a name="start-the-event-generator-application"></a>이벤트 생성기 응용 프로그램 시작

TelcoGenerator 앱을 시작하기 전에, 앞에서 만든 Azure Event Hubs로 데이터를 전송하도록 앱을 구성해야 합니다.

1. [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) 파일의 콘텐츠를 추출합니다.  
2. 원하는 텍스트 편집기에서 `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` 파일을 엽니다(.config 파일이 여러 개 있으므로 올바른 파일을 열어야 함).  

3. 구성 파일의 <appSettings> 요소를 다음 세부 정보로 업데이트합니다.

   * EventHubName 키의 값을 연결 문자열의 EntityPath 값으로 설정합니다.  
   * Microsoft.ServiceBus.ConnectionString 키 값을 이전 섹션의 5단계에서 가져온 EntityPath 값을 뺀 연결 문자열로 설정합니다.

4. 파일을 저장합니다.  
5. 다음으로 명령 창을 열고, TelcoGenerator 응용 프로그램을 압축 해제한 폴더로 변경하고, 다음 명령을 입력합니다.

   ```
   telcodatagen.exe 1000 .2 2
   ```

   이 명령은 다음 매개 변수를 사용합니다.
   * **시간당 호출 데이터 레코드 수**.  
   * **사기 확률** - 다시 말해서, 앱에서 사기성 호출을 시뮬레이션해야 하는 빈도. 값 .2는 호출 레코드의 약 20%가 사기성으로 나타남을 의미합니다.  
   * **기간** - 앱이 실행되어야 하는 시간. 또한 언제든지 명령줄에서 프로세스를 종료하여(Ctrl + C) 앱을 중지할 수 있습니다.

   몇 초 후 앱에서 이벤트 허브로 데이터를 전송함에 따라 화면에 전화 통화 레코드가 표시되기 시작합니다. 이 전화 통화 데이터에는 다음 필드가 포함되어 있습니다.

   |**레코드**  |**정의**  |
   |---------|---------|
   |CallrecTime    |  호출 시작 시간에 대한 타임스탬프       |
   |SwitchNum     |  호출 연결에 사용되는 전화 스위치. 이 예에서는 스위치는 발신 국가(미국, 중국, 영국, 독일 또는 오스트레일리아)를 나타내는 문자열입니다.       |
   |CallingNum     |  호출자의 전화번호.       |
   |CallingIMSI     |  국제 모바일 구독자 ID(IMSI) 호출자의 고유 식별자.       |
   |CalledNum     |   호출 수신자의 전화번호.      |
   |CalledIMSI     |  국제 모바일 구독자 ID(IMSI) 호출 수신자의 고유 식별자.       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기 

이제 호출 이벤트 스트림이 생겼으니, 이벤트 허브에서 데이터를 읽는 Stream Analytics 작업을 만들 수 있습니다.

1. Stream Analytics 작업을 만들려면 Azure Portal로 이동합니다.  

2. **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 선택합니다.  

3. **새 Stream Analytics 작업** 창을 다음 값으로 채웁니다.  

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |작업 이름     |  ASATutorial       |   이벤트 허브 네임스페이스를 식별하는 고유 이름입니다.      |
   |구독    |  \<사용자의 구독\>   |   작업을 만들 Azure 구독을 선택합니다.       |
   |리소스 그룹   |   MyASADemoRG      |   **기존 항목 사용**을 선택하고 계정의 새로운 리소스 그룹 이름을 입력합니다.      |
   |위치   |    미국 서부2     |      작업을 배포할 수 있는 위치입니다. 최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.      |
   |호스팅 환경    | 클라우드        |     Stream Analytics 작업은 클라우드 또는 에지에 배포할 수 있습니다. 클라우드를 사용하면 Azure Cloud에 배포할 수 있고, 에지를 사용하면 IoT 에지 장치에 배포할 수 있습니다.    |
   |스트리밍 단위     |    1       |      스트리밍 단위는 작업을 실행하는 데 필요한 컴퓨팅 리소스를 나타냅니다. 기본적으로 이 값은 1로 설정됩니다. 스트리밍 단위 크기를 조정하는 방법에 대한 자세한 내용은 [스트리밍 단위의 이해 및 크기 조정](stream-analytics-streaming-unit-consumption.md) 문서를 참조하세요.      |

   ![작업 만들기](media/stream-analytics-manage-job/create-a-job.png)   

4. 나머지 설정에서는 기본 옵션을 사용하고 **만들기**를 선택한 후 배포가 완료될 때까지 기다립니다.

## <a name="configure-job-input"></a>작업 입력 구성

그 다음 단계는 데이터 읽기 작업에 대한 입력 원본을 정의하는 것입니다. 이 자습서에서는 이전 섹션에서 입력으로 만든 이벤트 허브를 사용합니다. 다음 단계를 실행하여 작업에 대한 입력을 구성합니다.

1. Azure Portal에서 **모든 리소스** 창을 열고 ASATutorial Stream Analytics 작업을 찾습니다.  

2. Stream Analytics 작업 창의 **작업 토폴로지** 섹션에서 **입력** 옵션을 선택합니다.  

3. **+스트림 입력 추가**를 선택하고(참조 입력은 정적 조회 데이터를 참조하며, 이 자습서에서는 사용하지 않음), **이벤트 허브**를 선택하고, 창을 다음 값으로 채웁니다.  

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |입력 별칭     |  CallStream       |  입력을 식별하는 표시 이름. 입력 별칭은 영숫자 문자, 하이픈, 밑줄만 사용할 수 있으며 길이가 3자에서 63자 사이여야 합니다.       |
   |구독    |   \<사용자의 구독\>      |   이벤트 허브를 만든 Azure 구독을 선택합니다. 이벤트 허브는 Stream Analytics 작업과 같은 구독일 수도 있고 다른 구독일 수도 있습니다.       |
   |이벤트 허브 네임스페이스    |  MyEventHubNS       |  이전 섹션에서 만든 이벤트 허브 네임스페이스를 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 허브 네임스페이스가 드롭다운에 나열됩니다.       |
   |이벤트 허브 이름    |   MyEventHub      |  이전 섹션에서 만든 이벤트 허브를 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 허브가 드롭다운에 나열됩니다.       |
   |이벤트 허브 정책 이름   |  Mypolicy       |  이전 섹션에서 만든 이벤트 허브 공유 액세스 정책을 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 정책이 드롭다운에 나열됩니다.       |

   ![입력 구성](media/stream-analytics-manage-job/configure-input.png) 

4. 나머지 설정에서는 기본 옵션을 사용하고 **저장**을 선택한 후 배포가 완료될 때까지 기다립니다.

## <a name="configure-job-output"></a>작업 출력 구성 

마지막 단계는 변환된 데이터를 기록할 수 있는 작업의 출력 싱크를 정의하는 것입니다. 이 자습서에서는 결과를 Power BI로 출력하고 데이터를 시각화할 것입니다. 다음 단계를 실행하여 작업에 대한 출력을 구성합니다.

1. Azure Portal에서 **모든 리소스** 창을 열고 ASATutorial Stream Analytics 작업을 찾습니다.  

2. Stream Analytics 작업 창의 **작업 토폴로지** 섹션에서 **출력** 옵션을 선택합니다.  

3. **+추가** > **Power BI**를 선택하고 다음 세부 정보(표에서 보여주는 것처럼 출력 별칭, 데이터 집합 이름 및 테이블 이름을 식별하는 표시 이름을 입력할 수 있음)를 양식에 입력한 후 **권한 부여**를 선택합니다.  

   |**설정**  |**제안 값**  |
   |---------|---------|---------|
   |출력 별칭  |  MyPBIoutput  |
   |데이터 집합 이름  |   ASAdataset  | 
   |테이블 이름 |  ASATable  | 

   ![출력 구성](media/stream-analytics-manage-job/configure-output.png)  

4. **권한 부여**를 선택하면 팝업 창이 열리고, Power BI 계정에 인증하려면 자격 증명을 입력하라는 메시지가 표시됩니다. 권한 부여가 완료되면 설정을 **저장**합니다. 

## <a name="define-a-query-to-analyze-input-data"></a>입력 데이터를 분석하는 쿼리 정의

들어오는 데이터 스트림을 읽을 Stream Analytics 작업을 설정한 후에 할 일은 데이터를 실시간으로 분석하는 변환을 만드는 것입니다. [Stream Analytics 쿼리 언어](https://msdn.microsoft.com/library/dn834998.aspx)를 사용하여 변환 쿼리를 정의합니다. 이 자습서에서는 전화 데이터에서 사기성 호출을 검색하는 쿼리를 정의합니다. 

이 예제의 경우 사기성 호출이 위치가 다른 동일한 사용자에게서 오며 두 호출 간의 기간이 5초라고 가정합니다. 예를 들어 동일한 사용자가 미국 및 오스트레일리아에서 동시에 합법적으로 전화를 걸 수 없습니다. Stream Analytics 작업에 대한 변환 쿼리를 정의하려면 다음 단계를 실행합니다.

1. Azure Portal에서 **모든 리소스** 창을 열고 앞에서 만든 **ASATutorial** Stream Analytics 작업을 엽니다.  

2. Stream Analytics 작업 창의 **작업 토폴로지** 섹션에서 **쿼리** 옵션을 선택합니다. 팝업 창에는 작업에 대해 구성된 입력 및 출력이 나열되며, 입력 스트림을 변환하는 쿼리를 만들 수 있습니다.  

3. 다음으로 편집기의 기존 쿼리를 다음 데이터로 바꿉니다. 이 쿼리는 호출 데이터를 5초 간격으로 자체 조인합니다.

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   사기성 호출을 검사하려면 `CallRecTime` 값에 따라 스트리밍 데이터를 자체 조인해야 합니다. 그 후 `CallingIMSI` 값(발신 번호)이 동일하지만 `SwitchNum` 값(발신 국가)은 다른 호출 레코드를 찾을 수 있습니다. 스트리밍 데이터에 JOIN 작업을 사용할 경우 조인은 일치하는 행이 시간상으로 얼마나 분리할 수 있는지 정도에 대한 몇 가지 한도를 제공해야 합니다. 스트리밍 데이터가 무한하기 때문에 관계에 대한 시간 범위는 [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) 함수를 사용하여 조인의 ON 절 내에서 지정됩니다. 

   이 쿼리는 DATEDIFF 함수를 제외하고 일반 SQL 조인과 흡사합니다. 이 쿼리에 사용되는 DATEDIFF 함수는 Stream Analytics로 한정되며, `ON...BETWEEN` 절 내부에 나타나야 합니다.  

4. 쿼리를 **저장**합니다.  

   ![쿼리 정의](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>쿼리 테스트

쿼리 편집기에서 쿼리를 테스트할 수 있으며, 쿼리를 테스트하려면 샘플 데이터가 필요합니다. 이 연습에서는 이벤트 허브로 들어오는 전화 호출 스트림에서 샘플 데이터를 추출합니다. 쿼리를 테스트하려면 다음 단계를 실행합니다.

1. TelcoGenerator 앱이 실행 중이고 전화 호출 레코드를 생성하고 있는지 확인합니다.  

2. **쿼리** 창에서 CallStream 입력 옆에 있는 점을 선택한 후 **입력의 샘플 데이터**를 선택합니다. 그러면 입력 스트림에서 읽을 샘플 데이터의 양을 지정할 수 있는 창이 열립니다.  

   ![샘플 입력 데이터](media/stream-analytics-manage-job/sample-input-data.png)  

3. **분**을 3으로 설정하고 **확인**을 선택합니다. 입력 스트림에서 3분 분량의 데이터가 샘플링되며 샘플 데이터가 준비되면 사용자에게 알려줍니다. 알림 표시줄에서 샘플링 상태를 볼 수 있습니다. 

   샘플 데이터는 임시로 저장되었다가 쿼리 창이 열려 있는 동안 사용할 수 있습니다. 쿼리 창을 닫으면 샘플 데이터가 무시되고 새로운 샘플 데이터 집합을 만들어야 합니다. 대안으로, 안에 샘플 데이터가 있는 .json 파일을 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/telco.json)에서 가져온 후 CallStream 입력에 대한 샘플 데이터로 사용할 .json 파일을 업로드할 수 있습니다.  

4. **테스트**를 선택하여 쿼리를 테스트합니다. 이 스크린 샷과 비슷한 출력 결과가 표시될 것입니다.  

   ![테스트 출력](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>작업을 시작하고 출력을 시각화

1. 작업을 시작하려면 작업의 **개요** 창으로 이동하고 **시작**을 선택합니다.  

2. 작업 출력 시작 시간으로 **지금**을 선택하고 **시작**을 선택합니다. 몇 분 후 작업이 시작되고 알림 표시줄에서 상태를 볼 수 있습니다.  

3. 작업이 성공한 후에는 [Powerbi.com](https://powerbi.com/)으로 이동하여 회사 또는 학교 계정으로 로그인합니다. Stream Analytics 작업 쿼리가 결과를 출력하면 데이터 집합이 이미 생성된 것을 볼 수 있습니다. **데이터 집합** 탭으로 이동하면 "ASAdataset"라는 데이터 집합이 보입니다.  

4. 작업 영역에서 **+만들기**를 선택합니다. 새 대시보드를 만들고 이름을 Fraudulent Calls로 지정합니다. 이 대시보드에 타일 두 개를 추가할 것입니다. 하나는 특정 인스턴스의 사기성 호출 수를 보는 데 사용되고, 다른 하나는 꺾은선형 차트 시각화를 제공합니다.  

5. 창의 맨 위에서 **타일 추가** > **사용자 지정 스트리밍 데이터** > 다음 > **ASAdataset**를 선택하고 시각화 유형으로는 **카드**를, 필드로는 **fraudulentcalls**로 선택합니다. **다음**을 선택하고 타일 이름을 입력한 후 **적용**을 선택합니다.  

   ![타일 만들기](media/stream-analytics-manage-job/create-tiles.png)

6. 다음 옵션을 사용하여 4단계를 다시 수행합니다.
   * 시각화 유형으로 꺾은선형 차트를 선택합니다.  
   * 축을 추가하고 **windowend**를 선택합니다.  
   * 값을 추가하고 **fraudulentcalls**를 선택합니다.  
   * **표시할 시간 창**에 지난 10분을 선택합니다.  

7. 두 타일을 추가하면 대시보드가 다음 스크린샷처럼 보입니다. 이벤트 허브 발신자 응용 프로그램 및 Stream Analytics 응용 프로그램이 실행 중이면 새 데이터가 도착할 때마다 PowerBI 대시보드가 주기적으로 업데이트됩니다.  

   ![Power BI 결과](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>웹 응용 프로그램에 PowerBI 대시보드 포함

자습서의 이 부분에서는 PowerBI 팀에서 만든 [ASP.NET](http://asp.net/) 웹 응용 프로그램을 사용하여 대시보드를 포함할 것입니다. 대시보드를 포함하는 방법에 대한 자세한 내용은 [Power BI를 통해 포함](https://docs.microsoft.com/power-bi/developer/embedding) 문서를 참조하세요.

이 자습서에서는 사용자 소유 데이터 응용 프로그램에 대한 단계를 따르겠습니다. 응용 프로그램을 설정하려면 [PowerBI-개발자-샘플](https://github.com/Microsoft/PowerBI-Developer-Samples) Github 리포지토리로 이동하여 **사용자 소유 데이터** 섹션 아래의 지침을 따릅니다(**통합-대시보드-웹앱** 하위 섹션 아래에 있는 리디렉션 및 홈페이지 URL 사용). 우리는 대시보드 예제를 사용할 예정이므로 [GitHub 리포지토리](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User Owns Data/integrate-dashboard-web-app)에 있는 통합-대시보드-웹앱 샘플 코드를 사용합니다.
브라우저에서 응용 프로그램을 실행한 후에는 다음 단계에 따라 앞에서 만든 대시보드를 웹 페이지에 포함합니다.

1. **Power BI에 로그인**을 선택합니다. 그러면 PowerBI 계정에 대시보드에 대한 응용 프로그램 액세스 권한이 부여됩니다.  

2. **대시보드 가져오기** 단추를 선택합니다. 그러면 계정의 대시보드가 테이블에 표시됩니다. 앞에서 만든 대시보드 이름(powerbi-embedded-dashboard)을 찾아서 해당 **EmbedUrl**을 복사합니다.  

3. 마지막으로 **EmbedUrl**을 해당 텍스트 필드에 붙여넣고 **대시보드 포함**을 선택합니다. 이제 웹 응용 프로그램에 동일한 대시보드가 포함된 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 간단한 Stream Analytics 작업을 만들고, 들어오는 데이터를 분석하고, 그 결과를 Power BI 대시보드에 표시했습니다. Stream Analytics 작업에 대해 자세히 알아보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Stream Analytics 작업에서 Azure Functions 실행](stream-analytics-with-azure-functions.md)
