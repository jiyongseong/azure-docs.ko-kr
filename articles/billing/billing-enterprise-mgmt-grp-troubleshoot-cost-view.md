---
title: 기업 비용 보기 문제 해결 - Azure | Microsoft Docs
description: Azure Portal 내에서 조직의 비용 보기에 발생할 수 있는 문제를 해결하는 방법에 대해 알아봅니다.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: rithorn
ms.openlocfilehash: 193e4691d2e10dd1c5ad16c26ad25534e3156745
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-enterprise-cost-views"></a>기업 비용 보기 문제 해결 

기업 등록계약 내에 등록된 사용자에게 비용이 표시되지 않도록 하는 여러 설정이 있습니다.  이러한 설정은 등록 관리자가 관리하거나 Microsoft에서 직접 등록을 구매하지 않은 경우 파트너가 관리합니다.  이 문서를 통해 이러한 설정이 무엇이고 등록에 미치는 영향을 이해할 수 있습니다. 이러한 설정은 [Azure RBAC 역할](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)과는 별개입니다. 


## <a name="enabling-access-to-costs"></a>비용에 대한 액세스 사용

비용 정보를 검색할 때 권한 없음 또는 *"등록에서 비용 보기를 사용할 수 없습니다."* 라는 메시지가 표시되나요? ![권한 없음](media/billing-enterprise-mgmt-groups/unauthorized.png)

다음 이유 중 하나 때문일 수 있습니다.

1. 기업 파트너를 통해 Azure를 구매했고 파트너가 아직 가격 정책을 릴리스하지 않았습니다. 가격 책정을 릴리스하려면 [Enterprise Portal](https://ea.azure.com) 내의 설정을 업데이트하도록 파트너에게 문의합니다.
2. 또는 EA Direct 고객인 경우 몇 가지 가능성이 있습니다.
    * 사용자는 계정 소유자이며 등록 관리자가 "AO 보기 요금" 설정을 비활성화했습니다.  
    * 사용자는 부서 관리자이며 등록 관리자가 "DA 보기 요금" 설정을 비활성화했습니다.
    * 등록 관리자에게 액세스 권한을 문의하세요. 등록 관리자는 [Enterprise Portal](https://ea.azure.com/manage/enrollment)을 방문하여 다음과 같이 설정을 업데이트할 수 있습니다.

![Enterprise Portal 설정](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>자산을 사용할 수 없으신가요? 
구독 또는 관리 그룹에 액세스하려고 할 때 "이 자산을 사용할 수 없습니다."라는 오류 메시지가 표시되는 경우에는 이 항목을 볼 수 있는 올바른 역할이 없는 것입니다.  

![자산을 찾을 수 없음](media/billing-enterprise-mgmt-groups/asset-not-found.png)

액세스 권한을 받으려면 구독 또는 관리 그룹의 관리자에게 문의합니다.  
* 구독의 경우 필요한 역할에 대한 자세한 내용은 [Azure RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 문서를 참조하세요.
