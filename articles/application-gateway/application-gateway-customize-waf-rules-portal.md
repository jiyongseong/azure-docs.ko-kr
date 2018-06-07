---
title: Azure Application Gateway에서 웹 응용 프로그램 방화벽 규칙 사용자 지정 - Azure Portal | Microsoft Docs
description: 이 문서에서는 Azure Portal을 통해 Application Gateway에서 웹 응용 프로그램 방화벽 규칙을 사용자 지정하는 방법을 설명합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: victorh
ms.openlocfilehash: ae61e3a8308e95c16ccde71de37fb10666ef0df9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Azure Portal을 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway WAF(웹 응용 프로그램 방화벽)는 웹 응용 프로그램을 보호합니다. 이러한 보호 기능은 OWASP(Open Web Application Security Project) CRS(코어 규칙 집합)을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다. 이러한 이유로 Application Gateway는 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙 목록](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다. 

![WAF 사용][fig1]

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

**규칙 그룹 및 규칙을 보려면**
   1. Application Gateway로 이동하여 **웹 응용 프로그램 방화벽**을 선택합니다.  
   2. **고급 규칙 구성**을 선택합니다.  
   이 보기는 선택된 규칙 집합과 함께 제공되는 모든 규칙 그룹의 페이지에 하나의 테이블을 표시합니다. 모든 규칙의 확인란이 선택되어 있습니다.

![사용하지 않도록 설정된 규칙 구성][1]

## <a name="search-for-rules-to-disable"></a>사용하지 않을 규칙 검색

**웹 응용 프로그램 방화벽 설정** 블레이드는 텍스트 검색으로 규칙을 필터링하는 기능을 제공합니다. 검색 텍스트가 포함된 규칙 그룹 및 규칙만 결과에 표시됩니다.

![규칙 검색][2]

## <a name="disable-rule-groups-and-rules"></a>규칙 그룹 및 규칙을 사용하지 않도록 설정

규칙을 사용하지 않도록 설정할 때 규칙 그룹 전체를 사용하지 않도록 설정하거나 하나 이상의 규칙 그룹에서 특정 규칙만 사용하지 않도록 설정할 수 있습니다. 

**규칙 그룹 또는 특정 규칙을 사용하지 않도록 설정하려면**

   1. 사용하지 않도록 설정할 규칙 또는 규칙 그룹을 검색합니다.
   2. 사용하지 않도록 설정하려는 규칙에 대한 확인란을 선택 취소합니다. 
   2. **저장**을 선택합니다. 

![변경 내용 저장][3]

## <a name="next-steps"></a>다음 단계

비활성화된 규칙을 구성한 후 WAF 로그를 보는 방법에 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
