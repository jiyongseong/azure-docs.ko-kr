---
title: "사용 및 클라우드 서비스 공급자로 Azure 스택에 대 한 청구 관리 | Microsoft Docs"
description: "Azure 스택 클라우드 공급자로 등록 및 고객 추가 통해 탐색 합니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>사용 및 청구 Azure 스택에 대 한 클라우드 서비스 공급자 관리 

*적용 대상: Azure 스택 시스템 통합*

이 문서에서는 Azure 스택 클라우드 공급자 (CSP)로 등록 및 고객을 추가 하는 과정을 안내 합니다.

CSP를 Azure 스택을 사용 하는 다른 고객이 있을 수는 있습니다. 각 고객에 게 Azure에서 CSP 구독 하 고 각 사용자의 구독에 Azure 스택에서 사용을 직접 해야 합니다.

다음 다이어그램은 하 고 공유 서비스 계정을 선택 하 고 계정을 사용 하 여 azure 계정을 등록 해야 하는 단계를 보여 줍니다. 이 작업이 완료 되 면 때 등록할 수 최종 고객 합니다.

**사용량 추적 csp를 추가 하는 단계**

![사용 및 클라우드 서비스 공급자로 관리를 사용 하기 위한 프로세스입니다.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>CSP 또는 CSPSS 구독 만들기

### <a name="cloud-service-provider-subscription-types"></a>클라우드 서비스 공급자 구독 유형

Azure 스택에 사용 하는 공유 서비스 계정의 유형을 선택 해야 합니다. Azure 스택 다중 테 넌 트의 등록을 위해 사용할 수 있는 구독 유형은 다음과 같습니다.

 - 클라우드 서비스 공급자 
 - 파트너 공유 서비스 구독 

#### <a name="csp-shared-services"></a>CSP 공유 서비스

클라우드 서비스 공급자 공유 서비스 (CSPSS) 구독 등록 때 직접 CSP에 대 한 기본 설정된 선택 되었는지 또는 CSP 배포자 Azure 스택 작동 합니다.

CSPSS 구독은 공유 서비스 테 넌 트와 연결 합니다. Azure 스택을 등록 하면 구독 소유자가 있는 계정에 대 한 자격 증명을 제공 해야 합니다. Azure 스택 등록을 사용 하는 계정은; 배포에 사용 하는 관리자 계정과에서 다를 수 있습니다. 두 않습니다 *하지* 동일한 도메인에 속해야 합니다. 즉, 이미 사용 하는 테 넌 트를 사용 하 여 배포할 수 있습니다. 예를 들어 ContosoCSP.onmicrosoft.com를 사용 하 여 다음 다른 테 넌 트, 예를 들어 IURContosoCSP.onmicrosoft.com를 사용 하 여 등록 수 있습니다. 기억 일-이렇게 하려면 Azure 스택 관리 작업을 수행할 때 ContosoCSP.onmicrosoft.com를 사용 하 여 로그인 해야 합니다. 로그인 하면 IURContosoCSP.onmicrosoft.com 등록 작업을 수행 해야 할 때 사용 하 여 Azure입니다.

구독을 만드는 방법에 대 한 지침 CSPSS 구독에 대 한 설명에 대 한 다음과 같은 참조 [Azure 파트너 공유 서비스 추가](https://msdn.microsoft.com/partner-center/shared-services)합니다.

#### <a name="csp-subscriptions"></a>CSP 구독

클라우드 서비스 공급자 (CSP) 구독은 등록 때 CSP 재판매인에 가장 적합 또는 최종 사용자는 Azure 스택 작동 합니다.

## <a name="register-azure-stack"></a>Azure 스택 등록

Azure 스택을 등록 하려면 참조 [Azure 구독과 Azure 스택 등록](azure-stack-registration.md)합니다.

## <a name="add-end-customer"></a>최종 사용자를 추가 합니다.

새 테 넌 트 리소스를 사용 하 고 사용법을 자신의 클라우드 서비스 공급자 (CSP) 구독에 보고 됩니다 되도록 Azure 스택을 구성 하려면 참조 [사용에 대 한 테 넌 트를 추가 하 고 Azure 스택에 청구](azure-stack-csp-howto-register-tenants.md)합니다.

## <a name="charge-the-right-subscriptions"></a>오른쪽 구독 요금을 청구합니다

Azure 스택 등록 이라는 기능을 사용 합니다. 등록은 주어진된 Azure 스택에 대 한 요금을 청구 하는 데는 Azure 구독에 설명 하며 Azure에 저장 된 개체입니다. 이 섹션에서는 등록의 중요도입니다.

등록을 사용 하 여 Azure 스택 수행할 수 있습니다.
 - Azure 스택 사용 현황 데이터를 Azure Commerce 전달 하 고 Azure 구독 요금을 청구 합니다.
 - 다중 테 넌 트 Azure 스택 배포와는 다른 구독에 각 고객의 사용을 보고 합니다. 다중 테 넌 트 Azure 스택 동일한 Azure 스택 인스턴스에서 서로 다른 조직을 지원할 수 있습니다.

각 Azure 스택 하는 하나의 기본 구독 되 고 필요에 따라 테 넌 구독 합니다. 기본 구독이 테 넌 트 별 등록이 없는 경우 청구 되는 Azure 구독이입니다. 가장 먼저 등록 해야 합니다. 보고 기능이 작동 하려면 다중 테 넌 트 사용을 위해 구독 CSP 또는 CSPSS 구독 이어야 합니다.

그런 다음 Azure 스택을 이용 하는 각 테 넌 트에 대 한 Azure 구독 등록이 업데이트 됩니다. 테 넌 트 구독 CSP 유형 이어야 하며 기본 구독을 소유 하 고 파트너에 겹쳐서 표시 합니다. 즉, 다른 사용자의 고객을 등록할 수 없습니다.

Azure 스택이 글로벌 Azure에 사용 정보를 전달할 때 Azure에서 서비스 등록을 참조 하 고 적절 한 테 넌 트 구독에 각 테 넌 트 사용을 매핑합니다. 테 넌 트가 등록 되지 않은 경우 해당 사용 백업이 시작 된 Azure 스택 인스턴스에 대 한 기본 구독으로 이동 합니다.

테 넌 트 구독 CSP 구독 되므로 해당 청구서 CSP 파트너에 게 보내기 및 사용 정보는 최종 사용자에 게 표시 되지 않습니다.



## <a name="next-steps"></a>다음 단계

 - CSP 프로그램에 대 한 자세한 참조 [클라우드 솔루션 공급자 프로그램](https://partnercenter.microsoft.com/en-us/partner/programs)합니다.
 - 스택에서 Azure 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 참조 [사용 및 청구 Azure 스택의](/azure-stack-billing-and-chargeback.md)합니다.
