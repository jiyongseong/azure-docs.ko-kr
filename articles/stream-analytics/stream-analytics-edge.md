---
title: IoT Edge의 Azure Stream Analytics(미리 보기)
description: Azure Stream Analytics에서 Edge 작업을 만들고 Azure IoT Edge가 실행되는 장치에 배포합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 9a9608825cf041007c000729becb34e9a3063f92
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>IoT Edge의 Azure Stream Analytics(미리 보기)

> [!IMPORTANT]
> 이 기능은 미리 보기 상태입니다. 프로덕션 환경에서는 사용하지 않는 것이 좋습니다.
 
IoT Edge의 ASA(Azure Stream Analytics)는 개발자가 IoT 장치에 보다 가깝게 거의 실시간으로 분석 인텔리전스를 배포하여 장치 생성 데이터의 가치를 극대화할 수 있도록 합니다. 짧은 대기 시간, 복원력, 대역폭의 효율적 사용 및 규정 준수를 위해 고안된 이 기능을 사용하여 이제 기업에서는 업계 운영과 밀접한 컨트롤 논리를 배포하고, 클라우드에서 수행된 빅 데이터 분석을 보완할 수 있습니다.  
IoT Edge의 Azure Stream Analytics는 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 프레임워크 내에서 실행됩니다. ASA에서 작업을 만든 후, Azure IoT Hub를 사용하여 ASA 작업을 배포 및 관리합니다.
이 기능은 미리 보기로 제공되며, 질문이나 의견이 있는 경우 [이 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)를 사용하여 제품 팀에 문의할 수 있습니다. 

## <a name="scenarios"></a>시나리오
![높은 수준의 다이어그램](media/stream-analytics-edge/ASAedge_highlevel.png)

* **대기 시간이 짧은 명령 및 컨트롤**: 예를 들어 제조 안전 시스템은 매우 짧은 대기 시간으로 운영 데이터에 응답해야 합니다. Azure IoT Edge의 ASA를 사용하면 예외 상황이 감지될 때 거의 실시간으로 센서 데이터를 분석하고 명령을 실행하여 컴퓨터를 중지하거나 경고를 트리거할 수 있습니다.
*   **클라우드에 대한 연결 제한**: 원격 마이닝 장비, 연결된 선박 또는 해양 굴착 같은 중요한 업무용 시스템은 클라우드 연결이 간헐적으로 끊어지더라도 데이터를 분석하고 대응해야 합니다. ASA를 사용하면 스트리밍 논리가 네트워크 연결과는 별개로 실행되며, 추가 처리 및 저장을 위해 클라우드로 전송할 항목을 선택할 수 있습니다.
* **제한된 대역폭**: 제트 엔진 또는 연결된 자동차에서 생성하는 데이터 양이 매우 커서 데이터를 클라우드로 전송하기 전에 필터링하고 미리 처리해야 합니다. ASA를 사용하면 클라우드로 전송해야 하는 데이터를 필터링하거나 집계할 수 있습니다.
* **규정 준수**: 규정 준수에 따라 일부 데이터를 클라우드로 전송하기 전에 로컬로 익명화하거나 집계해야 할 수 있습니다.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics의 Edge 작업
### <a name="what-is-an-edge-job"></a>"edge" 작업이란 무엇인가요?

ASA Edge 작업은 [Azure IoT Edge 런타임](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)에서 모듈로 실행됩니다. 이 작업은 다음 두 부분으로 구성됩니다.
1.  작업 정의를 담당하는 클라우드 부분: 사용자가 클라우드의 입력, 출력, 쿼리 및 기타 설정(오류 이벤트 등)을 정의합니다.
2.  로컬로 실행되는 IoT Edge 모듈의 ASA. 이 부분은 ASA 복합 이벤트 처리 엔진을 포함하며 클라우드에서 작업 정의를 수신합니다. 

