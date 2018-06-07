---
title: "Azure 스택에 대 한 연결 된 azure 배포 결정 통합 시스템 | Microsoft Docs"
description: "배포 계획 다중 노드 배포 Azure 스택 Azure 연결에 대 한 결정을 확인 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: def9d5381144026b5ad0e8a076edd3c0692a08f4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure 스택에 대 한 결정을 계획 하는 azure 연결 된 배포 시스템을 통합
결정 한 다음 [Azure 스택 하이브리드 클라우드 환경으로 통합 됩니다는 어떻게](azure-stack-connection-models.md), Azure 스택 배포 결정 사항 마무리 다음 수 있습니다.

Azure에 연결 하는 배포 Azure 스택 의미 id 저장소에 대 한 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) 사용할 수 있습니다. 청구 모델 중에서 선택할 수 있습니다: 사용량 기준 과금으로-있습니다-사용 또는 용량 기반 합니다. 연결 된 배포는 기본 옵션 때문에 고객 특히 Azure 및 Azure 스택을 포함 하는 하이브리드 클라우드 시나리오에 대 한 Azure 스택에서 가장 많은 값을 가져올 수 있습니다. 

## <a name="choose-an-identity-store"></a>Id 저장소를 선택 합니다.
연결 된 배포를 Azure AD 또는 AD FS에 대 한 id 저장소 간에 선택할 수 있습니다. 연결이 끊긴된 배포 인터넷 연결 되지 않은 수만 AD FS를 사용 합니다.

Identity 저장소 선택한 테 넌 트 가상 컴퓨터 (Vm)는 관련이 없습니다. 테 넌 트 Vm 구성 방식에 따라에 연결 하려는 identity 상점을 선택할 수 있습니다: Azure AD, Windows Server Active Directory 도메인에 가입 된, 작업 그룹, 등입니다. 이 작업은 Azure 스택 identity provider 의사 결정 관련이 없습니다. 

예를 들어 테 넌 트 IaaS Azure 스택 맨 위에 Vm을 배포 하 고 회사 Active Directory 도메인에 가입 하 고 여기에서 계정을 사용 하도록 할 경우 계속 하면이 됩니다. 여기에서 선택한 Azure AD id 저장소를 사용 하 여 해당 계정에 대 한 필요가 없습니다.

### <a name="azure-ad-identity-store"></a>Azure AD id 저장소
두 Azure AD 계정 id 저장소 용 Azure AD 사용 하는 경우: 전역 관리자 계정 및 요금 청구 계정. 이러한 계정은 동일한 계정 또는 서로 다른 계정 수 있습니다. 하지만 동일한 사용자 계정을 사용 하 여 간단 하 고 제한 된 수의 Azure 계정에 있는 경우, 두 개의 계정을 사용 하 여 비즈니스 요구를 제시할 수 있습니다.

1. **전역 관리자 계정을** (연결 된 배포에만 필요). 이것이 Azure Active Directory에 응용 프로그램 및 스택 Azure 인프라 서비스에 대 한 서비스 주체를 만드는 데 사용 되는 Azure 계정입니다. 이 계정에 Azure 스택 시스템에서 배포 될 디렉터리에 디렉터리 관리자 권한이 있어야 합니다. "클라우드 연산자" 사이트용으로 Azure AD에 대 한 전역 관리자 테 넌 트 및 사용 됩니다. 
    - 프로 비전 하 고 응용 프로그램 및 서비스 사용자를 Azure Active Directory 및 Graph API를 사용 해야 하는 모든 Azure 스택 서비스에 위임 합니다. 
    - 서비스 관리자 계정. (나중에 변경할 수 있습니다)이 표시 되는 기본 공급자 구독의 소유자입니다. 이 계정에 Azure 스택 관리자 포털에 로그인 할 수 및 사용 하 여 제안 및 계획을 만들, 할당량을 설정 하 고, Azure 스택의 다른 관리 기능을 수행할 수 있습니다.
