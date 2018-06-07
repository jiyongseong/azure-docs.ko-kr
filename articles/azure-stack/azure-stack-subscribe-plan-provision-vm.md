---
title: "Azure 스택에 제품 구독 | Microsoft Docs"
description: "Azure 스택에서 제안에 대 한 구독 만들기"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Azure 스택에서 제안에 대 한 구독 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

후 [제안을 만들](azure-stack-create-offer.md), 사용자를 사용할 수 있습니다 해당 제품에 대 한 구독에 필요 합니다.   
- 클라우드 연산자로 관리자 포털 내에서 사용자에 대 한 구독을 만들 수 있습니다.  공용 및 개인 제품에 대 한 구독을 만들 수 있습니다.
- 테 넌 트 사용자로 사용자 포털을 사용 하 여 때 공용 제안에 가입할 수 있습니다.  

다음 섹션에서는 사용자 및 사용자로 제공 하는 서비스를 구독 하는 방법에 대 한 구독을 만들 때 클라우드 운영자에 대 한 지침을 제공 합니다.

## <a name="create-a-subscription-as-a-cloud-operator"></a>클라우드 연산자로 구독 만들기
클라우드 운영자는 사용자에 대 한 제안에 구독을 만들려면 관리 포털을 사용할 수 있습니다.  자신의 디렉터리 테 넌 트의 멤버에 대 한 구독을 만들 수 있습니다.  때 [다중 테 넌 트](azure-stack-enable-multitenancy.md) 가 활성화 만들 수도 있습니다 사용자에 대 한 구독 추가 디렉터리 테 넌 트에 있습니다.

공용 및 개인 제품에 대 한 구독을 만들 수 있습니다.  경우을 자신의 구독을 만들고 모든 제안 private, 확인, 다음 테 넌 트를 대신 하 여 구독을 만들 테 넌 트를 원하지 않는 합니다. Azure 스택 외부 청구 또는 카탈로그 시스템 서비스와 통합 하는 경우이 방법이 일반적입니다.

사용자에 대 한 구독을 만든 후 해당 사용자는 사용자 포털에 로그인 할 수와 제품에 구독을 찾을 수 있습니다.  

### <a name="to-create-the-subscription-for-a-user"></a>사용자에 대 한 구독을 만들려면
1.  관리 포털에서로 이동 **사용자 구독 합니다.**
2.  선택 **추가** 열려는 **새 사용자 구독** 창. 여기서 다음 세부 정보를 지정합니다.  

  a. **표시 이름** – 이름으로 표시 되는 구독을 식별 하는 데는 *사용자 구독 이름*합니다.

  나. **사용자** –이 구독에 대 한 사용 가능한 디렉터리 테 넌 트의 사용자를 지정 합니다. 사용자 이름으로 표시 *소유자*합니다.  사용자 이름의 형식은 id 솔루션에 따라 달라 집니다. 예:    

     -  **Azure AD:**  *&lt;user1>@&lt;contoso.onmicrosoft.com>*

     -   **AD FS:**  *&lt;user1>@&lt;azurestack.local>*     

  다.    **디렉터리 테 넌 트** – 디렉터리 테 넌 트 사용자 계정이 속하는 위치를 선택 합니다. 다중 테 넌 트를 설정 하지 않은 경우 로컬 디렉터리 테 넌 트만 ´ ù.

3.  선택 **제공** 열려는 **제공** 창에서 선택한 후는 **제공** 이 구독에 대 한 합니다. 사용자에 대 한 구독을 작성 하는 사설 또는 공용 제품을 선택할 수 있습니다.

4.  선택 **만들기** 구독을 만듭니다. **사용자 구독** 창에는 이제 새 구독 표시 됩니다.  이상 버전에서는 사용자가 사용자 포털에 로그온 하는 경우이 구독에 대 한 세부 정보를 볼 수 있습니다.

### <a name="to-make-an-add-on-plan-available"></a>추가 기능 계획을 사용할 수 있도록 하려면  
클라우드 운영자는 언제 든 지 이전에 만든된 구독에 추가 기능 계획을 추가할 수 있습니다.   
1.  관리 포털에서로 이동 **더 서비스** > **사용자 구독**, 한 다음 변경할 구독을 선택 합니다.   

2.  선택 **추가 기능** > **추가** 열려는 **계획 추가** 창.  

3.  추가 기능으로 추가할 계획을 선택 합니다.  콘솔에는 다음 구독에 연결 된 계획이 표시 됩니다.




## <a name="create-a-subscription-as-a-user"></a>사용자로 구독 만들기
사용자를 찾아 (조직) 디렉터리 테 넌 트에서 구독 공용 혜택 및 추가 기능이 계획에 사용자 포털 서명할 수 있습니다. Azure 스택 환경에서 지 원하는 [다중 테 넌 트](azure-stack-enable-multitenancy.md) 원격 디렉터리 테 넌 트에서 제품을 구독할 수 있습니다.

### <a name="to-subscribe-to-an-offer"></a>제공 하는 서비스를 구독 하려면
1. [로그인](azure-stack-connect-azure-stack.md) Azure 스택 사용자 포털 (https://portal.local.azurestack.external)를 클릭 하 여 **구독**합니다.

   ![구독 가져오기](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. 에 **표시 이름** 필드, 구독에 대 한 이름을 입력 하 고을 클릭 **제공**를에 제공 하는 중 하나를 클릭는 **제안을 선택** 창과 클릭  **만들**합니다.

   ![제품 만들기](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 만든 구독을 보려면 클릭 **더 많은 서비스**, 클릭 **구독**, 새 구독을 클릭 합니다.  

제공 하는 서비스를 구독 한 후 새 구독의 일부인 서비스를 확인 하기 위해 포털을 새로 고칩니다.

### <a name="to-subscribe-to-an-add-on-plan"></a>추가 기능 계획을 구독 하려면
제공 하는 추가 기능이 계획에 있는 경우 언제 든 지 구독에 해당 하는 계획을 추가할 수 있습니다.  

1. 사용자 포털에서 선택 **더 많은 서비스** > **구독**, 한 다음 변경 구독을 선택 합니다.

2. 선택 **계획 추가** 단추를 선택한 다음 원하는 추가 기능 계획을 선택 합니다. 경우 **계획 추가** 은 사용할 수 없는 다음 제안에 대 한 추가 기능 계획이 없는 해당 구독과 연결 된입니다.



## <a name="next-steps"></a>다음 단계
[가상 머신 프로비전](azure-stack-provision-vm.md)
