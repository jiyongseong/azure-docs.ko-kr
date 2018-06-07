---
title: "Azure AD Domain Services - 관리되는 도메인의 상태 확인 | Microsoft Docs"
description: "Azure Portal에서 상태 페이지를 사용하여 관리되는 도메인의 상태를 확인합니다."
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: a9421ace7abf1f3d45b1f8cd810067d79faa92ec
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인의 상태 확인

## <a name="overview-of-the-health-page"></a>상태 페이지 개요
Azure Portal에서 상태 페이지를 사용하여 관리되는 도메인에서 발생하는 일에 대한 최신 정보를 파악할 수 있습니다. 이 문서에서는 상태 페이지의 요소를 안내합니다.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>관리되는 도메인의 상태를 보는 방법
1. Azure Portal에서 [Azure AD Domain Services 페이지](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)로 이동합니다.
2. 상태를 보고 싶은 도메인을 클릭합니다.
3. 왼쪽의 탐색 창에서 **상태**를 클릭합니다.

다음 그림은 샘플 상태 페이지 ![상태 페이지 예제](.\media\active-directory-domain-services-alerts\health-page.png)를 보여줍니다.

>[!NOTE]
> 관리되는 도메인의 상태는 1시간마다 평가됩니다. 관리되는 도메인을 변경한 후 업데이트된 상태를 보려면 그 다음 평가 주기까지 기다려야 합니다. 오른쪽 상단 모서리의 "마지막 평가 시간" 타임스탬프는 관리되는 도메인의 상태를 마지막으로 평가한 시간을 나타냅니다.
>

### <a name="status-of-your-managed-domain"></a>관리되는 도메인의 상태
상태 페이지의 오른쪽 위에 표시되는 상태는 관리되는 도메인의 전체적인 상태를 나타냅니다. 상태는 도메인의 모든 기존 경고를 고려합니다. Azure AD Domain Services의 개요 페이지에서도 도메인의 상태를 볼 수 있습니다.

| 상태 | 아이콘 | 설명 |
| --- | :----: | --- |
| 실행 중 | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | 관리되는 도메인이 문제 없이 실행되며 중요 또는 경고 알림이 없습니다. 이 도메인에 정보 제공 알림이 있을 수 있습니다. |
| 주의 필요(경고) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | 관리되는 도메인에 대한 중요 알림은 없지만, 해결해야 하는 하나 이상의 경고 알림이 있습니다. |
| 주의 필요(중요) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | 관리되는 도메인에 하나 이상의 중요 경고가 있습니다. 경고 및/또는 정보 제공 알림이 있을 수도 있습니다. |
| 배포 중 | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | 도메인이 배포되고 있는 중입니다. |

## <a name="monitors"></a>모니터
모니터는 Azure AD Domain Services에서 정기적으로 모니터링하는 관리되는 도메인의 한 측면입니다. 모니터를 정상 상태로 유지하는 가장 좋은 방법은 관리되는 도메인에 대한 모든 활성 경고를 해결하는 것입니다.

Azure AD Domain Services는 현재 다음을 모니터링합니다.
 - Backup
 - Azure AD와 동기화

### <a name="the-backup-monitor"></a>'백업' 모니터
관리되는 도메인이 정기적으로 백업되고 있는지 여부를 모니터링합니다. 다음 표에서는 백업 모니터의 세부 정보 열에서 예상할 수 있는 내용에 대해 설명합니다.

| 세부 정보 값 | 설명 |
| --- | --- |
|"한 번도 백업되지 않음" | 새로 생성된 관리되는 도메인에서는 이 상태가 정상입니다. 일반적으로 첫 번째 백업은 관리되는 도메인이 생성되고 24시간 후에 수행됩니다. 관리되는 도메인이 새로 생성되지 않았거나 이 상태가 비정상적으로 오래 사용되는 경우[지원에 문의](active-directory-ds-contact-us.md)하세요. |
| 1-14일 전에 마지막 백업 수행 | 일반적으로 이 값은 백업 모니터에서 예상되는 값입니다. |
| 마지막 백업이 수행된 후 14일이 넘었습니다. | 마지막 백업이 수행된 후 2주가 넘었다면 비정상입니다. 활성 중요 경고 때문에 관리되는 도메인을 주기적으로 백업하지 못하는 것일 수 있습니다. 먼저, 관리되는 도메인에 대한 활성 경고를 해결하고, 그래도 문제가 계속되면 [지원에 문의](active-directory-ds-contact-us.md)합니다. |


### <a name="the-synchronization-with-azure-ad-monitor"></a>'Azure AD와 동기화' 모니터
Microsoft는 관리되는 도메인이 Azure Active Directory와 동기화되는 빈도를 모니터링합니다. 개체(사용자 및 그룹) 수 및 마지막 동기화 이후에 변경된 Azure AD 디렉터리 변경 항목 수는 동기화에 걸리는 시간에 영향을 줄 수 있습니다. 관리되는 도메인이 마지막으로 동기화된 지 3일이 넘은 경우 [지원에 문의](active-directory-ds-contact-us.md)하세요.

## <a name="alerts"></a>Alerts
Azure AD Domain Services가 실행되려면 해결해야 하는 관리되는 도메인의 문제에 대한 경고가 생성됩니다. 각 경고는 문제에 대해 설명하고 문제 해결을 위한 단계를 간략하게 설명하는 해결 방법 URL을 제공합니다. 모든 경고 및 해결 방법을 보려면 [경고 문제 해결](active-directory-ds-troubleshoot-alerts.md) 문서를 참조하세요.

### <a name="alert-severity"></a>경고 심각도
경고는 중요, 경고 및 정보의 세 가지 심각도 수준으로 분류됩니다.

 * **중요한 알림**은 관리되는 도메인에 심각한 영향을 주는 문제입니다. Microsoft가 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 없으므로 이러한 알림을 즉시 해결해야 합니다. 
 * **경고 알림**은 나중에 관리되는 도메인에 영향을 줄 수 있는 문제를 알려줍니다. 이러한 알림은 관리되는 도메인을 보호하는 권장 사항을 제공합니다.
 * **정보 제공 알림**은 도메인에 부정적인 영향을 미치지 않는 알림입니다. 정보 제공 알림은 도메인 및 Azure AD Domain Services에서 발생하는 일을 지속적으로 파악할 수 있도록 설계되었습니다.

## <a name="next-steps"></a>다음 단계
- [관리되는 도메인에 대한 경고 해결](active-directory-ds-troubleshoot-alerts.md)
- [Azure AD Domain Services에 대해 자세히 알아보기](active-directory-ds-overview.md)
- [제품팀에 문의](active-directory-ds-contact-us.md)
