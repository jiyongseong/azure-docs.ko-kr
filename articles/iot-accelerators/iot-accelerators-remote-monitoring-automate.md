---
title: 원격 모니터링 솔루션에서 장치 문제 감지 - Azure | Microsoft Docs
description: 이 자습서에서는 규칙 및 작업을 사용하여 원격 모니터링 솔루션에서 임계값 기반 장치 문제를 자동으로 감지하는 방법을 보여 줍니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f3583b27b2fb9959e65a9c66a75c1174ebf3e238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>임계값 기반 규칙을 사용하여 문제 감지

이 자습서는 원격 모니터링 솔루션에서 규칙 엔진의 기능을 보여 줍니다. 이러한 기능을 소개하기 위해 자습서에서는 Contoso IoT 응용 프로그램에서 시나리오를 사용합니다.

Contoso에는 **냉각기** 장치에서 보고된 압력이 250PSI를 초과하는 경우 중요한 경보를 생성하는 규칙이 있습니다. 운영자로서 초기 압력 스파이크를 찾아 문제가 있는 센서가 있을 수 있는 **냉각기** 장치를 식별하려고 합니다. 이러한 장치를 식별하려면 압력이 150PSI를 초과할 때 경고를 생성하는 규칙을 만듭니다.

또한 지난 5분 동안의 **냉각기** 장치의 평균 습도가 80%보다 크고 지난 5분 동안의 **냉각기** 장치의 온도가 화씨 75보다 클 때 트리거되어야 하는 중요한 경고를 알림 받았습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 솔루션에서 규칙 보기
> * 새 규칙 만들기
> * 여러 조건을 사용하여 새 규칙 만들기
> * 기존 규칙 편집
> * 규칙 삭제

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 Azure 구독에서 원격 모니터링 솔루션의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션을 아직 배포하지 않은 경우 [원격 모니터링 솔루션 가속기 배포](iot-accelerators-remote-monitoring-deploy.md) 자습서를 완료해야 합니다.

## <a name="view-the-rules-in-your-solution"></a>솔루션에서 규칙 보기

솔루션에서 **규칙** 페이지는 현재 모든 규칙의 목록을 표시합니다.

![규칙 및 작업 페이지](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2.png)

**냉각기** 장치에 적용되는 규칙만 보려면 필터를 적용합니다.

![규칙의 목록 필터링](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2.png)

규칙에 대한 자세한 내용을 보고 목록에서 선택하면 편집할 수 있습니다.

![규칙 세부 정보 보기](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2.png)

하나 이상의 규칙을 비활성화, 활성화 또는 삭제하려면 목록에서 여러 개의 규칙을 선택합니다.

![여러 규칙 선택](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>새 규칙 만들기

**냉각기** 장치의 압력이 150PSI를 초과할 때 경고를 생성하는 새 규칙을 추가하려면 **새 규칙**을 선택합니다.

![규칙 만들기](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2.png)

다음 값을 사용하여 규칙을 만듭니다.

| 설정          | 값                                 |
| ---------------- | ------------------------------------- |
| 규칙 이름        | 냉각기 경고                       |
| 설명      | 냉각기 압력이 150PSI를 초과함 |
| 장치 그룹     | **냉각기** 장치 그룹             |
| 계산      | 인스턴트                               |
| 조건 1 필드| pressure                              |
| 조건 1 연산자 | 다음보다 큼                      |
| 조건 1 값    | 150                               |
| 심각도 수준  | Warning                               |

새 규칙을 저장하려면 **적용**을 선택합니다.

규칙이 **규칙** 페이지 또는 **대시보드** 페이지에서 트리거되는 경우 볼 수 있습니다.

## <a name="create-a-new-rule-with-multiple-conditions"></a>여러 조건을 사용하여 새 규칙 만들기

지난 5분 동안의 **냉각기** 장치의 평균 습도가 80%보다 크고 지난 5분 동안의 **냉각기** 장치의 온도가 화씨 75보다 클 때 중요한 경고를 생성하는 여러 조건을 사용하여 새 규칙을 만들려면 **새 규칙**을 선택합니다.

![여러 규칙 만들기](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

다음 값을 사용하여 규칙을 만듭니다.

| 설정          | 값                                 |
| ---------------- | ------------------------------------- |
| 규칙 이름        | 냉각기 습도 및 온도 위험    |
| 설명      | 습도 및 온도가 중요함 |
| 장치 그룹     | **냉각기** 장치 그룹             |
| 계산      | 평균                               |
| 기간      | 5                                     |
| 조건 1 필드| humidity                              |
| 조건 1 연산자 | 다음보다 큼                      |
| 조건 1 값    | 80                               |
| 심각도 수준  | 중요                              |

두 번째 조건을 추가하려면 "+ 조건 추가"를 클릭합니다.

![조건 2 만들기](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

새 조건에 대해 다음 값을 사용합니다.

| 설정          | 값                                 |
| ---------------- | ------------------------------------- |
| 조건 2 필드| 온도                           |
| 조건 2 연산자 | 다음보다 큼                      |
| 조건 2 값    | 75                                |

새 규칙을 저장하려면 **적용**을 선택합니다.

규칙이 **규칙** 페이지 또는 **대시보드** 페이지에서 트리거되는 경우 볼 수 있습니다.

## <a name="edit-an-existing-rule"></a>기존 규칙 편집

기존 규칙을 변경하려면 규칙의 목록에서 선택합니다.

![규칙 편집](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>다음 단계

이 자습서는 다음 방법을 보여 줬습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 솔루션에서 규칙 보기
> * 새 규칙 만들기
> * 기존 규칙 편집
> * 규칙 삭제

이제 임계값 기반 규칙을 사용하여 문제를 감지하는 방법을 배웠으므로 제안된 다음 단계는 다음 방법을 배우기 위한 것입니다.

* [장치 관리 및 구성](iot-accelerators-remote-monitoring-manage.md)
* [장치 문제 해결 및 수정](iot-accelerators-remote-monitoring-maintain.md)
* [시뮬레이트된 장치로 솔루션 테스트](iot-accelerators-remote-monitoring-test.md)

<!-- Next tutorials in the sequence -->