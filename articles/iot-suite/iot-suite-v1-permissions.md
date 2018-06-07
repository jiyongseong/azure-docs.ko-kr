---
title: "Azure IoT Suite 및 Azure Active Directory | Microsoft Docs"
description: "Azure IoT Suite에서 Azure Active Directory를 사용하여 사용 권한을 관리하는 방법을 설명합니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a032fc4332c697748e658ad2615ed5b0915c56c1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>azureiotsuite.com 사이트에 대한 사용 권한

## <a name="what-happens-when-you-sign-in"></a>로그인 후 진행되는 작업

[azureiotsuite.com][lnk-azureiotsuite]에 처음 로그인하면, 사이트에서는 현재 선택된 AAD(Azure Active Directory) 테넌트 및 Azure 구독을 기반으로 사용자가 가진 사용 권한 수준을 결정합니다.

1. 로그인 사용자 이름 옆에 표시되는 테넌트 목록을 채우려면 먼저 사이트가 Azure로부터 사용자가 속하는 AAD 테넌트를 알아내야 합니다. 현재 사이트는 테넌트에 대한 사용자 토큰을 한 번에 하나만 가져올 수 있습니다. 따라서 오른쪽 상단 모서리의 드롭다운을 사용하여 테넌트로 전환하면, 사이트는 이 테넌트에 대한 토큰을 가져오기 위해서 해당 테넌트로 사용자를 다시 로그인합니다.

2. 다음으로, 사이트는 사용자가 선택한 테넌트와 연결된 구독을 Azure로부터 알아냅니다. 미리 구성된 솔루션을 새로 만들 때 사용할 수 있는 구독이 표시됩니다.

3. 마지막으로 사이트는 미리 구성된 솔루션으로 태그가 지정된 구독 및 리소스 그룹에서 모든 리소스를 가져와서 홈 페이지에 타일을 채웁니다.

다음 섹션은 미리 구성된 솔루션에 대한 액세스를 제어하는 역할을 설명합니다.

## <a name="aad-roles"></a>AAD 역할

AAD 역할은 권한 프로비전 미리 구성된 솔루션을 제어하고 미리 구성된 솔루션의 사용자를 관리합니다.

AAD에서 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당][lnk-aad-admin]에서 찾을 수 있습니다. 현재 문서에서는 미리 구성된 솔루션에서 사용된 대로 **전역 관리자** 및 **사용자** 디렉터리 역할에 중점을 둡니다.

### <a name="global-administrator"></a>전역 관리자

AAD 테넌트에는 여러 명의 전역 관리자가 있을 수 있습니다.

* AAD 테넌트를 만들 때, 만드는 사람은 기본적으로 해당 테넌트의 전역 관리자입니다.
* 전역 관리자는 미리 구성된 솔루션을 프로비전할 수 있으며 해당 AAD 테넌트 내부의 응용 프로그램에 대해 **관리자** 역할이 할당됩니다.
* 동일한 AAD 테넌트의 다른 사용자가 응용 프로그램을 만들 경우, 전역 사용자에게 부여되는 기본 역할은 **읽기 전용**입니다.
* 전역 관리자는 [Azure Portal][lnk-portal]을 사용하여 사용자를 응용 프로그램의 역할에 할당할 수 있습니다.

### <a name="domain-user"></a>도메인 사용자

AAD 테넌트에는 여러 명의 도메인 사용자가 있을 수 있습니다.

* 도메인 사용자는 [azureiotsuite.com][lnk-azureiotsuite] 사이트를 통해 미리 구성된 솔루션을 프로비전할 수 있습니다. 기본적으로 도메인 사용자에게는 프로비전된 응용 프로그램의 **Admin** 역할이 부여됩니다.
* 도메인 사용자는 [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo] 또는 [azure-iot-connected-factory][lnk-cf-github-repo] 리포지토리에서 build.cmd 스크립트를 사용하여 응용 프로그램을 만들 수 있습니다. 하지만 도메인 사용자에게는 역할을 할당할 권한이 없으므로 도메인 사용자에게 부여되는 기본 역할은 **읽기 전용**입니다.
* 동일한 AAD 테넌트의 다른 사용자가 응용 프로그램을 만들 경우, 해당 도메인 사용자에게는 기본적으로 해당 응용 프로그램에 대한 **읽기 전용** 역할이 할당됩니다.
* 도메인 사용자는 응용 프로그램에 역할을 할당할 수 없기 때문에, 응용 프로그램을 프로비전했더라도 응용 프로그램에 사용자를 추가하거나 사용자에 대한 역할을 추가할 수 없습니다.

### <a name="guest-user"></a>게스트 사용자

AAD 테넌트에는 여러 명의 게스트 사용자가 있을 수 있습니다. 게스트 사용자는 AAD 테넌트 내에서 제한된 권한 집합을 갖습니다. 결과적으로 게스트 사용자는 AAD 테넌트 내에서 미리 구성된 솔루션을 프로비전할 수 없습니다.

