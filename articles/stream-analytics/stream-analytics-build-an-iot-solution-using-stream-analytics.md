---
title: Azure Stream Analytics를 사용하여 IoT 솔루션 빌드
description: 요금 창구 시나리오의 Stream Analytics IoT 솔루션 시작하기 자습서
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Stream Analytics를 사용하여 IoT 솔루션 빌드

## <a name="introduction"></a>소개
이 솔루션에서는 Azure Stream Analytics를 사용하여 데이터에서 실시간으로 정보를 얻는 방법을 알아봅니다. 개발자는 클릭 스트림, 로그 및 장치에서 생성된 이벤트와 같은 데이터 스트림을 기록 레코드 또는 참조 데이터와 조합하여 비즈니스 통찰력을 얻을 수 있습니다. Azure Stream Analytics는 Microsoft Azure에 호스트된 완전히 관리되는 실시간 스트림 계산 서비스로, 기본 제공 복원력, 낮은 대기 시간 및 확장성을 제공하여 몇 분 안에 실행할 수 있습니다.

이 솔루션을 완료한 후에 다음을 수행할 수 있습니다.

* Azure Stream Analytics 포털 숙지
* 스트리밍 작업 구성 및 배포
* Stream Analytics 쿼리 언어를 사용하여 실제 문제 명시 및 해결
* 안심하고 Stream Analytics를 사용하여 고객에 대한 스트리밍 솔루션 개발
* 모니터링 및 로깅 경험을 사용하여 문제 해결

