---
title: "Azure Security Center에서 OS 버전 업데이트 | Microsoft Docs"
description: "이 문서에서는 Azure 보안 센터 권장 사항 **OS 버전 업데이트**를 구현하는 방법을 보여 줍니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
ms.openlocfilehash: ce0d178914907750e5da59f223a4b1e04b9bb6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="update-os-version-in-azure-security-center"></a>Azure 보안 센터에서 OS 버전 업데이트
클라우드 서비스의 가상 컴퓨터(VM)의 경우 Azure 보안 센터는 사용 가능한 최신 버전이 있는 경우 운영 체제(OS) 업데이트를 권장합니다.  프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  단계별 가이드는 아닙니다.
> 
> 

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항** 블레이드에서 **OS 버전 업데이트**를 선택합니다.
   ![OS 버전 업데이트][1]
2. 이렇게 하면 **OS 버전 업데이트**블레이드가 열립니다. 이 블레이드의 단계를 따라 OS 버전을 업데이트합니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 "OS 버전 업데이트"를 구현하는 방법을 보여 주었습니다. 클라우드 서비스 및 클라우드 서비스에 대한 OS 버전 업데이트에 대한 자세한 내용은 다음을 참조하세요.

* [클라우드 서비스 개요](../cloud-services/cloud-services-choose-me.md)
* [클라우드 서비스를 업데이트하는 방법](../cloud-services/cloud-services-update-azure-service.md)
* [클라우드 서비스를 구성하는 방법](../cloud-services/cloud-services-how-to-configure-portal.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) -- 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) -- 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) -- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
