---
title: 포털에서 Azure 앱에 대한 ID 만들기 | Microsoft Docs
description: Azure Resource Manager에서 리소스에 대한 액세스를 관리하기 위해 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Azure Active Directory 응용 프로그램 및 서비스 주체를 만드는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: a8784a8e29e65d8abea566b5a2bf41a2ae6cadf0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 응용 프로그램 및 서비스 주체 만들기

리소스를 액세스하거나 수정해야 하는 코드가 있는 경우 Azure AD(Active Directory) 응용 프로그램을 설정합니다. AD 응용 프로그램에 필수 사용 권한을 할당합니다. 고유한 사용 권한이 아닌 앱 ID에 대한 사용 권한을 할당할 수 있기 때문에 이 방법은 고유한 자격 증명을 사용하여 앱을 실행하는 것이 좋습니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.

이 문서에서는 포털을 통해 이러한 단계를 수행하는 방법을 보여 줍니다. 여기서는 응용 프로그램을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 응용 프로그램을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용하게 됩니다.

> [!IMPORTANT]
> 서비스 주체를 만드는 대신 응용 프로그램 ID에 Azure AD 관리되는 서비스 ID를 사용하는 것이 좋습니다. Azure AD MSI는 코드에 대한 ID 만들기를 간소화하는 Azure Active Directory의 공개 미리 보기 기능입니다. 코드가 Azure AD MSI를 지원하는 서비스에서 실행되고 Azure Active Directory 인증을 지원하는 리소스에 액세스하는 경우 Azure AD MSI를 사용하는 것이 좋습니다. 현재 지원되는 서비스를 비롯하여 Azure AD MSI에 대한 자세한 내용은 [Azure 리소스에 대한 관리 서비스 ID](../active-directory/managed-service-identity/overview.md)를 참조하세요.

## <a name="required-permissions"></a>필요한 사용 권한

이 문서를 완료하려면 Microsoft Azure Active Directory 테넌트에 응용 프로그램을 등록하고 Azure 구독의 역할에 응용 프로그램을 할당하기 위한 충분한 권한이 있어야 합니다. 이러한 단계를 수행하기 위한 올바른 권한이 있는지 확인해보겠습니다.

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory 권한 확인

