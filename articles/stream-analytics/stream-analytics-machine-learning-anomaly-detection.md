---
title: Azure Stream Analytics의 변칙 검색(미리 보기)
description: 이 아티클에서는 Azure Stream Analytics 및 Azure Machine Learning을 함께 사용하여 변칙을 검색하는 방법을 설명합니다.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e7274e4507d901a209ed5832e98ca630feefda4f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics의 변칙 검색

> [!IMPORTANT]
> 이 기능은 미리 보기 상태이며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다.

**AnomalyDetection** 연산자는 이벤트 스트림에서 다양한 형식의 변칙을 검색하는 데 사용됩니다. 예를 들어 장시간에 걸쳐 사용 가능한 메모리가 천천히 감소하는 것은 메모리 누수를 암시할 수 있으며, 일정 범위에서 안정적인 상태를 유지하던 웹 서비스 요청 수가 갑자기 증가하거나 늘어날 수 있습니다.  

AnomalyDetection 연산자는 세 가지 형식의 변칙을 검색합니다. 

* **양방향 수준 변화**: 값 수준의 지속적인 증가 또는 감소, 상향 및 하향. 이 값은 순간 또는 단기 변화인 스파이크(spike) 및 딥(dip)과는 다릅니다.  

* **느린 긍정 추세**: 시간 경과에 따른 추세의 느린 증가.  

* **느린 부정 추세**: 시간 경과에 따른 추세의 느린 감소.  

AnomalyDetection 연산자를 사용할 때는 **Limit Duration** 절을 지정해야 합니다. 이 절은 변칙을 검색할 때 고려해야 하는 시간 간격(현재 이벤트에서 검색 기록까지의 간격)을 지정합니다. 이 연산자는 선택적으로  **When**  절을 사용하여 특정 속성이나 조건과 일치하는 이벤트로만 제한될 수 있습니다. 이 연산자는 선택적으로  **Partition by**  절에 지정된 키를 기반으로 이벤트 그룹을 별도로 처리할 수도 있습니다. 학습 및 예측은 각 파티션에 대해 독립적으로 발생합니다. 

## <a name="syntax-for-anomalydetection-operator"></a>AnomalyDetection 연산자에 대한 구문

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**사용 예**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>인수

* **scalar_expression** -변칙 검색을 수행하는 스칼라 식입니다. 이 매개 변수에 허용되는 값에는 단일(스칼라) 값을 반환하는 Float 또는 Bigint 데이터 형식이 포함됩니다. 와일드 카드 식 \* **는** 허용되지 않습니다. 스칼라 식은 다른 분석 함수 또는 외부 함수를 포함할 수 없습니다. 

* **partition_by_clause** - `PARTITION BY <partition key>` 절은 학습 및 훈련을 별도 파티션으로 나눕니다. 즉, `<partition key>` 값별로 별도 모델을 사용하고, 해당 값을 갖는 이벤트만 해당 모델의 학습 및 훈련에 사용합니다. 예를 들어 다음 쿼리는 판독을 학습하고 동일한 센서의 다른 판독에 대해서만 점수를 매깁니다.

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration clause** `DURATION(<unit>, <length>)` - 변칙을 검색할 때 고려해야 하는 시간 간격(현재 이벤트에서 검색 기록까지의 간격)을 지정합니다. 지원되는 단위와 해당 약어에 대한 자세한 설명은 [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics)를 참조하세요. 

* **when_clause** - 변칙 검색 계산에서 고려되는 이벤트에 대한 부울 조건을 지정합니다.

### <a name="return-types"></a>반환 형식

이 AnomalyDetection 연산자는 3가지 점수 모두를 포함하는 레코드를 출력으로 반환합니다. 다양한 형식의 변칙 검색기와 연결된 속성은 다음과 같습니다.

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

레코드에서 개별 값을 추출하려면 **GetRecordPropertyValue** 함수를 사용합니다. 예: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