AAD의 사용자 및 역할에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure AD에서 사용자 만들기][lnk-create-edit-users]
* [앱에 사용자 할당][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure 구독 관리자 역할

Azure 관리자 역할은 Azure 구독을 AD 텐넌트에 매핑할 수 있는 권한을 제어합니다.

[Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법][lnk-admin-roles] 문서에서 Azure 관리자 역할에 대해 자세히 알아보세요.

## <a name="application-roles"></a>응용 프로그램 역할

응용 프로그램 역할은 미리 구성된 솔루션의 장치에 대한 액세스를 제어합니다.

프로비전된 응용 프로그램에는 정의된 역할 두 개와 정의된 암시적 역할 한 개가 있습니다.

* **관리:** 설정을 추가, 관리, 장치, 제거 및 수정하는 모든 권한을 가집니다.
* **읽기 전용:** 장치, 규칙, 작업, 작업 및 원격 분석을 볼 수 있습니다.

[RolePermissions.cs][lnk-resource-cs] 소스 파일에서 각 역할에 할당된 권한을 찾을 수 있습니다.

### <a name="changing-application-roles-for-a-user"></a>사용자에 대한 응용 프로그램 역할 변경

다음 절차를 사용하여 Active Directory의 사용자를 미리 구성된 솔루션의 관리자로 만들 수 있습니다.

사용자에 대한 역할을 변경하려면 AAD 전역 관리자여야 합니다.

1. [Azure Portal][lnk-portal]로 이동합니다.
2. **Azure Active Directory**를 선택합니다.
3. 이 솔루션을 프로비전할 때 azureiotsuite.com에서 선택한 디렉터리를 사용해야 합니다. 구독과 관련된 여러 디렉터리가 있는 경우 포털의 오른쪽 상단에 있는 사용자 계정 이름을 클릭하면 디렉터리를 전환할 수 있습니다.
4. **엔터프라이즈 응용 프로그램** 및 **모든 응용 프로그램**을 클릭합니다.
4. **모든** 상태의 **모든 응용 프로그램**을 표시합니다. 미리 구성된 솔루션의 이름을 가진 응용 프로그램을 검색합니다.
5. 미리 구성된 솔루션 이름과 일치하는 응용 프로그램의 이름을 클릭합니다.
6. **사용자 및 그룹**을 클릭합니다.
7. 역할을 변경할 사용자를 선택합니다.
8. **할당**을 클릭하고 사용자에게 할당하려는 역할(예: **Admin**)을 선택한 다음 확인 표시를 클릭합니다.

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>서비스 관리자가 구독과 특정 AAD 테넌트 간의 디렉터리 매핑을 변경하고자 합니다. 이 태스크를 완료하려면 어떻게 해야 하나요?

[Azure AD 디렉터리에 기존 구독을 추가하는 방법](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)을 참조하세요.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>AAD 테넌트의 도메인 사용자/회원이 미리 구성된 솔루션을 만들었습니다. 응용 프로그램에 대한 역할을 어떻게 할당하나요?

전역 관리자에게 AAD 테넌트에 대한 전역 관리자로 만들어 달라고 요청한 후 사용자에게 직접 역할을 할당하세요. 또는 전역 관리자에게 역할을 직접 할당해 달라고 요청하세요. 미리 구성된 솔루션이 배포된 AAD 테넌트를 변경하려면 다음 질문을 참조하세요.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>원격 모니터링 미리 구성된 솔루션 및 응용 프로그램이 할당되어 있는 AAD 테넌트를 어떻게 변경하나요?

<https://github.com/Azure/azure-iot-remote-monitoring>에서 클라우드 배포를 실행하고 새로 생성된 AAD 테넌트와 다시 배포할 수 있습니다. AAD 테넌트를 만들 때 기본적으로 전역 관리자가 되기 때문에, 사용자를 추가하고 해당 사용자에 역할을 추가하는 사용 권한을 갖게 됩니다.

1. [Azure Portal][lnk-portal]에서 AAD 디렉터리를 만듭니다.
2. <https://github.com/Azure/azure-iot-remote-monitoring>으로 이동합니다.
3. `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}`(예: `build.cmd cloud debug myRMSolution`)를 실행합니다.
4. 프롬프트가 표시되면, **tenantid** 를 새로 만든 테넌트가(이전 테넌트 대신) 되도록 설정합니다.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>조직 계정으로 로그인 할 때 서비스 관리자 또는 공동 관리자를 변경하려고 합니다.

지원 문서 [조직 계정으로 로그인하였을 때 서비스 관리자 및 공동 관리자 변경][lnk-service-admins]을 참조하세요.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>왜 이 오류가 표시되나요? "Your account does not have the proper permissions to create a solution.(사용자의 계정에 솔루션을 생성하기에 적합한 권한이 없습니다.) Please check with your account administrator or try with a different account.(계정 관리자에게 문의하거나 다른 계정으로 시도하세요.)"

지침은 다음 다이어그램을 살펴보겠습니다.

![][img-flowchart]

> [!NOTE]
> AAD 테넌트에 대한 전역 관리자이고 구독에 대한 공동 관리자임을 확인한 후에 오류가 계속 표시되는 경우 계정 관리자를 통해 사용자를 제거하고 다음과 같은 순서로 필요한 사용 권한을 다시 할당합니다. 먼저 사용자를 전역 관리자로 추가한 다음 Azure 구독에 대한 공동 관리자로 추가합니다. 문제가 지속되면 [도움말 및 지원][lnk-help-support]에 문의하세요.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure 구독이 있는데 왜 이런 오류가 표시되나요? "Azure 구독은 미리 구성된 솔루션을 만드는 데 필요합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다."

Azure 구독이 있는 것이 확실하다면, 구독에 대한 테넌트 매핑의 유효성을 검사하고 드롭다운에 올바른 테넌트가 선택되어 있는지 확인하세요. 원하는 테넌트가 맞는다는 것을 확인했으면, 이전의 다이어그램에 따라서 구독과 이 AAD 테넌트 매핑의 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계
IoT Suite에 대해 계속 알아보려면 [미리 구성된 솔루션을 사용자 지정][lnk-customize]하는 방법을 참조하세요.

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
