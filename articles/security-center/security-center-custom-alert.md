---
title: Azure Security Center의 사용자 지정 경고 규칙 | Microsoft Docs
description: 이 문서를 통해 Azure Security Center에서 사용자 지정 경고 규칙을 만들 수 있습니다.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: e43d925317e32d2fcbdeb75eff71de0cc5a91378
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Azure Security Center의 사용자 지정 경고 규칙(미리 보기)
이 문서를 통해 Azure Security Center에서 사용자 지정 경고 규칙을 만들 수 있습니다.

## <a name="what-are-custom-alert-rules-in-security-center"></a>Security Center에서 사용자 지정 경고 규칙이란?

Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 미리 정의된 일련의 [보안 경고](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)가 있습니다. 일부 시나리오에서는 환경의 특정 요구 사항에 맞도록 사용자 지정 알림을 만드는 것이 좋습니다. 

Security Center에서 사용자 지정 경고 규칙을 사용하면 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다. 쿼리를 만들 수 있습니다. 또한 이러한 쿼리의 결과는 사용자 지정 규칙에 대한 기준으로 사용될 수 있고 이 조건이 일치하면 규칙이 실행됩니다. 컴퓨터 보안 이벤트, 파트너의 보안 솔루션 로그 또는 API를 사용하여 수집된 데이터를 사용하여 사용자 지정 쿼리를 만들 수 있습니다. 

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Security Center에서 사용자 지정 경고 규칙을 만들려면 어떻게 할까요?

**Security Center** 대시보드를 열고 다음 단계를 따라 사용자 지정 경고 규칙을 만듭니다.

1.  왼쪽 창의 **감지** 아래에서 **사용자 지정 경고 규칙(미리 보기)** 을 클릭합니다.
2.  **Security Center – 사용자 지정 경고 규칙(미리 보기)** 페이지에서 **새 사용자 지정 경고 규칙**을 클릭합니다.

    ![사용자 지정 경고](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)
    
3.  다음 옵션을 포함한 사용자 지정 경고 규칙 만들기 페이지가 표시됩니다.
    
    ![생성](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  **이름** 필드에 이 사용자 지정 규칙의 이름을 입력합니다.
5.  **설명** 필드에 이 규칙의 의도를 반영하는 간단한 설명을 입력합니다.
6.  **심각도** 필드에서 필요에 맞게 심각도 수준(높음, 보통, 낮음)을 선택합니다.
7.  **구독** 필드에서 이 규칙을 적용할 수 있는 구독을 선택합니다.
8.  **작업 영역** 필드 및 **검색 쿼리** 필드에서 이 규칙으로 모니터링하려는 작업 영역 및 결과를 가져오는 데 사용하는 쿼리를 선택합니다. 쿼리 결과가 경고를 트리거합니다. 유효한 쿼리를 입력하는 경우 녹색 확인 표시가 이 필드의 오른쪽 모서리에 나타납니다.

    ![쿼리](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. **기간** 필드에서 위의 쿼리가 실행되는 시간 범위를 선택합니다. 이 필드의 아래쪽에서 검색 결과는 선택한 시간 범위에 따라 변경됩니다.

    ![기간](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. **평가** 필드에서 이 규칙을 평가하고 실행해야 하는 빈도를 선택합니다.
12. **결과 수** 필드에서 연산자를 선택합니다(초과 또는 미만).
13. **임계값** 필드에서 이전에 선택한 연산자에 대해 참조로 사용할 수 있는 숫자를 입력합니다.
14. Security Center에서 이 규칙에 다른 경고를 보내기 전에 대기할 시간을 설정하려는 경우 **경고 표시 안 함** 옵션을 사용합니다.
15. **확인**을 클릭하여 마칩니다.

새 경고 규칙 만들기를 마치면 사용자 지정 경고 규칙의 목록에 표시됩니다. 해당 규칙의 조건을 충족하면 새로운 경고가 트리거되고 **보안 경고** 대시보드에서 확인할 수 있습니다.

![경고](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

규칙을 만드는 동안 설정된 매개 변수(검색 쿼리, 임계값 등)를 이 사용자 지정 규칙의 경고에서 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center에서 사용자 지정 경고 규칙을 만드는 방법을 알아보았습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다. 
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

