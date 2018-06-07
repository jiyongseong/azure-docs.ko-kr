---
title: "Azure Portal에서 Azure Database for MySQL에 대한 메트릭 경고 구성"
description: "이 문서에서는 Azure Portal에서 Azure Database for MySQL의 메트릭 경고를 구성 및 액세스하는 방법을 설명합니다."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 025e7089f797b0accf8ee7d14028e5b5ab23c113
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Azure Portal을 사용하여 Azure Database for MySQL의 메트릭에 대한 경고를 설정합니다. 

이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL을 설정하는 방법을 보여 줍니다. Azure 서비스의 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

이 경고는 특정 메트릭의 값이 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 경고가 트리거됩니다. 

트리거되면 다음 작업을 수행하도록 경고를 구성할 수 있습니다.
* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 웹후크 호출

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.
* [Azure 포털](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [명령줄 인터페이스(CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal에서 메트릭에 대한 경고 규칙 만들기
1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 Azure Database for MySQL 서버를 선택합니다.

2. 다음과 같이 사이드바의 **모니터링** 섹션에서 **경고 규칙**을 선택합니다.

   ![경고 규칙 선택](./media/howto-alert-on-metric/1-alert-rules.png)

3. **메트릭 경고 추가**(+ 아이콘)를 선택합니다. 

4. 아래와 같이 **규칙 추가** 페이지가 열립니다.  필수 정보를 입력합니다.

   ![메트릭 경고 양식 추가](./media/howto-alert-on-metric/2-add-rule-form.png)

   | 설정 | 설명  |
   |---------|---------|
   | Name | 경고 규칙의 이름을 제공합니다. 이 값은 경고 알림 전자 메일로 전송됩니다. |
   | 설명 | 경고 규칙에 대한 간단한 설명을 제공합니다. 이 값은 경고 알림 전자 메일로 전송됩니다. |
   | 경고 대상: | 이러한 종류의 경고에 대한 **메트릭**을 선택합니다. |
   | 구독 | 이 필드는 Azure Database for MySQL을 호스트하는 구독으로 미리 채워져 있습니다. |
   | 리소스 그룹 | 이 필드는 Azure Database for MySQL의 리소스 그룹으로 미리 채워져 있습니다. |
   | 리소스 | 이 필드는 Azure Database for MySQL의 이름으로 미리 채워져 있습니다. |
   | 메트릭 | 경고를 발행할 메트릭을 선택합니다. 예: **저장소 비율**. |
   | 조건 | 비교할 메트릭의 조건을 선택합니다. 예: **보다 큼**. |
   | 임계값 | 메트릭의 임계값입니다. 예: 85(%) |
   | 기간 | 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 기간입니다. 예: **지난 30분 동안**. |

   이 예제를 기반으로 경고는 30분 동안 85%를 초과하는 저장소 비율을 찾습니다. 평균 저장소 비율이 30분 동안 85%를 초과하면 해당 경고가 트리거됩니다. 첫 번째 트리거가 발생한 후 평균 저장소 비율이 30분 넘게 85% 미만을 유지하면 다시 트리거됩니다.

5. 경고 규칙에 대해 원하는 알림 방법을 선택합니다. 

   경고가 발생했을 때 구독 관리자 및 공동 관리자에게 메일을 보내려면 **소유자, 참가자 및 구독자에게 메일 보내기** 옵션을 선택합니다.

   경고가 발생했을 때 다른 메일 주소에서 알림을 받으려면 해당 메일을 **추가 관리자 전자 메일** 필드에 추가합니다. 여러 전자 메일은 세미콜론(*email@contoso.com;email2@contoso.com*)으로 구분됩니다.

   필요에 따라 경고가 발생했을 때 호출하려면 **Webhook** 필드에 유효한 URI를 입력합니다.

6. **확인**을 선택하여 경고를 만듭니다.

   앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="manage-your-alerts"></a>경고 관리
경고를 만든 후 해당 경고를 선택하고 다음 작업을 수행할 수 있습니다.

* 이 경고와 관련된 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인.
* 경고 규칙을 **편집** 또는 **삭제**.
* 알림 수신을 일시적으로 중지하거나 다시 시작하려면 경로를 **사용 안 함** 또는 **사용**으로 설정.


## <a name="next-steps"></a>다음 단계
* [경고에서의 webhook 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)에 대해 자세히 알아봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