ASA는 IoT Hub를 사용하여 장치에 Edge 작업을 배포합니다. [IoT Edge 배포에 대한 추가 정보는 여기에서 확인할 수 있습니다](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Edge 작업](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>설치 지침
다음 표에는 간단한 단계가 설명되어 있습니다. 더 자세한 내용은 다음 섹션에 제공됩니다.
|      |단계   | 위치     | 메모   |
| ---   | ---   | ---       |  ---      |
| 1   | **ASA Edge 작업 만들기**   | Azure portal      |  새 작업을 선택하고 **Edge**를 **호스팅 환경**으로 선택합니다. <br> 이러한 작업은 클라우드에서 생성/관리되며 사용자 고유의 IoT Edge 장치에서 실행됩니다.     |
| 2   | **저장소 컨테이너 만들기**   | Azure portal       | 저장소 컨테이너는 작업 정의를 저장하는 데 사용되며 IoT 장치에서 액세스할 수 있습니다. <br>  모든 기존 저장소 컨테이너를 다시 사용할 수 있습니다.     |
| 3   | **장치에서 IoT Edge 환경 설정**   | 장치      | [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 또는 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)에 대한 지침          |
| 4   | **IoT Edge 장치에 ASA 배포**   | Azure portal      |  ASA 작업 정의는 이전에 만든 저장소 컨테이너로 내보내집니다.       |
[이 단계별 자습서](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)에 따라 IoT Edge에 첫 번째 ASA 작업을 배포할 수 있습니다. 다음 비디오는 IoT Edge 장치에서 Stream Analytics 작업을 실행하는 프로세스를 이해하는 데 도움이 됩니다.  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]



#### <a name="create-an-asa-edge-job"></a>ASA Edge 작업 만들기
> [!Note]
> 이 자습서에서는 Azure Portal을 사용하여 ASA 작업 만들기에 중점을 둡니다. [Visual Studio 플러그 인을 사용하여 ASA Edge 작업 만들기](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)도 사용할 수 있습니다.

1. Azure Portal에서 새 "Stream Analytics 작업"을 만듭니다. [새 ASA 작업을 만들기 위한 직접 링크](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)

2. 생성 화면에서 **Edge**를 **호스팅 환경**으로 선택합니다(다음 그림 참조). ![작업 생성](media/stream-analytics-edge/ASAEdge_create.png)
3. 작업 정의
    1. **입력 스트림 정의**. 작업에 대해 하나 또는 여러 개의 입력 스트림을 정의합니다.
    2. 참조 데이터를 정의합니다(선택 사항).
    3. **출력 스트림 정의**. 작업에 대해 하나 또는 여러 개의 출력 스트림을 정의합니다. 
    4. **쿼리 정의**. 인라인 편집기를 사용하여 클라우드에서 ASA 쿼리를 정의합니다. 컴파일러는 ASA Edge에 대해 사용되도록 설정된 구문을 자동으로 확인합니다. 샘플 데이터를 업로드하여 쿼리를 테스트할 수도 있습니다. 
4. 선택적 설정 지정
    1. **이벤트 순서**. Portal에서 정렬되지 않은 정책을 구성할 수 있습니다. [여기](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396)에서 설명서를 참조할 수 있습니다.
    2. **로캘**. 국제화 형식을 설정합니다.


#### <a name="create-a-storage-container"></a>저장소 컨테이너 만들기
ASA 컴파일된 쿼리 및 작업 구성을 내보내려면 저장소 컨테이너가 필요합니다. 특정 쿼리로 ASA Docker 이미지를 구성하는 데 사용됩니다. 
1. [이러한 지침](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)에 따라 Azure Portal에서 저장소 계정을 만듭니다. ASA에서 이 계정을 사용하기 위해 모든 기본 옵션을 유지할 수 있습니다.
2. 새로 만든 저장소 계정에서 Blob Storage 컨테이너를 만듭니다.
    1. "Blob"을 클릭하고 "+컨테이너"를 클릭합니다. 
    2. 이름을 입력하고 컨테이너를 "비공개"로 유지합니다.