형식에 대한 변칙은 변칙 점수 중 하나가 임계값을 초과할 때 검색됩니다. 임계값은 임의의 부동 소수점 수> = 0일 수 있습니다. 임계값은 민감도가 높아지면 신뢰도가 떨어지고 민감도가 낮아지면 신뢰도가 높아집니다. 예를 들어 더 낮은 임계값을 사용하면 검색이 변경에 더 민감해지고 더 많은 경고를 생성하지만, 더 높은 임계값을 사용하면 검색이 변경에 덜 민감해지고 신뢰도는 높아지지만 일부 변칙은 검색하지 못합니다. 사용할 정확한 임계값은 시나리오에 따라 달라집니다. 상한은 없지만 권장되는 범위는 3.25-5입니다. 

예제에 나와 있는 값 3.25는 제안되는 시작점에 불과합니다. 고유한 데이터 집합에 대해 작업을 실행하여 값을 미세 조정하고 허용되는 임계값에 도달할 때까지 출력 값을 관찰합니다.

## <a name="anomaly-detection-algorithm"></a>변칙 검색 알고리즘

* AnomalyDetection 연산자는 이벤트에 어떤 형식의 배포도 가정하지 않는 **자율 학습** 방식을 사용합니다. 일반적으로 두 가지 모델이 지정된 시간에 병렬로 유지 관리되며, 그 중 하나는 점수 매기기에 사용되고 다른 하나는 백그라운드에서 학습됩니다. 변칙 검색 모델은 대역 외(out-of-band) 메커니즘보다는 현재 스트림의 데이터를 사용하여 학습됩니다. 학습에 사용되는 데이터의 양은 사용자가 Limit Duration 매개 변수 내에 지정한 기간 크기 d에 따라 다릅니다. 각 모델은 d에서 2d에 상당하는 이벤트를 기반으로 학습됩니다. 최상의 결과를 얻으려면 각 기간에 최소 50개의 이벤트가 있는 것이 좋습니다. 

* AnomalyDetection 연산자는 **슬라이딩 윈도우 의미 체계**를 사용하여 모델을 학습하고 이벤트 점수를 매깁니다. 즉, 각 이벤트는 변칙이 평가되어 점수가 반환됩니다. 점수는 해당 변칙의 신뢰 수준을 나타냅니다. 

* AnomalyDetection 연산자는 동일한 입력이 작업 출력 시작 시간에 관계없이 항상 동일한 점수를 산출하는 **반복성 보장**을 제공합니다. 작업 출력 시작 시간은 작업에 의해 첫 번째 출력 이벤트가 생성되는 시간을 나타냅니다. 이것은 사용자가 현재 시간, 사용자 지정 값 또는 마지막 출력 시간(작업이 이전에 출력을 생성한 경우)으로 설정합니다. 

### <a name="training-the-models"></a>모델 학습 

시간이 지나면서 모델은 서로 다른 데이터로 학습됩니다. 점수를 이해하기 위해서는 모델이 학습되는 기본 메커니즘을 이해하는 것이 좋습니다. **t<sub>0</sub>** 는 **작업 출력 시작 시간**이고 **d**는 Limit Duration 매개 변수의 **기간 크기**입니다. 시간은 `01/01/0001 00:00:00`에 시작하여 **d 크기의 홉**으로 나뉜다고 가정합니다. 다음 단계는 모델을 학습하고 이벤트 점수를 매기는 데 사용됩니다.

* 작업이 시작되면 t<sub>0</sub> – 2d(시간)부터 데이터를 읽습니다.  
* 시간이 다음 홉에 도달하면 새 모델 M1이 생성되고 학습되기 시작합니다.  
* 시간이 또 다음 홉에 도달하면 새 모델 M2가 생성되고 학습되기 시작합니다.  
* 시간이 t<sub>0</sub>에 도달하면 M1이 활성화되고 해당 점수가 출력되기 시작합니다.  
* 다음 홉에서 세 가지가 동시에 발생합니다.  

  * M1은 더 이상 필요 없고 삭제됩니다.  
  * M2는 충분히 학습되었기 때문에 점수 매기기에 사용됩니다.  
  * 새 모델 M3이 생성되고 백그라운드에서 학습되기 시작합니다.  

