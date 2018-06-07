---
title: "Azure Security Center에서 응용 프로그램 보호 | Microsoft Docs"
description: "이 문서에서는 Azure 응용 프로그램을 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure 보안 센터의 권장 사항에 대해 설명합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Azure 보안 센터에서 응용 프로그램 보호
Azure 보안 센터에서는 Azure 리소스의 보안 상태를 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.  이러한 권장 사항은 가상 컴퓨터(VM), 네트워킹, SQL, 응용 프로그램 등의 Azure 리소스 유형에 적용됩니다.

이 문서에서는 응용 프로그램에 적용되는 권장 사항에 대해 설명합니다.  응용 프로그램 권장 사항은 웹 응용 프로그램 방화벽의 배포에 초점을 둡니다.  아래 테이블을 참조로 사용하여 제공되는 응용 프로그램 권장 사항을 이해하고 각 권장 사항을 적용할 경우 어떻게 되는지 이해할 수 있습니다.

## <a name="available-application-recommendations"></a>제공되는 응용 프로그램 권장 사항
| 권장 사항 | 설명 |
| --- | --- |
| [웹 응용 프로그램 방화벽 추가](security-center-add-web-application-firewall.md) |웹 끝점에 WAF(웹 응용 프로그램 방화벽)를 배포하는 것이 좋습니다. 공개 인바운드 웹 포트(80,443)으로 연결된 네트워크 보안 그룹에 있는 모든 공용 연결 IP(인스턴스 수준 IP 또는 부하 분산된 IP)에 대해 WAF 권장 사항이 표시됩니다.</br></br>Security Center에서는 가상 컴퓨터와 App Service 환경에 있는 웹 응용 프로그램을 대상으로 한 공격을 방어할 수 있도록 WAF를 프로비전할 것을 권장합니다. ASE(App Service 환경)는 Azure App Service의 [프리미엄](https://azure.microsoft.com/pricing/details/app-service/) 서비스 계획 옵션으로, Azure App Service 앱의 안전한 실행을 위해 완전히 격리된 전용 환경을 제공합니다. ASE에 대한 자세한 내용을 보려면 [App Service Environment 설명서](../app-service/environment/intro.md)를 참조하세요.</br></br>기존 WAF 배포에 이러한 응용 프로그램을 추가하여 보안 센터에서 여러 웹 응용 프로그램을 보호할 수 있습니다. |
| [응용 프로그램 보호 완료](security-center-add-web-application-firewall.md#finalize-application-protection) |WAF 구성을 완료하려면 트래픽 경로가 WAF 어플라이언스로 전환되어야 합니다. 이 권장 사항을 따르면 필요한 설정 변경이 완료됩니다. |

## <a name="see-also"></a>참고 항목
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure 보안 센터에서 가상 컴퓨터 보호](security-center-virtual-machine-recommendations.md)
* [Azure 보안 센터에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure 보안 센터에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