> [!Note]
> 배포가 생성되면 ASA는 저장소 컨테이너로 작업 정의를 내보냅니다. 이 작업 정의는 배포 기간 동안 동일하게 유지됩니다. 결과적으로 Edge에서 실행되는 작업을 업데이트하려는 경우 ASA에서 작업을 편집하고 IoT Hub에서 새 배포를 만들어야 합니다.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>장치에서 IoT Edge 환경 설정
Edge 작업은 Azure IoT Edge를 실행하는 장치에 배포할 수 있습니다.
이를 위해 다음 단계를 수행해야 합니다.
- IoT Hub를 만듭니다.
- Edge 장치에 Docker 및 IoT Edge 런타임을 설치합니다.
- IoT Hub에서 장치를 "IoT Edge 장치"로 설정합니다.

이러한 단계는 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 또는 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)에 대한 IoT Edge 설명서에 나와 있습니다.  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>IoT Edge 장치에 ASA 배포
##### <a name="add-asa-to-your-deployment"></a>배포에 ASA 추가
- Azure Portal에서 IoT Hub를 열고, IoT Edge 탐색기로 이동한 후 장치 블레이드를 엽니다.
- **모듈 설정**을 선택한 후 **Azure 서비스 IoT Edge 모듈 가져오기**를 선택합니다.
- 구독 및 사용자가 만든 ASA Edge 작업을 선택합니다. 그런 후 저장소 계정을 선택합니다. 저장을 클릭합니다.
![모듈에 ASA 배포 추가](media/stream-analytics-edge/set_module.png)


> [!Note]
> 이 단계에서 ASA는 선택한 저장소 컨테이너에 대한 액세스를 요청하고 "EdgeJobs"라는 폴더를 만듭니다. 각 배포에 대해 "EdgeJobs" 폴더에 새 하위 폴더가 만들어집니다.
> 작업에 Edge 장치에 배포하기 위해 ASA는 작업 정의 파일에 대한 SAS(공유 액세스 서명)를 만듭니다. SAS 키는 장치 쌍을 사용하여 IoT Edge 장치에 안전하게 전송됩니다. 이 키의 만료 기간은 만든 날로부터 3년입니다.


IoT Edge 배포에 대한 자세한 내용은 [이 페이지](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)를 참조하세요.


##### <a name="configure-routes"></a>경로 구성
IoT Edge에서는 모듈 간에, 그리고 모듈과 IoT Hub 간에 선언적으로 경로를 지정하는 방법을 제공합니다. 전체 구문은 [여기](https://docs.microsoft.com/azure/iot-edge/module-composition)에 설명되어 있습니다.
ASA 작업에서 만든 입/출력의 이름은 라우팅을 위한 끝점으로 사용할 수 있습니다.  

###### <a name="example"></a>예
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
이 예제에서는 다음 그림에 설명된 시나리오에 대한 경로를 보여줍니다. 여기에는 이름이 "**ASA**"이고, 입력 "**temperature**"와 출력"**alert**"를 포함하는 Edge 작업이 포함되어 있습니다.
![라우팅 예제](media/stream-analytics-edge/RoutingExample.png)

이 예제에서는 다음 경로를 정의합니다.
- **tempSensor**의 모든 메시지는 **temperature**라는 입력에 대해 **ASA** 모듈로 전송됩니다.
- **ASA** 모듈의 모든 출력은 이 장치에 연결된 IoT Hub로 전송됩니다($upstream).
- **ASA** 모듈의 모든 출력은 **tempSensor**의 **control** 끝점으로 전송됩니다.


## <a name="technical-information"></a>기술 정보
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>클라우드 작업과 비교한 Edge 작업의 현재 제한 사항
목표는 Edge 작업 및 클라우드 간에 동등성을 유지하는 것입니다. SQL 쿼리 언어 기능의 대부분은 이미 지원됩니다.
그러나 다음 기능이 Edge 작업에서는 아직 지원되지 않습니다.
* UDF(사용자 정의 함수) 및 UDA(사용자 정의 집계)
* Azure ML 함수
* 한 번에 15개 이상의 집계 사용
* AVRO 형식의 입/출력 현재 CSV 및 JSON만 지원됩니다.
* 다음 SQL 연산자:
    * AnomalyDetection
    * 지리 공간적 연산자:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>런타임 및 하드웨어 요구 사항
IoT Edge에서 ASA를 실행하려면 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)를 실행 할 수 있는 장치가 필요합니다. 

