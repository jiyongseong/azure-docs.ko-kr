---
title: Azure에서 외부 사용자에 대한 역할 할당 관리 | Microsoft Docs
description: Azure에서 조직 외부의 사용자에 대한 RBAC(역할 기반 액세스 제어)를 관리합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 084594b637f813c110e4e0b2e9df2b9103d58efc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203889"
---
# <a name="manage-role-assignments-for-external-users"></a>외부 사용자를 위한 역할 할당 관리

RBAC(역할 기반 액세스 제어)를 사용하면 환경의 특정 리소스에 액세스해야 하지만 전체 인프라 또는 청구 관련 범위에 액세스하지 않아도 되는 외부 공동 작업자, 공급업체 또는 프리랜서와 협력하는 대기업 및 SMB의 보안을 더 효율적으로 관리할 수 있습니다. RBAC를 통해 관리자 계정에서 관리하는 Azure 구독을 소유하는 유연성(구독 수준에서 서비스 관리자 역할)을 제공하고 동일한 구독이지만 관리 권한 없이 작업하도록 여러 사용자를 초대할 수 있습니다.

> [!NOTE]
> Office 365 관리 센터에서 프로비전된 Office 365 구독 또는 Azure Active Directory 라이선스(예: Azure Active Directory에 액세스)는 RBAC를 사용할 자격이 없습니다.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>구독 범위에서 RBAC 역할 할당

RBAC가 사용되는 일반적인 두 가지 예는 다음과 같습니다(제한되지는 않음).

* 조직의 외부 사용자를 특정 리소스 또는 전체 구독을 관리하도록 초대한 경우(관리 사용자의 Azure Active Directory 테넌트 중 일부가 아님)
* 조직 내에서 사용자와 작업(사용자의 Azure Active Directory 테넌트 중 일부)하지만 환경에서 전체 구독 또는 특정 리소스 그룹이나 리소스 범위에 대한 세분화된 액세스 권한이 필요한 다른 팀 또는 그룹의 일부인 경우

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>구독 수준에서 Azure Active Directory 외부 사용자에 대한 액세스 권한 부여

RBAC 역할은 구독의 **소유자**만 부여할 수 있습니다. 따라서 관리자는 이 역할이 미리 할당되었거나 Azure 구독을 만든 사용자로 로그인해야 합니다.

Azure Portal에서 관리자로 로그인한 후에 "구독"을 선택하고 원하는 구독을 선택합니다.
![Azure Portal의 구독 블레이드](./media/role-assignments-external-users/0.png) 기본적으로 관리 사용자가 Azure 구독을 구매한 경우 사용자는 **계정 관리자**로 표시되고 이는 구독 역할입니다. Azure 구독 역할에 대한 자세한 내용은 [구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경](../billing/billing-add-change-azure-subscription-administrator.md)을 참조하세요.

이 예제에서 사용자 "alflanigan@outlook.com"은 AAD 테넌트 "기본 테넌트 Azure"에서 "체험 평가판" 구독의 **소유자**입니다. 이 사용자가 초기 Microsoft 계정 "Outlook"을 사용하여 Azure 구독을 만들었으므로(Microsoft 계정 = Outlook, Live 등) 이 테넌트에 추가된 다른 모든 사용자의 기본 도메인 이름은 **"@alflaniganuoutlook.onmicrosoft.com"** 입니다. 기본적으로 테넌트를 만든 사용자의 사용자 이름 및 도메인 이름을 결합하고 **".onmicrosoft.com"** 확장을 추가하여 새 도메인의 구문 형식을 지정합니다.
또한 사용자는 새 테넌트에 추가하고 확인한 후에 테넌트에 있는 사용자 지정 도메인 이름을 사용하여 로그인할 수 있습니다. Azure Active Directory 테넌트에서 사용자 지정 도메인 이름을 확인하는 방법에 대한 자세한 내용은 [디렉터리에 사용자 지정 도메인 이름 추가](/active-directory/active-directory-add-domain)를 참조하세요.

이 예제에서 "기본 테넌트 Azure" 디렉터리에는 도메인 이름 "@alflanigan.onmicrosoft.com"을 가진 사용자만이 포함됩니다.

관리 사용자는 구독을 선택한 후에 **Access Control(IAM)** 및 **새 역할 추가**를 차례로 클릭해야 합니다.

![Azure Portal의 액세스 제어 IAM 기능](./media/role-assignments-external-users/1.png)

![Azure Portal의 액세스 제어 IAM 기능에서 새 사용자 추가](./media/role-assignments-external-users/2.png)