2. **요금 청구 계정** (둘 다 연결 하 고 배포의 연결이 해제에 대 한 필수). Azure 스택 통합 시스템과 Azure commerce 백 엔드 간의 청구 관계를 설정 하이 Azure 계정이 사용 됩니다. 이 계정이 Azure 스택 요금에 대 한 요금이 청구 됩니다. 이 계정은 마켓플레이스 배포 및 다른 하이브리드 시나리오에도 사용 됩니다. 

### <a name="ad-fs-identity-store"></a>AD FS id 저장소
서비스 관리자 계정에 대 한 사용자 회사 Active Directory와 같은 고유한 id 저장소를 사용 하려면이 옵션을 선택 합니다.  

## <a name="choose-a-billing-model"></a>청구 모델을 선택 합니다.
하나를 선택할 수 **사용량 기준 과금으로-있습니다-사용** 또는 **용량** 청구 모델입니다. 사용량 기준 과금-으로--사용 요금 청구 모델 배포 사용량을 보고 연결을 통해 Azure에 30 일 마다 한 번 이상 있어야 합니다. 사용량 기준 과금-으로--사용 요금 청구 모델은 연결 된 배포에 사용할 수만 있습니다.  

### <a name="pay-as-you-use"></a>사용량 기준 과금-으로--사용
사용량 기준 과금-으로--사용 요금 청구 모델을 사용은 Azure 구독에 청구 됩니다. Azure 스택 서비스를 사용할 때만 지불 합니다. 사용 하도록 결정 하는 모델의 경우 Azure 구독 및 해당 구독과 연결 된 계정 ID 필요 합니다 (예를 들어 serviceadmin@contoso.onmicrosoft.com). EA, CSP 및 CSL 구독이 지원 됩니다. 사용 현황 보고 하는 동안 구성 된 [Azure 스택 등록](azure-stack-registration.md)합니다.

> [!NOTE]
> 대부분의 경우에서 기업 고객은 EA 구독을 사용 하며 서비스 공급자는 CSP 또는 CSL 구독을 사용 합니다.

CSP 구독을 사용 하도록 하려는 경우의 올바른 방법은에 따라 달라 지므로 정확한 CSP 시나리오는 CSP를 사용 하도록 구독을 식별 하려면 아래 표를 검토 합니다.

|시나리오|도메인 및 구독 옵션|
|-----|-----|
|한 **직접 CSP 파트너** 또는 **간접 CSP 공급자**, Azure 스택 작동 합니다|CSL (공용 서비스 계층) 구독을 사용 합니다.<br>     또는<br>파트너 센터에서 설명 하는 이름으로 Azure AD 테 넌 트를 만듭니다. 예를 들어 &lt;조직 > 연결 된 Azure CSP 구독과 CSPAdmin 합니다.|
|한 **간접 CSP Reseller**, Azure 스택 작동 합니다|연결 된 파트너 센터를 사용 하는 CSP Azure 구독을 사용 하 여 조직에 Azure AD 테 넌 트를 만들려면 간접 CSP 공급자에 게 문의 하십시오.|

### <a name="capacity-based-billing"></a>기반된 요금 청구 용량
용량 청구 모델을 사용 하려는 경우 Azure 스택 용량 계획 SKU 시스템의 가용성에 따라 구입 해야 합니다. 정확한 수량을 구입 하 여 Azure 스택에서 실제 코어의 수를 알아야 합니다. 

용량 청구 필요는 EA (기업 계약) 등록을 위해 Azure 구독. 이유는 Azure 구독을 필요로 하는 배포, 설정 등록입니다. Azure 스택 사용에 대 한 구독이 사용 되지 않습니다.

## <a name="learn-more"></a>자세한 정보
- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 정보를 참조 하십시오.는 [Azure 스택](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지.
- Azure 스택에 대 한 로드맵 및 지리적 가용성에 대 한 정보에 대 한 통합된 시스템 백서를 참조: [Azure 스택: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 
- Microsoft Azure 스택 패키징 및 가격에 대 한 자세한 내용을 보려면 [다운로드는.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다. 

## <a name="next-steps"></a>다음 단계
[데이터 센터 네트워크 통합](azure-stack-network.md)