ASA 및 Azure IoT Edge는 **Docker** 컨테이너를 사용하여 여러 호스트 OS(Windows, Linux)에서 실행되는 이식 가능한 솔루션을 제공합니다.

IoT Edge의 ASA는 x86-64 또는 ARM 아키텍처 둘 다에서 실행되는 Windows 및 Linux 이미지로 사용할 수 있습니다. 


### <a name="input-and-output"></a>입력 및 출력
#### <a name="input-and-output-streams"></a>입력 및 출력 스트림
ASA Edge 작업은 IoT Edge 장치에서 실행되는 다른 모듈에서 입/출력을 가져올 수 있습니다. 특정 모듈과 연결하려면 배포 시에 라우팅 구성을 설정할 수 있습니다. 자세한 내용은 [IoT Edge 모듈 컴퍼지션 설명서](https://docs.microsoft.com/azure/iot-edge/module-composition)를 참조하세요.

입/출력 모두에서 CSV 및 JSON 형식이 지원됩니다.

ASA 작업에서 생성한 각 입/출력 스트림에 해당하는 끝점이 배포 모듈에 생성됩니다. 이러한 끝점은 배포 경로에서 사용될 수 있습니다.



##### <a name="reference-data"></a>참조 데이터
참조 데이터(조회 테이블이라고도 함)는 정적이거나 느리게 변경되는 특성을 지닌 한정된 데이터 집합으로, 데이터 스트림을 조회하거나 상관 관계를 지정하는 데 사용됩니다. Azure Stream Analytics 작업에서 참조 데이터를 사용하려면 일반적으로 쿼리에서 [참조 데이터 조인](https://msdn.microsoft.com/library/azure/dn949258.aspx)을 사용합니다. 자세한 내용은 [참조 데이터에 대한 ASA 설명서](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)를 참조하세요.

Iot Edge에서 ASA에 대한 참조 데이터를 사용하려면 다음 단계를 수행해야 합니다. 
1. 작업에 대한 새 입력 만들기
2. **참조 데이터**를 **원본 유형**으로 선택합니다.
3. 파일 경로를 설정합니다. 파일 경로는 장치 ![참조 데이터 만들기](media/stream-analytics-edge/ReferenceData.png)에서 **절대** 파일 경로여야 합니다.
4. Docker 구성에서 **공유 드라이브**를 사용하도록 설정하고, 배포를 시작하기 전에 해당 드라이브가 사용되도록 설정되어 있는지 확인합니다.

자세한 내용은 [Windows용 Docker 설명서](https://docs.docker.com/docker-for-windows/#shared-drives)를 참조하세요.

> [!Note]
> 현재 로컬 참조 데이터만 지원됩니다.




## <a name="license-and-third-party-notices"></a>라이선스 및 타사 알림
* [IoT Edge의 Azure Stream Analytics 미리 보기 라이선스](https://go.microsoft.com/fwlink/?linkid=862827). 
* [IoT Edge의 Azure Stream Analytics에 대한 타사 알림 미리 보기](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Azure Iot Edge에 대한 추가 정보](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [IoT Edge의 ASA 자습서](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [이 설문 조사를 사용하여 팀에 의견 보내기](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Visual Studio 도구를 사용하여 Stream Analytics Edge 작업 개발](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
