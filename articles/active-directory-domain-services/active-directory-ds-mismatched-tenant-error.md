---
title: "기존 Azure AD Domain Services로 관리되는 도메인의 디렉터리 불일치 오류 해결 | Microsoft Docs"
description: "기존 Azure AD Domain Services로 관리되는 도메인의 디렉터리 불일치 문제 이해 및 해결"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: maheshu
ms.openlocfilehash: 24e11769e9b403bc00157e3f60869effa6a9633f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>기존 Azure AD Domain Services로 관리되는 도메인의 디렉터리 불일치 문제 해결
기존 Azure AD Domain Services 관리되는 도메인이 있습니다. Azure Portal로 이동하여 관리되는 도메인을 확인할 때 다음 오류 메시지가 표시됩니다.

![디렉터리 불일치 오류](.\media\getting-started\mismatched-tenant-error.png)

오류가 해결될 때까지 이 관리되는 도메인을 관리할 수 없습니다.


## <a name="whats-causing-this-error"></a>이 오류의 원인
이 오류는 관리되는 도메인과 해당 항목이 활성화된 가상 네트워크가 서로 다른 두 Azure AD 테넌트에 속할 때 발생합니다. 예를 들어 'contoso.com'이라는 관리되는 도메인이 Contoso의 Azure AD 테넌트에서 활성화되었습니다. 그러나 관리되는 도메인이 활성화된 Azure 가상 네트워크는 다른 Azure AD 테넌트인 Fabrikam에 속합니다.

새 Azure Portal(및 특히 Azure AD Domain Services 확장)은 Azure Resource Manager에서 구축됩니다. 최신 Azure Resource Manager 환경에서는 보안을 높이고 역할 기반 액세스 제어(RBAC)를 리소스에 적용하기 위해 특정 제한이 적용됩니다. Azure AD 테넌트에 Azure AD Domain Services를 활성화하는 것은 관리되는 도메인에 자격 증명 해시가 동기화되기 때문에 중요한 작업입니다. 이 작업을 위해서는 사용자가 디렉터리의 테넌트 관리자여야 합니다. 또한 관리되는 도메인을 활성화한 가상 네트워크에 대해 관리자 권한이 있어야 합니다. RBAC 검사가 일관되게 작동하려면 관리되는 도메인과 가상 네트워크가 동일한 Azure AD 테넌트에 속해야 합니다.

즉 다른 Azure AD 테넌트인 'fabrikam.com'에서 소유한 Azure 구독에 속한 가상 네트워크에서 Azure AD 테넌트 'contoso.com'에 대해 관리되는 도메인을 활성화할 수 없습니다. 

**유효한 구성**: 이 배포 시나리오에서 Contoso로 관리되는 도메인은 Contoso Azure AD 테넌트에 대해 활성화됩니다. 관리되는 도메인은 Contoso Azure AD 테넌트가 소유한 Azure 구독에 속한 가상 네트워크에 노출됩니다. 따라서 관리되는 도메인과 가상 네트워크 모두 동일한 Azure AD 테넌트에 속합니다. 이 구성은 유효하며 모두 지원됩니다.

![유효한 테넌트 구성](./media/getting-started/valid-tenant-config.png)

**테넌트 구성 불일치**: 이 배포 시나리오에서 Contoso로 관리되는 도메인은 Contoso Azure AD 테넌트에 대해 활성화됩니다. 그러나 관리되는 도메인이 Fabrikam Azure AD 테넌트에서 소유한 Azure 구독에 속한 가상 네트워크에 노출됩니다. 따라서 관리되는 도메인과 가상 네트워크는 다른 두 Azure AD 테넌트에 속합니다. 이 구성은 일치하지 않는 테넌트 구성이며 지원되지 않습니다. 가상 네트워크를 관리되는 도메인과 동일한 Azure AD 테넌트(즉 Contoso)로 이동해야 합니다. 자세한 내용은 [해결](#resolution) 섹션을 참조하세요.

![테넌트 구성 불일치](./media/getting-started/mismatched-tenant-config.png)

따라서 관리되는 도메인과 해당 도메인이 활성화된 가상 네트워크가 서로 다른 두 Azure AD 테넌트에 속한 경우 이 오류가 표시됩니다.

리소스 관리자 환경에서 다음과 같은 규칙이 적용됩니다.
- Azure AD 디렉터리에 여러 Azure 구독이 있을 수 있습니다.
- 한 Azure 구독에는 가상 네트워크와 같은 여러 리소스가 있을 수 있습니다.
- 단일 Azure AD Domain Services로 관리되는 도메인은 Azure AD 디렉터리에 대해 활성화됩니다.
- Azure AD Domain Services로 관리되는 도메인은 동일한 Azure AD 테넌트 안의 Azure 구독 중 하나에 속한 가상 네트워크에서 활성화할 수 있습니다.


## <a name="resolution"></a>해결 방법
두 가지 방법으로 디렉터리 불일치 오류를 해결할 수 있습니다. 방법은 다음과 같습니다.

- **삭제** 단추를 클릭하여 기존의 관리되는 도메인을 삭제합니다. 관리되는 도메인과 해당 도메인을 사용할 수 있는 가상 네트워크가 Azure AD 디렉터리에 속하도록 [Azure Portal](https://portal.azure.com)을 사용하여 다시 만듭니다. 삭제된 도메인에 이전에 조인한 모든 컴퓨터를 새로 만든 관리되는 도메인에 조인합니다.

- 가상 네트워크가 포함된 Azure 구독을 관리되는 도메인이 속한 Azure AD 디렉터리로 이동합니다. [Azure 구독의 소유권을 다른 계정으로 이전](../billing/billing-subscription-transfer.md) 문서의 단계를 수행합니다.


## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](active-directory-ds-getting-started.md)
* [문제 해결 가이드 - Azure AD Domain Services ](active-directory-ds-troubleshooting.md)
