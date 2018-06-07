---
title: Azure Security Center에서 ID 및 액세스 모니터링 | Microsoft Docs
description: Azure Security Center의 ID 및 액세스 기능을 사용하여 사용자의 액세스 작업과 ID 관련 문제를 모니터링하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 5ee263ef8fb0f20049215eda53e0d58a45342b7e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Azure Security Center에서 ID 및 액세스 모니터링
이 문서를 통해 Azure Security Center를 사용하여 사용자의 ID를 모니터링하고 작업에 액세스할 수 있습니다.

## <a name="why-monitor-identity-and-access"></a>ID 및 액세스 모니터링하는 이유는 무엇입니까?
ID는 엔터프라이즈의 제어 평면으로서 ID 보호가 최우선 순위가 되어야 합니다. 이전에는 조직 주위에 경계가 있었으며 해당 경계는 기본 방어 경계 중 하나였습니다. 오늘날에는 추가 데이터와 앱을 클라우드로 이동하면 ID는 새 경계가 됩니다.

ID 작업을 모니터링하여 문제가 발생하기 전에 사전 대응 조치를 취하거나 사후 대응을 통해 공격 시도를 중단할 수 있습니다. ID 및 액세스 대시보드에서는 다음을 포함한 ID 상태 개요를 제공합니다.

* 로그온에 실패한 시도 횟수 
* 해당 시도 중에 사용된 사용자 계정
* 잠긴 계정
* 암호가 변경되거나 다시 설정된 계정 
* 현재 로그인한 계정 수

## <a name="monitor-identity-and-access-activities"></a>ID 및 액세스 작업 모니터링
ID 및 액세스와 관련된 현재 작업을 보려면 **ID 및 액세스** 대시보드에 액세스해야 합니다.

1. **Security Center** 대시보드를 엽니다.

2. 왼쪽 창의 **방지** 아래에서 **ID 및 액세스**를 선택합니다. 여러 작업 영역에 있는 경우 작업 영역 선택기가 나타납니다.

    ![작업 영역 선택 영역](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 맨 오른쪽의 열이 **업그레이드 계획**을 표시하면 이 작업 영역은 체험용 구독을 사용하는 중입니다. 표준 구독으로 업그레이드하여 이 기능을 사용합니다. 맨 오른쪽의 열이 **업데이트 필요**를 표시하면 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)을 업데이트하여 이 기능을 사용합니다. 가격 책정 계획에 대한 자세한 내용은 Security Center 가격 책정을 참조하세요. 
    > 
3. 둘 이상의 작업 영역을 조사할 경우 **실패한 로그인** 열에 따라 조사의 우선 순위를 지정할 수 있습니다. 이 작업 영역에서 현재 실패한 로그온 시도 획수를 보여줍니다. 사용하려는 작업 영역을 선택하면 **ID 및 액세스** 대시보드가 나타납니다.

    ![ID 및 액세스](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. 이 대시보드에 표시되는 정보를 통해 잠재적으로 의심스러운 활동을 식별하도록 도울 수 있습니다. 대시보드는 세 가지 주요 영역으로 구성되어 있습니다.

    a. **ID 상태** 이 작업 영역에서 수행된 ID 관련 작업을 요약합니다.

    나. **실패한 로그인** 실패한 로그인 시도에 대한 주요 원인을 신속하게 식별할 수 있습니다. 로그온하려는 대부분의 시도가 실패한 상위 10개의 계정 목록이 표시됩니다.

    다. **시간에 따른 로그인** 시간에 따른 로그인 횟수를 신속하게 식별할 수 있습니다. 컴퓨터 계정 로그인 시도의 상위 목록을 보여줍니다.
    
선택한 타일에 관계 없이 표시되는 대시보드는 로그 검색 쿼리를 기반으로 합니다. 유일한 차이점은 쿼리 및 결과의 유형입니다. 여전히 컴퓨터와 같은 항목을 선택할 수 있으며 관련 데이터를 볼 수 있습니다. 

## <a name="see-also"></a>참고 항목
이 문서에서는 Security Center에서 ID 및 액세스를 모니터링하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 응답](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다. 
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. Security Center 사용에 관한 질문과 대답에 대한 답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