* 이 주기는 활성 모델이 삭제되는 모든 홉에 반복되고, 병렬 모델로 전환되며, 백그라운드에서 세 번째 모델이 학습되기 시작합니다. 

도식적으로 보면, 단계는 다음과 같습니다. 

![모델 학습](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Model** | **학습 시작 시간** | **점수 사용 시작 시간** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* 모델 M1은 오전 11:20에 학습을 시작하며 이 시간은 작업이 오전 11:13에 판독을 시작한 후 다음 번 홉입니다. 첫 번째 출력은 13분간의 데이터를 학습한 후 오전 11:33에 M1에서 생성됩니다. 

* 새 모델 M2 역시 오전 11:30에 학습을 시작하지만 해당 점수는 오전 11:40까지 사용되지 않으며 이 시간은 10분간의 데이터로 학습한 후입니다. 

* M3은 M2와 동일한 패턴을 따릅니다. 

### <a name="scoring-with-the-models"></a>모델로 점수 매기기 

Machine Learning 수준에서 변칙 검색 알고리즘은 들어오는 각각의 이벤트를 기록 기간의 이벤트와 비교하여 기묘도(strangeness) 값을 계산합니다. 기묘도(strangeness) 계산은 각 변칙 형식에 따라 다릅니다.  

기묘도(strangeness) 계산을 자세히 살펴보겠습니다(이벤트가 존재하는 일련의 기록 기간이 있다고 가정). 

1. **양방향 수준 변화:** 기록 기간을 기반으로 정상 작동 범위는 [10번째 백분위 수, 90번째 백분위 수]로 계산됩니다. 즉, 하한값은 10번째 백분위 수이고 상한값은 90번째 백분위 수입니다. 현재 이벤트에 대한 기묘도(strangeness) 값은 다음과 같이 계산됩니다.  

   - 0, event_value가 정상 작동 범위 내에 있는 경우  
   - 이벤트 값/90번째 백분위 수, 이벤트 값 > 90번째 백분위 수인 경우  
   - 10번째 백분위 수/이벤트 값, 이벤트 값 < 10번째 백분위 수인 경우  

2. **느린 긍정 추세:** 기록 기간의 이벤트 값에 대한 추세선이 계산되고, 작업은 선 내에서 긍정적인 추세를 찾습니다. 기묘도(strangeness) 값은 다음과 같이 계산됩니다.  

   - Slope, 기울기가 양수인 경우  
   - 0, 그 밖의 경우 

3. **느린 부정 추세:** 기록 기간의 이벤트 값에 대한 추세선이 계산되고, 작업은 선 내에서 부정적인 추세를 찾습니다. 기묘도(strangeness) 값은 다음과 같이 계산됩니다. 

   - Slope, 기울기가 음수인 경우  
   - 0, 그 밖의 경우  

들어오는 이벤트에 대한 기묘도(strangeness) 값이 계산되면 기묘도(strangeness) 값을 기반으로 마팅게일(martingale) 값이 계산됩니다. (마팅게일(martingale) 값이 계산되는 방식에 대한 자세한 내용은 [Machine Learning 블로그](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)를 참조하세요.) 마팅게일(martingale) 값은 변칙 점수로 재조정됩니다. 마팅게일(martingale) 값은 기묘도(strangeness) 값에 대응하여 천천히 증가하기 때문에 검색기가 산발적인 변화에 대해 강력한 상태를 유지하여 잘못된 경고를 줄일 수 있습니다. 또한 유용한 속성이 있습니다. 

확률[M<sub>t</sub> > λ와 같이 t가 존재함] < 1/λ, 여기서 M<sub>t</sub>는 순간 t의 마팅게일(martingale) 값이고 λ는 실제 값입니다. 예를 들어 M<sub>t</sub>>100일 때 경고를 실행하면 거짓 긍정의 확률은 1/100 미만입니다.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>양방향 수준 변화 검색기에 대한 지침 

양방향 수준 변화 검색기는 정전 및 복구, 혼잡 시 교통량 등과 같은 시나리오에 사용할 수 있습니다. 하지만 모델이 특정 데이터로 학습되고 나면, 새 값이 이전 상한값보다 높거나(상향 수준 변화) 이전 하한값보다 낮은(하향 수준 변화) 경우에만 또 다른 수준 변화가 비정상이 되는 방식으로 작동합니다. 따라서 모델이 비정상으로 간주되려면 학습 기간에서 새 수준의 범위(상향 또는 하향)에 있는 데이터 값을 보지 말아야 합니다. 

검색기를 사용할 때는 다음 사항을 고려해야 합니다. 

1. 시계열에서 값이 갑자기 증가하거나 감소하면 AnomalyDetection 연산자가 검색합니다. 그러나 정상으로 복귀를 검색하는 것에 더 많은 계획이 필요합니다. 시계열이 변칙 이전에 안정적인 상태였다면, 이것은 AnomalyDetection 연산자의 검색 기간을 변칙 길이의 절반 이하로 설정하여 달성할 수 있습니다. 이 시나리오는 변칙의 최소 지속 시간을 미리 예측할 수 있고 해당 시간 프레임에 모델이 충분히 학습하기에 충분한 이벤트(최소 50개의 이벤트)가 있다고 가정합니다. 

   이 내용은 아래 그림 1과 2에 상한 변화를 사용하여 표시되어 있습니다. (동일한 논리가 하한 변화에 적용됩니다.) 두 그림 모두 파형은 비정상적인 수준 변화입니다. 주황색 세로줄은 홉 경계를 나타내며 홉 크기는 AnomalyDetection 연산자에 지정된 검색 기간과 동일합니다. 녹색 줄은 학습 기간의 크기를 나타냅니다. 그림 1에서 홉 크기는 변칙이 지속되는 시간과 같습니다. 그림 2에서 홉 크기는 변칙이 지속되는 시간의 절반입니다. 모든 경우, 점수 매기기에 사용된 모델이 정상 데이터로 학습되었기 때문에 상향 변화가 검색됩니다. 하지만 양방향 수준 변화 검색기가 작동하는 방식을 기반으로, 정상으로 복귀하는 점수를 얻는 모델에 사용된 학습 기간에서 정상 값을 제외해야 합니다. 그림 1에서 점수 매기기 모델의 학습에는 정상 이벤트가 포함되기 때문에 정상으로 복귀는 검색될 수 없습니다. 하지만 그림 2에서는 학습에 비정상적인 부분만 포함되기 때문에 정상으로 복귀가 검색됩니다. 절반 미만인 경우에는 동일한 이유로 작동하지만, 초과되는 경우에는 약간의 정상적인 이벤트가 포함됩니다. 

   ![기간 크기가 변칙 길이와 동일한 AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![기간 크기가 변칙 길이의 절반과 동일한 AD](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. 변칙 길이를 예측할 수 없는 경우에는 검색기가 최대로 작동됩니다. 하지만 시간 범위를 더 좁게 선택하면 학습 데이터가 제한되고 이로 인해 정상 복귀를 검색할 확률이 증가합니다. 

3. 다음 시나리오에서는 학습 기간에 값이 동일하게 높은 변칙이 이미 포함되어 있기 때문에 긴 변칙이 검색되지 않습니다. 

   ![동일한 크기의 변칙](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>변칙을 검색할 쿼리 예제 

다음 쿼리는 변칙이 검색될 경우 경고를 출력하는 데 사용할 수 있습니다.
입력 스트림이 균일하지 않은 경우 집계 단계를 통해 균일한 시계열로 변환할 수 있습니다. 이 예제에서는 AVG를 사용하지만 구체적인 집계 형식은 사용자 시나리오에 따라 다릅니다. 또한 시계열에 집계 기간보다 큰 간격이 있으면 시계열에 변칙 검색을 트리거하는 이벤트가 없습니다(슬라이딩 윈도우 의미 체계에 따름). 결과적으로 다음 이벤트가 도착할 때 균일할 것이라는 가정이 중단됩니다. 이러한 상황에서는 시계열의 간격을 채워야 합니다. 가능한 한 가지 방법은 아래와 같이 모든 도약 기간에 마지막 이벤트를 가져오는 것입니다.

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>성능 지침

* 작업에 6개의 스트리밍 단위를 사용합니다. 
* 1초 이상 간격으로 이벤트를 전송합니다.
* AnomalyDetection 연산자를 사용하는 분할되지 않은 쿼리는 평균 약 25ms의 계산 대기 시간으로 결과를 생성할 수 있습니다.
* 분할된 쿼리에서 발생하는 대기 시간은 파티션 수에 따라 약간 달라집니다. 계산 수가 더 높아지기 때문입니다. 그러나 대기 시간은 모든 파티션에서 총 이벤트 수가 비슷할 경우 분할되지 않은 경우와 거의 같습니다.
* 실시간이 아닌 데이터를 읽는 동안 많은 양의 데이터가 빠르게 수집됩니다. 이 데이터의 처리는 현재 더 느립니다. 이러한 시나리오의 대기 시간은 기간 크기 또는 이벤트 간격 자체보다 기간 내의 데이터 요소 수에 따라 선형적으로 증가하는 것으로 확인되었습니다. 실시간 이외의 경우에서 대기 시간을 줄이기 위해서는 좀 더 작은 기간 크기를 사용하는 것이 좋습니다. 또는 현재 시간에서 작업을 시작하는 것이 좋습니다. 분할되지 않은 쿼리의 일부 대기 시간 예제: 
    - 검색 기간에 데이터 요소가 60개 있으면 3MBps의 처리량으로 10초가 지연될 수 있습니다. 
    - 600개의 데이터 요소에서는 0.4MBps의 처리량으로 약 80초가 지연될 수 있습니다.

## <a name="limitations-of-the-anomalydetection-operator"></a>AnomalyDetection 연산자의 제한 사항 

* 현재 이 연산자는 스파이크(spike) 및 딥(dip) 검색을 지원하지 않습니다. 스파이크(spike) 및 딥(dip)은 시계열 내의 즉흥적이거나 짧은 변화입니다. 

* 현재 이 연산자는 계절성 패턴을 처리하지 않습니다. 계절성 패턴은 데이터에 반복되는 패턴입니다. 예를 들어 주말에 달라지는 웹 트래픽 현상 또는 블랙 프라이데이 기간에 달라지는 쇼핑 경향과 같이 변칙은 아니지만 동작에 예상되는 패턴입니다. 

* 이 연산자는 입력 시계열이 균일할 것으로 예상합니다. 연속 또는 도약 기간에 대해 집계를 수행하여 이벤트 스트림을 균일하게 만들 수 있습니다. 이벤트 간 간격이 항상 집계 기간보다 작은 시나리오에서는 연속 기간만으로 충분히 시계열을 균일하게 만들 수 있습니다. 간격은 더 커질 수 있는 경우 도약 기간을 사용하여 마지막 값을 반복함으로써 간격을 채울 수 있습니다. 

## <a name="references"></a>참조

* [변칙 검색 - Machine Learning을 사용하여 시계열 데이터에서 변칙 검색](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine Learning 변칙 검색 API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [시계열 변칙 검색](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