다음 단계에서는 할당하고 역할 및 RBAC 역할에 할당할 사용자를 선택합니다. **역할** 드롭다운 메뉴에서는 Azure에서 사용할 수 있는 기본 제공 RBAC 역할만을 관리 사용자에게 표시합니다. 각 역할 및 해당 할당 가능한 범위에 대한 자세한 설명은 [Azure 역할 기반 Access Control의 기본 제공 역할](built-in-roles.md)을 참조하세요.

관리 사용자는 외부 사용자의 이메일 주소를 추가해야 합니다. 예상된 동작을 기존 테넌트에서 외부 사용자에세 표시하지 않습니다. 외부 사용자를 초대한 후에 현재 구독 범위에서 RBAC 역할에 할당한 모든 사용자와 함께 **[구독] > [액세스 제어(IAM)]** 를 표시합니다.

![새 RBAC 역할에 사용 권한 추가](./media/role-assignments-external-users/3.png)

![구독 수준에서 RBAC 역할 목록](./media/role-assignments-external-users/4.png)

사용자 "chessercarlton@gmail.com"은 "체험 평가판" 구독에 대한 **소유자**로 초대되었습니다. 외부 사용자는 초대를 보낸 후에 활성화 링크를 포함한 이메일 확인을 받게 됩니다.
![RBAC 역할에 대한 이메일 초대](./media/role-assignments-external-users/5.png)

조직의 외부에 있는 새 사용자는 "기본 테넌트 Azure" 디렉터리에서 기존 특성을 갖지 않습니다. 해당 특성은 외부 사용자가 역할을 할당한 구독과 연결되어 있는 디렉터리에 기록하는 데 동의한 후에 만들어집니다.

![RBAC 역할에 대한 이메일 초대 메시지](./media/role-assignments-external-users/6.png)

외부 사용자는 지금부터 Azure Active Directory 테넌트에서 외부 사용자로 표시되고 Azure Portal에서 볼 수 있습니다.

![사용자 블레이드 Azure Active Directory Azure Portal](./media/role-assignments-external-users/7.png)

**사용자** 보기에서는 외부 사용자가 Azure Portal의 다른 아이콘 형식으로 인식될 수 있습니다.

그러나 **구독** 범위에서 외부 사용자에 대한 **소유자** 또는 **참가자** 액세스 권한을 부여하면 **전역 관리자**가 허용하지 않는 한 관리 사용자의 디렉터리에 대한 액세스를 허용하지 않습니다. 사용자 속성에서 두 공통 매개 변수가 있는 **사용자 형식**, **멤버** 및 **게스트**를 식별할 수 있습니다. 멤버는 디렉터리에 등록되어 있는 사용자인 반면 게스트는 외부 소스의 디렉터리로 초대된 사용자입니다. 자세한 내용은 [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](../active-directory/active-directory-b2b-admin-add-users.md)을 참조하세요.

> [!NOTE]
> 포털에서 자격 증명을 입력한 후에 외부 사용자가 올바른 디렉터리에 로그인하는지 확인합니다. 동일한 사용자는 여러 디렉터리에 대한 액세스 권한이 있을 수 있고 Azure Portal의 오른쪽 위에 있는 사용자 이름을 클릭 그 중 하나를 선택한 다음 드롭다운 목록에서 적절한 디렉터리를 선택할 수 있습니다.

디렉터리에 있는 게스트인 경우 외부 사용자는 Azure 구독에 대한 모든 리소스를 관리할 수 있지만 디렉터리에 액세스할 수 없습니다.

![Azure Active Directory Azure Portal에 제한된 액세스](./media/role-assignments-external-users/9.png)

Azure Active Directory와 Azure 구독에는 다른 Azure 리소스와 Azure 구독이 가진 것과 같은 자식-부모 관계가 없습니다(예: 가상 머신, 가상 네트워크, 웹앱, 저장소 등). 후자가 모두 Azure 구독에서 생성되고 관리되며 요금이 청구되는 반면 Azure 구독은 Azure 디렉터리에 대한 액세스를 관리하는 데 사용됩니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](/active-directory/active-directory-how-subscriptions-associated-directory)를 참조하세요.

모든 기본 제공 RBAC 역할에서 **소유자** 및 **참가자**는 환경에서 모든 리소스에 대한 완전한 관리 액세스를 제공합니다. 두 역할의 차이는 새 RBAC 역할을 만들고 삭제할 수 없다는 점입니다. **Virtual Machine 참여자**와 같은 다른 기본 제공 역할은 생성되는 **리소스 그룹**에 관계없이 이름으로 표시된 리소스에만 완전한 관리 액세스를 제공합니다.

