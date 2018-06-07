---
title: Azure Monitor에서 동적 임계값을 사용하여 경고 만들기 | Microsoft Docs
description: 기계 학습 기반 동적 임계값을 사용한 경고 만들기
author: antonfrMSFT
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: antonfr;mbullwin
ms.openlocfilehash: 45d2f39ff7fb2e5a22cda74cd0c794db28e351f1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33943300"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Azure Monitor의 동적 임계값을 사용한 경고(제한된 공개 미리 보기)

동적 임계값을 사용한 경고는 Azure Monitor에 포함된 Azure 메트릭 경고의 향상된 기능이며, 고급 ML(Machine Learning) 기능을 활용하여 메트릭의 기존 동작을 학습하고 기준선을 자동으로 계산하여 경고 임계값으로 사용합니다.

동적 임계값을 사용하면 다음과 같은 이점이 있습니다.

- 모니터가 메트릭의 과거 성능을 자동으로 학습하고 ML 알고리즘을 적용하여 경고 임계값을 결정하기 때문에 미리 정의된 엄격한 경계 설정과 관련된 번거로움을 덜어줍니다.
- 계절성 동작을 파악하여 예상되는 계절성 동작의 편차에 대해서만 경고할 수 있습니다. 정기적으로 주말에 서비스가 유휴 상태였다가 월요일마다 급상승하는 경우 동적 임계값을 사용한 메트릭 경고는 트리거되지 않습니다. 현재 지원: 매시간, 매일 및 매주 계절성.
- 메트릭 성능을 지속적으로 학습하고 메트릭 변경에 적응합니다.

동적 임계값 기반 경고는 이 [문서](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for)에 나열된 Azure Monitor 기반 메트릭 원본에서 사용할 수 있습니다.

## <a name="sign-up-to-access-the-preview"></a>미리 보기에 액세스하려면 등록하세요.

이 기능을 테스트해보려면 [미리 보기에 등록](http://aka.ms/DynamicThresholdMetricAlerts)하세요. 언제나 여러분의 의견을 환영합니다. [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)으로 의견을 보내주세요.

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>동적 임계값을 사용한 경고를 구성하는 방법

동적 임계값을 사용한 경고는 Azure Monitor의 경고를 통해 구성할 수 있습니다.

![경고 미리 보기](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>동적 임계값을 사용한 경고 규칙 만들기

1. Monitor 아래 경고 창에서 **새 경고 규칙** 단추를 선택하여 Azure에서 새 경고를 만듭니다.

   ![새 경고 규칙](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. 규칙 만들기 섹션은 _경고 조건 정의_, _경고 세부 정보 정의_ 및 _작업 그룹 정의_로 이루어진 세 부분으로 표시됩니다. 우선 _경고 조건 정의_ 섹션에서 **대상 선택** 링크를 사용하여 리소스를 선택하고 대상을 지정합니다. 적절한 리소스를 선택했으면 완료 단추를 클릭합니다.

   ![대상 선택](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. 다음으로 **조건 추가** 단추를 사용하여 리소스에 사용할 수 있는 신호 옵션 목록을 보려면 신호 목록에서 적절한 **메트릭** 옵션을 선택합니다. (예: CPU 사용률)

   ![조건 추가](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. 신호 논리 구성 화면의 경로 논리 섹션에 조건을 동적 유형으로 전환할 수 있는 옵션이 있습니다. 이 옵션은 메트릭(파란색 선) 옆에 동적 임계값(빨간색 선)을 자동으로 생성합니다.

   ![동적](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. 차트에 표시되는 임계값은 지난 7일간의 데이터를 기반으로 계산됩니다. 경고가 만들어지면 동적 임계값은 사용 가능한 과거 데이터를 추가로 수집하고 새 데이터를 지속적으로 학습하여 임계값을 보다 정확하게 만들어 갑니다.

6. 추가 경고 논리 설정:
   - 조건 - 다음 세 가지 조건 중 하나에서 경고가 트리거되도록 선택할 수 있습니다.
       - 상한 임계값보다 크거나 하한 임계값보다 낮습니다(기본값).
       - 상한 임계값보다 큽니다.
       - 하한 임계값보다 낮습니다.
   - 시간 집계: 평균(기본값), 합계, 최소 최대.
   - 경고 민감도:
       - 높음 - 가장 작은 편차에서 경고가 트리거되므로 경고가 더 많이 발생합니다.
       - 중간 - 높음보다 덜 민감하고 높은 민감도보다 경보가 적게 발생합니다(기본값).
       - 낮음 - 가장 민감하지 않은 임계값입니다.

    ![경고 논리 설정](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. 평가 기준:
    -  기간, 경고는 **기간**을 선택하여 지정된 조건을 찾아야 합니다.

    ![평가 기준](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > 지원되는 기간 값: 5분, 10분, 30분 및 1시간.

   일시적인 스파이크로 인해 발생하는 경고 소음을 줄이려면 "경고를 트리거할 위반 횟수" 설정을 사용하는 것이 좋습니다. 이 기능을 사용하면 임계 값이 연속해서 X번 위반되었거나 지난 Z기간 중 Y번 위반된 경우에만 경고를 받을 수 있습니다. 예: 

    문제가 주어진 5분간 3번 연속해서 15분간 지속되는 경우 경고를 트리거하려면 다음 설정을 사용합니다.

   ![평가 기준](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    기간이 5분일 때 지난 30분 중 15분 동안 동적 임계값 위반이 발생한 경우 경고를 트리거하려면 다음 설정을 사용합니다.

   ![평가 기준](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. 현재 사용자는 동적 임계값을 사용한 경고 조건을 단일 조건으로 사용할 수 있습니다.

   ![규칙 만들기](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>질문과 대답

- 질문: 시간이 경과하면서 메트릭이 천천히 변경되는 경우 동적 임계값을 사용한 경고가 트리거되나요?

- 답변: 그렇지 않을 가능성이 있습니다. 동적 임계값은 느리게 진화하는 문제보다는 상당한 편차를 검색하는 데 유용합니다.

- 질문: API를 통해 동적 임계값을 구성할 수 있나요?

- 답변: 해당 기능은 준비되고 있습니다.
