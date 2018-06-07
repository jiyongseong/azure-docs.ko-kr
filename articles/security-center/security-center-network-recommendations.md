---
title: "Azure Security Center에서 네트워크 보호 | Microsoft Docs"
description: "이 문서에서는 Azure 네트워크를 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure 보안 센터의 권장 사항에 대해 설명합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-network-in-azure-security-center"></a>Azure 보안 센터에서 네트워크 보호
Azure 보안 센터에서는 Azure 리소스의 보안 상태를 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.  이러한 권장 사항은 가상 컴퓨터(VM), 네트워킹, SQL, 응용 프로그램 등의 Azure 리소스 유형에 적용됩니다.

이 문서에서는 네트워크에 적용되는 권장 사항에 대해 설명합니다.  네트워크 권장 사항은 차세대 방화벽, 네트워크 보안 그룹, 인바운드 트래픽 규칙 구성 등에 초점을 둡니다.  아래 테이블을 참조로 사용하여 제공되는 네트워크 권장 사항을 이해하고 각 권장 사항을 적용할 경우 어떻게 되는지 이해할 수 있습니다.

## <a name="available-network-recommendations"></a>제공되는 네트워크 권장 사항
| 권장 사항 | 설명 |
| --- | --- |
| [차세대 방화벽 추가](security-center-add-next-generation-firewall.md) |보안 보호를 증가시키기 위해 Microsoft 파트너의 차세대 방화벽(NGFW)을 추가하라는 권장 사항입니다. |
| [NGFW를 통해서만 트래픽 라우팅](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |인바운드 트래픽이 NGFW를 통해 VM로 강제하도록 네트워크 보안 그룹(NSG) 규칙을 구성하는 것이 좋습니다. |
| [서브넷 또는 가상 컴퓨터에서 네트워크 보안 그룹 활성화](security-center-enable-network-security-groups.md) |서브넷 또는 VM에서 NSG를 활성화하는 것이 좋습니다. |
| [인터넷 끝점을 통한 액세스 제한](security-center-restrict-access-through-internet-facing-endpoints.md) |NSG에 대한 인바운드 트래픽 규칙을 구성하라는 권장 사항입니다. |

## <a name="see-also"></a>참고 항목
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure 보안 센터에서 가상 컴퓨터 보호](security-center-virtual-machine-recommendations.md)
* [Azure 보안 센터에서 응용 프로그램 보호](security-center-application-recommendations.md)
* [Azure 보안 센터에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