**Virtual Machine 참여자**의 기본 제공 RBAC 역할을 구독 수준에서 할당한다는 것은 사용자에게 역할을 할당했다는 의미입니다.

* 해당 배포 날짜 및 일부인 리소스 그룹에 관계 없이 모든 가상 머신을 볼 수 있습니다.
* 구독에는 가상 머신에 대한 완전한 관리 액세스 권한이 있습니다.
* 구독에서 다른 리소스 형식을 볼 수 없습니다.
* 요금 청구 관점에서 변경 사항을 수행할 수 없습니다.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>외부 사용자에게 기본 제공 RBAC 역할 할당

이 테스트의 다른 시나리오에서 외부 사용자 "alflanigan@gmail.com"은 **Virtual Machine 참여자**로 추가됩니다.

![가상 머신 참여자 기본 제공 역할](./media/role-assignments-external-users/11.png)

이 기본 제공 역할을 가진 외부 사용자에 대한 정상 동작은 가상 머신과 배포 시 필요한 리소스에 인접한 Resource Manager만을 보고 관리하는 것입니다. 기본적으로 이러한 제한된 역할은 Azure Portal에서 만든 해당 리소스에 대해서만 액세스를 제공합니다.

![Azure Portal에서 가상 머신 참여자 역할 개요](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>구독 수준에서 동일한 디렉터리의 사용자에 대한 액세스 권한 부여

RBAC 역할을 부여하는 관리자 관점뿐만 아니라 역할에 대한 액세스 권한이 부여된 사용자 관점에서도 프로세스 흐름은 외부 사용자를 추가하는 것과 동일합니다. 여기서 차이점은 구독 내의 모든 리소스 범위를 로그인한 후에 대시보드에서 사용할 수 있는 경우 초대받은 사용자가 구독 내에서 어떤 이메일 초대도 수신하지 않는다는 것입니다.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>리소스 그룹 범위에서 RBAC 역할 할당

**리소스 그룹** 범위에서 RBAC 역할을 할당하는 작업은 외부 또는 내부라는 두 종류의 사용자(동일한 디렉터리의 일부)에게 구독 수준에서 역할을 할당하는 동일한 프로세스입니다. 환경에서 리소스 그룹을 확인하는 RBAC 역할이 할당되어 있는 사용자는 Azure Portal의 **리소스 그룹** 아이콘에서 액세스 권한이 할당됩니다.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>리소스 범위에서 RBAC 역할 할당

Azure의 리소스 범위에서 RBAC 역할을 할당하는 작업은 구독 수준 또는 리소스 그룹 수준에서 역할을 할당하는 동일한 프로세스이며 두 시나리오에 대해 동일한 워크플로를 따릅니다. 액세스가 할당된 항목만을 볼 수 있는 RBAC 역할이 할당되어 있는 사용자는 **모든 리소스** 탭 또는 대시보드에서 직접 확인할 수 있습니다.

RBAC에서 리소스 그룹 범위 또는 리소스 범위에 대한 중요한 측면은 사용자가 올바른 디렉터리에 로그인하는 것입니다.

![Azure Portal에서 디렉터리 로그인](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Azure Active Directory 그룹에 대한 RBAC 역할 할당

Azure의 세 가지 다른 범위에서 RBAC를 사용하는 모든 시나리오는 개인 구독을 관리할 필요 없이 할당된 사용자로 다양한 리소스를 관리, 배포 및 관리하는 권한을 제공합니다. RBAC 역할이 구독, 리소스 그룹 또는 리소스 범위에 할당되었는지와 관계없이 뒤에 나오는 할당된 사용자가 만든 리소스는 모두 사용자가 액세스할 수 있는 하나의 Azure 구독에서 청구됩니다. 이러한 방식으로 전체 Azure 구독에 대해 청구 관리자 권한을 가진 사용자는 리소스를 관리하는 사용자가 누구인지와 관계없이 소비에 대한 전체 개요를 가집니다.

대규모 조직의 경우 RBAC 역할은 Azure Active Directory 그룹에게 동일한 방식으로 적용되어 관리 사용자가 각 사용자에 각각이 아닌 팀 또는 전체 부서에 대한 세부적인 액세스 권한을 부여하는 관점을 고려할 수 있습니다. 따라서 시간 및 관리 효율성이 뛰어난 옵션으로 생각합니다. 이 예제를 설명하기 위해 **참가자** 역할은 구독 수준에서 테넌트의 그룹 중 하나에 추가되었습니다.

![AAD 그룹에 대한 RBAC 역할 추가](./media/role-assignments-external-users/14.png)

이러한 그룹은 Azure Active Directory 내에서만 프로비전되고 관리되는 보안 그룹입니다.