## <a name="prerequisites"></a>필수 조건
이 솔루션을 완료하려면 다음 필수 구성 요소가 필요합니다.
* [Azure 구독](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>시나리오 소개: “Hello, Toll!”
톨게이트 요금소는 일반적인 현상입니다. 전 세계의 많은 고속도로, 다리 및 터널에서 이러한 요금소를 만날 수 있습니다. 각 요금소에는 여러 개의 요금 창구가 있습니다. 유인 창구에서는 직원에게 요금을 지불하기 위해 멈춰야 합니다. 무인 창구에서는 차량이 톨게이트 창구를 통과할 때 각 창구 상단의 센서가 차량의 윈드쉴드에 부착된 RFID 카드를 검색합니다. 이러한 요금소를 통과하는 차량 흐름을 흥미로운 작업을 수행할 수 있는 이벤트 스트림으로 쉽게 시각화할 수 있습니다.

![요금 창구에 서 있는 자동차 사진](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>들어오는 데이터
이 솔루션에서는 두 가지 데이터 스트림을 사용합니다. 톨게이트 요금소의 입구 및 출구에 설치된 센서가 첫 번째 스트림을 생성합니다. 두 번째 스트림은 차량 등록 데이터가 포함된 정적 조회 데이터 집합입니다.

### <a name="entry-data-stream"></a>진입 데이터 스트림
진입 데이터 스트림에는 톨게이트 요금소로 들어가는 자동차에 대한 정보가 포함됩니다. 종료 데이터 이벤트는 샘플 앱에 포함된 Web App의 Event Hub 큐에 라이브 스트리밍됩니다.

| TollId | EntryTime | LicensePlate | 시스템 상태 | 계정을 | 모델 | VehicleType | VehicleWeight | Toll | 태그 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |1007 BNJ |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

다음은 열에 대한 간단한 설명입니다.

| 열 | 설명 |
| --- | --- |
| TollId |톨게이트 요금소를 고유하게 식별하는 요금 창구 ID |
| EntryTime |요금 창구에 차량이 진입하는 날짜 및 시간(UTC) |
| LicensePlate |차량 번호판 번호 |
| 시스템 상태 |미국의 주 이름 |
| 계정을 |자동차 제조업체 |
| 모델 |자동차 모델 번호 |
| VehicleType |여객 차량의 경우 1, 화물 차량의 경우 2 |
| WeightType |차량 무게(톤), 승용차는 0 |
| Toll |통행료 값(USD) |
| 태그 |요금을 수동으로 지불할 때 이 지불을 자동으로 비워놓는 자동차의 e-태그 |

### <a name="exit-data-stream"></a>진출 데이터 스트림
진출 데이터 스트림에는 요금소를 떠나는 차량에 대한 정보가 포함됩니다. 종료 데이터 이벤트는 샘플 앱에 포함된 Web App의 Event Hub 큐에 라이브 스트리밍됩니다.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |1007 BNJ |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

다음은 열에 대한 간단한 설명입니다.

| 열 | 설명 |
| --- | --- |
| TollId |톨게이트 요금소를 고유하게 식별하는 요금 창구 ID |
| ExitTime |차량이 요금소를 빠져 나가는 날짜 및 시간(UTC) |
| LicensePlate |차량 번호판 번호 |

### <a name="commercial-vehicle-registration-data"></a>화물 차량 등록 데이터
이 솔루션에서는 화물 차량 등록 데이터베이스의 정적 스냅숏을 사용합니다. 이 데이터는 샘플에 포함된 Azure Blob Storage에 JSON 파일로 저장됩니다.

| LicensePlate | RegistrationId | 만료됨 |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

다음은 열에 대한 간단한 설명입니다.

| 열 | 설명 |
| --- | --- |
| LicensePlate |차량 번호판 번호 |
| RegistrationId |차량 등록 ID |
| 만료됨 |차량의 등록 상태: 차량 등록이 활성화된 경우는 0, 등록이 만료된 경우는 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Azure Stream Analytics를 위한 환경 설정
이 솔루션을 완료하려면 Microsoft Azure 구독이 필요합니다. Azure 계정이 없는 경우 [평가판 버전을 요청](http://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.

이 문서 끝에 나오는 "Azure 계정 정리" 섹션의 단계를 수행해야 Azure 크레딧을 최대한 활용할 수 있습니다.

## <a name="deploy-the-sample"></a>샘플 배포 
몇 번 클릭하여 리소스 그룹에 쉽게 배포할 수 있는 몇 가지 리소스가 있습니다. 솔루션 정의는 GitHub 리포지토리([https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp))에서 호스팅됩니다.

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Azure Portal에서 TollApp 템플릿 배포
1. TollApp 환경을 Azure에 배포하려면 [TollApp Azure 템플릿 배포](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)에 대한 이 링크를 사용하세요.

2. 메시지가 표시되면 Azure Portal에 로그인합니다.

3. 다양한 리소스가 청구되는 구독을 선택합니다.

4. 고유한 이름의 새 리소스 그룹을 지정합니다(예: `MyTollBooth`). 

5. Azure 위치를 선택합니다.

6. **간격**을 몇 초로 지정합니다. 이 값은 샘플 웹앱에서 Event Hub로 데이터를 전송하는 빈도에 사용됩니다. 

7. 사용 약관에 동의하려면 **확인**합니다.

8. 리소스를 나중에 쉽게 찾을 수 있도록 **대시보드에 고정**을 선택합니다.

9. **구매**를 선택하여 샘플 템플릿을 배포합니다.

10. 몇 분 후에 **배포 성공**을 확인하는 알림이 표시됩니다.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Azure Stream Analytics TollApp 리소스 검토
1. Azure Portal에 로그인

2. 이전 섹션에서 명명된 리소스 그룹을 찾습니다.

3. 다음 리소스가 리소스 그룹에 나열되어 있는지 확인합니다.
   - 하나의 Cosmos DB 계정
   - 하나의 Azure Stream Analytics 작업
   - 하나의 Azure Storage 계정
   - 하나의 Azure Event Hub
   - 두 개의 Web Apps

## <a name="examine-the-sample-tollapp-job"></a>샘플 TollApp 작업 검사 
1. 이전 섹션의 리소스 그룹에서 시작하여 **tollapp**으로 시작하는 Stream Analytics 스트리밍 작업을 선택합니다(고유한 이름을 만들기 위해 임의 문자 포함).

2. 작업의 **개요** 페이지에서 쿼리 구문을 보는 **쿼리** 상자가 표시됩니다.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   쿼리를 설명하기 위해 요금소 창구에 진입하는 차량 수를 계산해야 한다고 가정해 보겠습니다. 고속도로 요금소 창구에 차량 진입의 연속 스트림이 있기 때문에 진입 이벤트는 중지되지 않는 스트림과 유사합니다. 스트림을 수량화하기 위해 측정할 "시간"을 정의해야 합니다. 질문을 "3분 간격으로 요금소 창구에 진입하는 차량은 몇 대입니까?"로 더 구체화해 보겠습니다. 이를 일반적으로 연속 개수(Tumbling Count)라고 합니다.

   아시다시피 Azure Stream Analytics는 SQL과 유사한 쿼리를 사용하고 몇 가지 확장을 추가하여 쿼리의 시간 관련 측면을 지정합니다.  자세한 내용은 쿼리에 사용된 [시간 관리](https://msdn.microsoft.com/library/azure/mt582045.aspx) 및 [창 작업](https://msdn.microsoft.com/library/azure/dn835019.aspx) 구문을 참조하세요.

3. TollApp 샘플 작업의 입력을 검사합니다. 현재 쿼리에 EntryStream 입력만 사용됩니다.
   - **EntryStream** 입력은 자동차가 고속도로의 요금소에 진입할 때마다 나타내는 데이터를 큐에 대기하는 Event Hub 연결입니다. 이 샘플의 일부인 웹앱은 이벤트를 만들고, 해당 데이터는 이 Event Hub에서 큐에 대기합니다. 이 입력은 스트리밍 쿼리의 FROM 절에 쿼리됩니다.
   - **ExitStream** 입력은 자동차가 고속도로의 요금소에서 퇴출할 때마다 나타내는 데이터를 큐에 대기하는 Event Hub 연결입니다. 이 스트리밍 입력은 쿼리 구문의 이후 변형에 사용됩니다.
   - **Registration** 입력은 필요에 따라 조회에 사용되는 고정 registration.json 파일을 가리키는 Azure Blob Storage 연결입니다. 이 참조 데이터 입력은 쿼리 구문의 이후 변형에 사용됩니다.

4. TollApp 샘플 작업의 출력을 검사합니다.
   - **Cosmos DB** 출력은 출력 싱크 이벤트를 받는 Cosmos 데이터베이스 컬렉션입니다. 이 출력은 스트리밍 쿼리의 INTO 절에 사용합니다.

## <a name="start-the-tollapp-streaming-job"></a>TollApp 스트리밍 작업 시작
스트리밍 작업을 시작하려면 다음 단계를 수행합니다.

1. 작업의 **개요** 페이지에서 **시작**을 선택합니다.

2. **시작 작업** 창에서 **지금**을 선택합니다.

3. 몇 분 후에 작업이 실행되면 스트리밍 작업의 **개요** 페이지에서 **모니터링** 그래프가 표시됩니다. 그래프에는 몇 천 개의 입력 이벤트 및 수 십 개의 출력 이벤트가 표시됩니다.

## <a name="review-the-cosmosdb-output-data"></a>CosmosDB 출력 데이터 검토
1. TollApp 리소스를 포함하는 리소스 그룹을 찾습니다.

2. **tollapp<random>-cosmos** 이름 패턴을 가진 Azure Cosmos DB 계정을 선택합니다.

3. **데이터 탐색기** 제목을 선택하여 데이터 탐색기 페이지를 엽니다.

4. **tollAppDatabase** > **tollAppCollection** > **Documents**를 확장합니다.

5. 출력이 지원되면 ID 목록에서 여러 문서가 표시됩니다.

6. JSON 문서를 검토하려는 각 ID를 선택합니다. 각각 tollid, windowend 시간 및 해당 창에서 자동차 대수입니다.

7. 추가로 3분 후에 다른 4개의 문서 집합이 지원됩니다(tollid당 문서 하나). 


## <a name="report-total-time-for-each-car"></a>각 자동차에 대한 총 시간 보고
자동차가 요금소를 통과하는 데 필요한 평균 시간은 프로세스의 효율성과 고객 경험을 평가하는 데 도움이 됩니다.

총 시간을 확인하려면 ExitTime 스트림과 EntryTime 스트림을 조인합니다. 동일하게 일치하는 TollId 및 LicencePlate 열에서 두 개의 입력 스트림을 조인합니다. **JOIN** 연산자에서는 조인된 이벤트 간에 허용할 수 있는 시간 차이를 설명하는 일시적인 시간 여유를 지정해야 합니다. **DATEDIFF** 함수를 사용하여 이벤트 사이의 간격이 15분 이하가 되도록 지정합니다. **DATEDIFF** 함수를 진출과 진입 시간에 적용하여 차량이 요금소에서 사용하는 실제 시간을 계산합니다. **SELECT** 문에서 **DATEDIFF**를 사용할 때 **JOIN** 조건에서 사용하는 것에 비해 어떤 차이가 있는지 적어둡니다.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>TollApp 스트리밍 작업 쿼리 구문을 업데이트하려면:

1. 작업의 **개요** 페이지에서 **중지**를 선택합니다.

2. 작업이 중지되었다는 알림을 잠시 기다립니다.

3. JOB TOPOLOGY 제목 아래에서 **< > 쿼리**를 선택합니다.

4. 조정된 스트리밍 SQL 쿼리를 붙여넣습니다.

5. **저장**을 선택하여 쿼리를 저장합니다. 변경 내용을 저장하려면 **예**를 선택합니다.

6. 작업의 **개요** 페이지에서 **시작**을 선택합니다.

7. **시작 작업** 창에서 **지금**을 선택합니다.

### <a name="review-the-total-time-in-the-output"></a>출력에서 총 시간 검토
스트리밍 작업에서 CosmosDB 출력 데이터를 검토하려면 이전 섹션의 단계를 반복합니다. 최신 JSON 문서 검토 

예를 들어 이 문서에서는 2분으로 요금소 기간을 보여주는 특정 번호판, 진입 시간과 퇴출 시간 및 DATEDIFF 계산 시간(분) 필드를 포함한 예제 자동차를 보여줍니다. 
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>등록이 만료된 차량 보고
Azure Stream Analytics는 참조 데이터의 고정 스냅숏을 사용하여 임시 데이터 스트림과 조인할 수 있습니다. 이 기능을 보여 주기 위해 다음 샘플 질문을 사용하겠습니다. 등록 입력은 라이선스 태그의 만료를 나열하는 고정 Blob json 파일입니다. 번호판에 조인하여 요금소를 통과하는 각 자동차에 참조 데이터를 비교합니다. 

화물 차량이 요금 회사에 등록된 경우 검사받기 위해 정차하지 않고 요금 창구를 통과할 수 있습니다. 등록 조회 표를 사용하여 등록 기간이 만료된 모든 화물 차량을 식별합니다.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. TollApp 스트리밍 작업 쿼리 구문을 업데이트하는 이전 섹션의 단계를 반복합니다.

2. 스트리밍 작업에서 CosmosDB 출력 데이터를 검토하려면 이전 섹션의 단계를 반복합니다. 

예제 출력:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>작업 규모 확장
Azure Stream Analytics는 탄력적으로 크기를 조정하여 대량의 데이터를 처리할 수 있도록 디자인되었습니다. Azure Stream Analytics 쿼리에서 **PARTITION BY** 절을 사용하면 이 단계에서 확장하는 시스템을 알 수 있습니다. **PartitionId**는 입력(이벤트 허브)의 파티션 ID와 일치하도록 시스템이 추가하는 특수한 열입니다.

파티션에 대한 쿼리 규모를 확장하려면 쿼리 구문을 다음 코드로 편집합니다.
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

더 큰 스트리밍 단위로 스트리밍 작업 규모를 확장하려면:

1. 현재 작업을 **중지**합니다. 

2. **< > 쿼리** 페이지에서 쿼리 구문을 업데이트하고 변경 내용을 저장합니다.

3. 스트리밍 작업의 CONFIGURE 제목 아래에서 **크기 조정**을 선택합니다.
   
4. **스트리밍 단위** 슬라이더를 1에서 6으로 조정합니다. 스트리밍 단위는 작업이 검색할 수 있는 계산 능력의 크기를 정의합니다. **저장**을 선택합니다.

5. 추가 규모 조정을 보여주는 스트리밍 작업을 **시작**합니다. Azure Stream Analytics에서는 추가 계산 리소스에 작업을 분산하고 더 많은 처리량을 달성할 뿐만 아니라 PARTITION BY 절에 지정된 열을 사용하여 리소스에 작업을 분할합니다. 

## <a name="monitor-the-job"></a>작업 모니터링
**모니터링** 영역에는 실행 중인 작업에 대한 통계가 들어 있습니다. 동일한 지역에서 저장소 계정을 사용하는 데 처음으로 구성이 필요합니다(이 문서의 나머지 부분과 같은 이름 toll).   

![모니터링 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

작업 대시보드에서 **설정** 영역에서도 **작업 로그**에 액세스할 수 있습니다.

## <a name="clean-up-the-tollapp-resources"></a>TollApp 리소스 정리
1. Azure Portal에서 Stream Analytics 작업을 중지합니다.

2. TollApp 템플릿에 관련된 8개의 리소스를 포함하는 리소스 그룹을 찾습니다.

3. **리소스 그룹 삭제**를 선택합니다. 삭제를 확인할 리소스 그룹의 이름을 입력합니다.

## <a name="conclusion"></a>결론
이 솔루션에서는 Azure Stream Analytics 서비스에 대해 소개했습니다. Stream Analytics 작업에 대해 입력 및 출력을 구성하는 방법을 보여 줍니다. 이 솔루션에서는 지금까지 요금 데이터 시나리오를 통해 동작 중인 데이터 공간에서 발생하는 일반적인 문제 형식과 Azure Stream Analytics에서 간단한 SQL 유사 쿼리를 사용하여 이러한 문제를 해결하는 방법을 설명했습니다. 또한 임시 데이터 작업을 위한 SQL 확장 구성을 설명했습니다. 데이터 스트림을 조인하는 방법 및 정적 참조 데이터를 사용하여 데이터 스트림을 보강하는 방법과 처리량을 높일 수 있도록 쿼리를 확장하는 방법을 설명했습니다.

이 솔루션이 개요에 해당하는 내용을 잘 소개하고는 있지만 완전하지는 않습니다. [일반적인 Stream Analytics 사용 패턴에 대한 쿼리 예제](stream-analytics-stream-analytics-query-patterns.md)에서 SQL 언어를 사용하는 추가 쿼리 패턴을 찾을 수 있습니다.