1. **Azure Active Directory**를 선택합니다.

   ![Azure Active Directory 선택](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Azure Active Directory에서 **사용자 설정**을 선택합니다.

   ![사용자 설정 선택](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. **앱 등록** 설정을 확인합니다. **예**로 설정된 경우 관리자가 아닌 사용자가 AD 앱을 등록할 수 있습니다. 이 설정에서는 Azure AD 테넌트의 모든 사용자가 앱을 등록할 수 있습니다. [Azure 구독 권한 확인](#check-azure-subscription-permissions)을 계속 진행할 수 있습니다.

   ![앱 등록 보기](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. 앱 등록 설정이 **아니요**로 설정되어 있으면 [전역 관리자](../active-directory/active-directory-assign-admin-roles-azure-portal.md)만 앱을 등록할 수 있습니다. 사용자 계정이 Azure AD 테넌트에 대한 관리자인지 확인합니다. **개요**를 선택하여 사용자 정보를 확인합니다. 계정이 사용자 역할에 할당되어 있으나 앱 등록 설정(이전 단계)이 관리자로 제한되어 있으면 관리자에게 사용자를 전역 관리자 역할로 할당하거나 사용자가 앱을 등록할 수 있게 설정하도록 요청합니다.

   ![사용자 찾기](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Azure 구독 권한 확인

Azure 구독에서 사용자 계정에 AD 앱을 역할에 할당할 수 있는 `Microsoft.Authorization/*/Write` 권한이 있어야 합니다. 이 작업에 대한 권한은 [소유자](../role-based-access-control/built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 역할을 통해 부여됩니다. 계정이 **참가자** 역할에 할당된 경우 적절한 사용 권한이 없습니다. 서비스 주체를 역할에 할당하려고 하면 오류가 발생합니다.

Azure 구독 권한을 확인하려면

1. 오른쪽 위 모서리에서 계정을 선택하고 **사용 권한**을 선택합니다.

   ![사용자 권한 선택](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. 드롭다운 목록에서 구독을 선택합니다. **이 구독에 대한 전체 액세스 세부 정보를 보려면 여기를 클릭합니다.** 를 선택합니다.

   ![사용자 찾기](./media/resource-group-create-service-principal-portal/view-details.png)

1. 할당된 사용자 역할을 확인하고 AD 앱을 역할에 할당하기 위한 적절한 권한이 있는지 확인합니다. 이러한 권한이 없으면 구독 관리자에게 사용자 액세스 관리자 역할에 사용자를 추가할 것을 요청합니다. 다음 이미지에서 사용자에게는 소유자 역할이 할당됩니다. 즉, 사용자에게는 적절한 권한이 있습니다.

   ![권한 표시](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 응용 프로그램 만들기

1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
1. **Azure Active Directory**를 선택합니다.

   ![Azure Active Directory 선택](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. **앱 등록**을 선택합니다.

   ![앱 등록 선택](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. **새 응용 프로그램 등록**을 선택합니다.

   ![앱 추가](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. 응용 프로그램에 대한 이름 및 URL을 제공합니다. 만들려는 응용 프로그램 유형으로 **웹앱/API**를 선택합니다. [네이티브 응용 프로그램](../active-directory/manage-apps/application-proxy-configure-native-client-application.md)에 대한 자격 증명을 만들 수 없으므로 해당 형식은 자동화된 응용 프로그램에 대해 작동하지 않습니다. 값을 설정한 후 **만들기**를 선택합니다.

   ![응용 프로그램 이름 지정](./media/resource-group-create-service-principal-portal/create-app.png)

응용 프로그램이 만들어졌습니다.

## <a name="get-application-id-and-authentication-key"></a>응용 프로그램 ID 및 인증 키 가져오기

프로그래밍 방식으로 로그인하는 경우 응용 프로그램에 대한 ID 및 인증 키가 필요합니다. 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. Azure Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

   ![응용 프로그램 선택](./media/resource-group-create-service-principal-portal/select-app.png)

1. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. 일부 [응용 프로그램 예제](#log-in-as-the-application)에서는 이 값을 클라이언트 ID라고 합니다.

   ![클라이언트 ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. 인증 키를 생성하려면 **설정**을 선택합니다.

   ![설정 선택](./media/resource-group-create-service-principal-portal/select-settings.png)

1. 인증 키를 생성하려면 **키**를 선택합니다.

   ![키 선택](./media/resource-group-create-service-principal-portal/select-keys.png)

1. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

   ![키 저장](./media/resource-group-create-service-principal-portal/save-key.png)

   키를 저장하면 키 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사해둡니다. 응용 프로그램으로 로그인하려면 응용 프로그램 ID와 함께 키 값을 제공합니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

   ![공유 키](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>테넌트 ID 가져오기

프로그래밍 방식으로 로그인하는 경우 인증 요청과 함께 테넌트 ID를 전달해야 합니다.

1. **Azure Active Directory**를 선택합니다.

   ![Azure Active Directory 선택](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 테넌트 ID를 가져오려면 Azure AD 테넌트에 대한 **속성**을 선택합니다.

   ![Azure AD 속성 선택](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. **디렉터리 ID**를 복사합니다. 이 값은 테넌트 ID입니다.

   ![테넌트 ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>응용 프로그램을 역할에 할당

구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. 응용 프로그램을 할당하려는 범위 수준으로 이동합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다. 대신 리소스 그룹 또는 리소스를 선택할 수 있습니다.

   ![구독 선택](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. 응용 프로그램을 할당할 특정 구독(리소스 그룹 또는 리소스)을 선택합니다.

   ![할당을 위한 구독 선택](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. **Access Control(IAM)** 을 선택합니다.

   ![액세스 선택](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. **추가**를 선택합니다.

   ![추가 선택](./media/resource-group-create-service-principal-portal/select-add.png)

1. 응용 프로그램에 할당할 역할을 선택합니다. 다음 이미지에서는 **읽기 권한자** 역할을 보여 줍니다.

   ![역할 선택](./media/resource-group-create-service-principal-portal/select-role.png)

1. 기본적으로 Azure Active Directory 응용 프로그램이 사용 가능한 옵션에 표시되지 않습니다. 응용 프로그램을 찾으려면 검색 필드에서 그 이름을 입력해야 합니다. 이를 선택합니다.

   ![앱 검색](./media/resource-group-create-service-principal-portal/search-app.png)

1. **저장**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

## <a name="next-steps"></a>다음 단계
* 다중 테넌트 응용 프로그램을 설정하려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
* 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)을 참조하세요.  
* 권한이 부여되거나 사용자에 대해 거부될 수 있는 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md)을 참조하세요.
