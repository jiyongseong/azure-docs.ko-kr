---
title: Azure Active Directory 그룹 기반 라이선스 추가 시나리오 | Microsoft Docs
description: Azure Active Directory 그룹 기반 라이선스의 추가 시나리오
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: mtillman
editor: piotrci
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb8bd727618eda2a887cc9e1b739889204eb87fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Azure Active Directory에서 라이선스 관리를 위해 그룹을 사용하는 경우 시나리오, 제한 사항 및 알려진 문제

다음 정보와 예제를 사용하면 Azure AD(Azure Active Directory) 그룹 기반 라이선스에 대해 자세히 이해할 수 있습니다.

## <a name="usage-location"></a>사용 위치

일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자에 대해 **사용 위치** 속성을 지정해야 합니다. [Azure Portal](https://portal.azure.com)의 **사용자** &gt; **프로필** &gt; **설정**에서 지정할 수 있습니다.

그룹 라이선스 할당의 경우 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 사용자가 여러 위치에 있는 경우 라이선스가 있는 그룹에 사용자를 추가하기 전에 해당 사실이 사용자 개체에 제대로 반영되는지 확인합니다.

> [!NOTE]
> 그룹 라이선스 할당이 이루어져도 사용자에 대한 기존 사용 위치는 수정되지 않습니다. 항상 Azure AD에서 사용자 만들기 흐름의 일부로(예: AAD Connect 구성을 통해) 사용 위치를 설정하는 것이 좋습니다. 이렇게 하면 라이선스 할당의 결과가 항상 정확하고 허용되지 않은 위치에서 사용자에게 서비스가 제공되지 않습니다.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>동적 그룹에서 그룹 기반 라이선스 사용

모든 보안 그룹에서 그룹 기반 라이선스를 사용할 수 있으며, 이는 Azure AD 동적 그룹과 결합할 수 있음을 의미합니다. 동적 그룹은 사용자 개체 특성에 대한 규칙을 실행하여 그룹에서 사용자를 자동으로 추가하거나 제거합니다.

예를 들어 사용자에게 할당하려는 일부 제품 집합에 대한 동적 그룹을 만들 수 있습니다. 각 그룹은 특성을 기준으로 사용자를 추가하는 규칙을 통해 채워지고 각 그룹에는 사용자에게 제공하려고 의도한 라이선스가 할당됩니다. 특성을 온-프레미스에 할당하여 Azure AD와 동기화할 수 있거나 클라우드에서 특성을 직접 관리할 수 있습니다.

그룹에 추가된 직후에 라이선스가 사용자에게 할당됩니다. 특성을 변경하면 사용자는 그룹에서 나가고 라이선스가 제거됩니다.

### <a name="example"></a>예

어떤 사용자가 Microsoft 웹 서비스에 액세스해야 하는지 결정하는 온-프레미스 ID 관리 솔루션의 예를 고려하세요. 이 솔루션은 **extensionAttribute1**을 사용하여 사용자가 가지고 있어야 하는 라이선스를 나타내는 문자열 값을 저장합니다. Azure AD Connect는 이 값을 Azure AD와 동기화합니다.

사용자에 따라 라이선스 중 하나만 필요하거나 두 라이선스가 모두 필요할 수 있습니다. Office 365 Enterprise E5 및 EMS(Enterprise Mobility + Security) 라이선스를 그룹의 사용자에게 배포하는 예는 다음과 같습니다.

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: 기본 서비스

![Office 365 Enterprise E5 기본 서비스의 스크린샷](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: 허가된 사용자

![Enterprise Mobility + Security 허가된 사용자의 스크린샷](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

이 예에서는 사용자에게 두 라이선스를 모두 할당하려는 경우 한 사용자를 수정하고 extensionAttribute1을 `EMS;E5_baseservices;` 값으로 설정합니다. 이 수정 작업은 온-프레미스에서 수행할 수 있습니다. 변경 내용이 클라우드와 동기화되면 사용자가 두 그룹에 자동으로 추가되고 라이선스가 할당됩니다.

![사용자의 extensionAttribute1을 설정하는 방법을 보여 주는 스크린샷](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> 기존 그룹의 멤버 자격 규칙을 수정할 때는 주의해야 합니다. 규칙이 변경되면 그룹의 멤버 자격을 다시 평가하고 새 규칙을 충족하지 않는 사용자를 제거합니다(새 규칙과 일치하는 사용자는 이 과정의 영향을 받지 않음). 해당 사용자가 프로세스 중에 라이선스를 제거하면 데이터의 손실 또는 일부 경우에 서비스 중단이 발생할 수 있습니다.

> 라이선스 할당을 사용하는 대규모 동적 그룹이 있는 경우 기본 그룹에 적용하기 전에 소규모 테스트 그룹에서 주요 변경 내용의 유효성을 검사하는 것이 좋습니다.

## <a name="multiple-groups-and-multiple-licenses"></a>여러 그룹 및 여러 라이선스

사용자는 라이선스를 가진 여러 그룹의 구성원이 될 수 있습니다. 고려할 사항은 다음과 같습니다.

- 동일한 제품에 대한 라이선스가 여러 개 있으면 겹칠 수 있어 사용 가능한 모든 서비스가 사용자에게 적용됩니다. 다음 예에서는 두 개의 라이선스 그룹을 보여 줍니다. *E3 기본 서비스*에는 모든 사용자에게 처음 배포할 기본 서비스가 포함되어 있으며, *E3 확장 서비스*에는 일부 사용자에게만 배포할 수 있는 추가 서비스(Sway 및 Planner)가 포함되어 있습니다. 이 예제에서 사용자는 두 그룹 모두에 추가되었습니다.

  ![사용하도록 설정된 서비스의 스크린샷](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  결과적으로 사용자가 제품의 12개 서비스 중 7개를 사용하도록 설정하는 한편, 하나의 라이선스만 이 제품에 사용합니다.

- *E3* 라이선스를 선택하면 어떤 그룹으로 인해 사용자가 어떤 서비스를 사용할 수 있는지에 대한 정보를 포함하여 자세한 정보가 표시됩니다.

  ![그룹별로 사용하도록 설정된 서비스의 스크린샷](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>그룹 라이선스와 공존하는 직접 라이선스

사용자가 그룹의 라이선스를 상속하면 사용자 속성에서 해당 라이선스 할당을 바로 제거하거나 수정할 수 없습니다. 그룹에서 변경한 다음 모든 사용자에게 전파해야 합니다.

그러나 상속된 라이선스 외에도 동일한 제품 라이선스를 사용자에게 직접 할당할 수 있습니다. 다른 사용자에게 영향을 주지 않고 한 명의 사용자만 제품의 추가 서비스를 사용하도록 설정할 수 있습니다.

직접 할당된 라이선스를 제거하더라도 상속된 라이선스에는 영향을 주지 않습니다. 그룹에서 Office 365 Enterprise E3 라이선스를 상속받는 사용자를 생각해보겠습니다.

1. 처음에 사용자는 다음과 같이 네 가지 서비스 계획을 사용할 수 있는 *E3 기본 서비스* 그룹에서만 라이선스를 상속받습니다.

  ![E3 그룹에서 사용하도록 설정된 서비스의 스크린샷](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. **할당**을 선택하여 E3 라이선스를 사용자에게 직접 할당할 수 있습니다. 이 경우 Yammer Enterprise를 제외한 모든 서비스 계획을 사용하지 않도록 설정합니다.

  ![사용자에게 라이선스를 직접 할당하는 방법의 스크린샷](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 결과적으로 사용자는 여전히 E3 제품의 라이선스 하나만 사용합니다. 그러나 직접 할당을 사용하면 해당 사용자에 대해서만 Yammer Enterprise 서비스를 사용하도록 설정할 수 있습니다. 그룹 멤버 자격과 직접 할당으로 어떤 서비스를 사용할 수 있는지는 확인할 수 있습니다.

  ![상속된 할당 대 직접 할당의 스크린샷](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 직접 할당을 사용할 때 허용되는 작업은 다음과 같습니다.

  - Yammer Enterprise는 사용자 개체에서 직접 해제 수 있습니다. 다른 서비스 토글과 달리 그림에서는 **사용/해제** 토글이 이 서비스에 대해 사용하도록 설정되었습니다. 이 서비스가 사용자에 대해 직접 사용하도록 설정되어 있으므로 수정할 수 있습니다.
  - 직접 할당된 라이선스의 일부로서 추가 서비스도 사용할 수 있습니다.
  - **제거** 단추를 사용하여 사용자로부터 직접 라이선스를 제거할 수 있습니다. 이제 사용자는 상속된 그룹 라이선스만 가지며 원래 서비스만 사용할 수 있게 됩니다.

    ![직접 할당을 제거하는 방법을 보여 주는 스크린샷](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>제품에 추가된 새 서비스 관리
Microsoft에서 제품에 새 서비스를 추가하면 해당 서비스는 제품 라이선스를 할당한 모든 그룹에서 기본적으로 사용하도록 설정됩니다. 제품 변경에 대한 알림을 구독하는 테넌트의 사용자는 예정된 서비스 추가에 대해 알리는 메일을 미리 받게 됩니다.

관리자는 변경이 영향을 미치는 모든 그룹을 검토하고 각 그룹에서 새 서비스를 사용하지 않도록 설정하는 등의 작업을 수행합니다. 예를 들어 특정 서비스만 배포할 그룹을 만든 경우 해당 그룹을 다시 방문하여 새로 추가된 서비스가 사용하지 않도록 설정되었는지 확인할 수 있습니다.

이 프로세스가 진행되는 과정의 예는 다음과 같습니다.

1. 원래 *Office 365 Enterprise E5* 제품을 여러 그룹에 할당했습니다. 해당 그룹 중 하나인 *O365 E5 - Exchange only*는 해당 구성원에 대해 *Exchange Online(계획 2)* 서비스만 사용할 수 있도록 설계되어 있습니다.

2. Microsoft에서 E5 제품이 새 서비스인 *Microsoft Stream*을 통해 확장될 것이라는 알림을 받았습니다. 서비스가 테넌트에서 사용 가능해지면 다음을 수행할 수 있습니다.

3. [**Azure Active Directory > 라이선스 > 모든 제품**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) 블레이드로 이동하고 *Office 365 Enterprise E5*, **허가된 그룹**을 차례로 선택하여 해당 제품을 가진 모든 그룹 목록을 표시합니다.

4. 검토할 그룹을 클릭합니다(이 경우 *O365 E5 - Exchange only*). 그러면 **라이선스** 탭이 열립니다. E5 라이선스를 클릭하면 모든 사용 가능한 서비스가 나열된 블레이드가 열립니다.
> [!NOTE]
> *Exchange Online* 서비스 이외에 *Microsoft Stream* 서비스가 이 그룹에 자동으로 추가되어 사용하도록 설정되었습니다.

  ![그룹 라이선스에 추가된 새 서비스의 스크린샷](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. 이 그룹에서 새 서비스를 사용하지 않으려면 서비스 옆에 있는 **설정/해제** 토글을 클릭하고 **저장** 단추를 클릭하여 변경을 확인합니다. 이제 Azure AD에서 그룹의 모든 사용자를 처리하여 변경을 적용합니다. 그룹에 새로 추가된 사용자는 *Microsoft Stream* 서비스를 사용할 수 없습니다.

  > [!NOTE]
  > 사용자는 일부 다른 라이선스 할당(직접 라이선스 할당의 구성원으로 포함된 다른 그룹)을 통해서만 서비스를 사용할 수 있습니다.

6. 필요한 경우 이 제품이 할당된 다른 그룹에 대해 동일한 단계를 수행합니다.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell을 사용하여 누가 상속됨과 직접 라이선스를 가지고 있는지 확인
PowerShell 스크립트를 사용하여 사용자가 라이선스를 직접 할당받는지 아니면 그룹에서 상속받는지를 확인할 수 있습니다.

1. `connect-msolservice` cmdlet을 실행하여 인증하고 테넌트에 연결합니다.

2. `Get-MsolAccountSku`는 테넌트에서 프로비전된 모든 제품 라이선스를 검색하는 데 사용할 수 있습니다.

  ![Get-msolaccountsku cmdlet의 스크린샷](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. [이 PowerShell 스크립트](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)에서 관심있는 라이선스에 *AccountSkuId* 값을 사용합니다. 라이선스가 할당된 방법에 대한 정보를 사용하여 이 라이선스가 있는 사용자가 목록이 생성됩니다.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>감사 로그를 사용하여 그룹 기반 라이선스 작업 모니터링

[Azure AD 감사 로그](./active-directory-reporting-activity-audit-logs.md#audit-logs)를 사용하여 다음을 비롯한 그룹 기반 라이선스와 관련된 모든 작업을 확인할 수 있습니다.
- 그룹에 대한 라이선스를 변경한 사용자
- 시스템에서 그룹 라이선스 변경 처리를 시작한 시점 및 종료한 시점
- 그룹 라이선스 할당의 결과로 사용자에게 이뤄진 라이선스 변경

>[!NOTE]
> 감사 로그는 포털의 Azure Active Directory 섹션에 있는 대부분의 블레이드에서 제공됩니다. 사용자가 액세스하는 위치에 따라 블레이드 컨텍스트와 관련된 작업을 표시하도록 필터를 미리 적용할 수 있습니다. 예상한 결과가 표시되지 않는 경우 [필터링 옵션](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs)을 검토하거나 [**Azure Active Directory > 작업 > 감사 로그**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)에서 필터링되지 않은 감사 로그에 액세스합니다.

### <a name="find-out-who-modified-a-group-license"></a>라이선스 그룹을 수정한 사용자 확인

1. **작업** 필터를 설정하여 *그룹 라이선스를 설정*하고 **적용**을 클릭합니다.
2. 결과는 그룹에서 설정 또는 수정된 모든 라이선스 사례를 포함합니다.
>[!TIP]
> *대상* 필터에서 그룹의 이름을 입력하여 결과 범위를 지정할 수도 있습니다.

3. 변경된 내용의 세부 정보를 보려면 목록 보기에서 항목을 클릭합니다. *수정된 속성* 아래에서 라이선스 할당에 대한 기존 값과 새 값이 모두 나열됩니다.

세부 정보를 포함한 최근 그룹 라이선스 변경의 예는 다음과 같습니다.

![스크린샷 그룹 라이선스 변경](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>그룹 변경이 처리를 시작하고 완료한 시점 확인

그룹에 대한 라이선스가 변경되면 Azure AD는 모든 사용자에게 변경 내용을 적용하기 시작합니다.

1. 그룹이 처리를 시작하는 시점을 표시하려면 **작업** 필터를 설정하여 *사용자에게 그룹 기반 라이선스를 적용하기 시작*합니다. 작업의 작업자는 *Microsoft Azure AD 그룹 기반 라이선스*이며 모든 그룹 라이선스 변경을 실행하는 데 사용되는 시스템 계정입니다.
>[!TIP]
> 목록에서 항목을 클릭하여 *속성 수정* 필드를 확인합니다. 여기서는 처리하기 위해 선택한 라이선스 변경 내용을 표시합니다. 그룹을 변경하고 처리할 항목이 확실하지 않은 경우에 유용합니다.

2. 마찬가지로 그룹이 처리를 완료하는 시점을 확인하려면 *사용자에게 그룹 기반 라이선스 적용 완료* 필터 값을 사용합니다.
>[!TIP]
> 이 경우에 *수정된 속성* 필드에는 결과의 요약이 포함됩니다. 처리로 인해 오류가 발생했는지 신속하게 확인하는 데 유용합니다. 샘플 출력:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. 모든 사용자의 변경 내용을 포함하여 그룹 처리 방법의 전체 로그를 확인하려면 다음 필터를 설정합니다.
  - **(작업자)에 의해 시작**: "Microsoft Azure AD 그룹 기반 라이선스"
  - **날짜 범위**(선택 사항): 특정 그룹이 처리를 시작 및 완료하는 시점을 알고 있는 경우에 범위를 사용자 지정합니다.

이 샘플 출력은 처리 시작, 모든 사용자 변경 내용 결과 및 처리 완료를 표시합니다.

![스크린샷 그룹 라이선스 변경](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> *사용자 라이선스 변경* 관련 항목을 클릭하면 개별 사용자에게 적용된 라이선스 변경에 대한 세부 정보가 표시됩니다.

## <a name="deleting-a-group-with-an-assigned-license"></a>할당된 라이선스가 있는 그룹 삭제

활성 라이선스가 할당된 그룹은 삭제할 수 없습니다. 관리자는 사용자로부터 라이선스가 제거된다는 것을 인식하지 못하는 그룹을 삭제할 수도 있습니다. 이러한 이유로 그룹을 삭제하려면 먼저 그룹에서 라이선스를 제거해야 합니다.

Azure Portal에서 그룹을 삭제하려고 하면 다음과 같은 오류 알림이 표시될 수 있습니다. ![그룹 삭제 실패 스크린샷](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

그룹의 **라이선스** 탭으로 이동하여 할당된 라이선스가 있는지 확인합니다. 그러한 경우 해당 라이선스를 제거하고 그룹을 다시 삭제합니다.

PowerShell 또는 Graph API를 통해 그룹을 삭제하려고 할 때 비슷한 오류가 표시될 수 있습니다. 온-프레미스에서 동기화된 그룹을 사용하는 경우 Azure AD에서 그룹을 삭제하지 못하면 Azure AD Connect에서 오류를 보고할 수도 있습니다. 이러한 모든 경우에는 그룹에 할당된 라이선스가 있는지 확인한 다음 먼저 이러한 라이선스를 제거합니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

그룹 기반 라이선스를 사용하는 경우 제한 사항 및 알려진 문제점에 대한 다음 목록을 잘 알고 있는 것이 좋습니다.

- 그룹 기반 라이선스는 현재 다른 그룹을 포함하는 그룹(중첩된 그룹)을 지원하지 않습니다. 중첩된 그룹에 라이선스를 적용하는 경우 그룹의 최상위 수준 사용자 멤버에게만 라이선스가 적용됩니다.

- 해당 기능은 보안 그룹과 함께 사용해야 합니다. Office 그룹은 현재 지원되지 않으며 라이선스 할당 프로세스에서 이러한 그룹을 사용할 수 없습니다.

- [Office 365 관리자 포털](https://portal.office.com )은 현재 그룹 기반 라이선스를 지원하지 않습니다. 사용자가 그룹에서 라이선스를 상속받는 경우 이 라이선스는 Office 관리 포털에 일반 사용자 라이선스로 표시됩니다. 해당 라이선스를 수정하거나 라이선스를 제거하려고 시도하는 경우 포털에서 오류 메시지를 반환합니다. 상속된 그룹 라이선스는 사용자가 직접 수정할 수 없습니다.

- 사용자가 그룹에서 제거되고 라이선스를 상실하는 경우 해당 라이선스(예: SharePoint Online)의 서비스 계획이 **일시 중단됨** 상태로 설정됩니다. 서비스 계획은 최종 사용 안 함 상태로 설정되지 않습니다. 이 예방 조치를 통해 관리자가 그룹 멤버 자격 관리에서 실수하는 경우 사용자 데이터를 실수로 제거하지 않도록 방지할 수 있습니다.

- 대규모 그룹(예: 100,000명의 사용자)에 대해 라이선스를 할당하거나 수정하면 성능에 영향을 줄 수 있습니다. 특히 Azure AD 자동화에서 생성된 변경 볼륨은 Azure AD와 온-프레미스 시스템 간의 디렉터리 동기화 성능에 부정적인 영향을 줄 수 있습니다.

- 로드가 높은 특정 경우에서 라이선스 처리는 지연될 수 있으며 그룹 라이선스 추가/제거 또는 그룹에서 사용자 추가/제거와 같은 변경 내용은 처리되는 데 시간이 오래 걸릴 수 있습니다. 변경 내용이 처리되는 데 24시간 이상이 걸리는 경우 조사를 허용하도록 [지원 티켓을 열어](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest)주세요. *일반 공급*에 도달하기 전에 이 기능의 성능 특징을 향상시킬 예정입니다.

- 라이선스 관리 자동화는 모든 유형의 환경 변경 사항에 자동으로 응답하지 않습니다. 예를 들어 라이선스가 부족하여 일부 사용자가 오류 상태에 있을 수 있습니다. 사용 가능한 사용자 수를 확보하기 위해 직접 할당된 일부 라이선스를 다른 사용자에게서 제거할 수 있습니다. 그러나 시스템은 이러한 변경에 자동으로 대응하지 않고 해당 오류 상태에 있는 사용자를 수정합니다.

  이러한 제한 사항을 해결하려면 Azure AD의 **그룹** 블레이드로 이동하여 **다시 처리**를 클릭하면 됩니다. 이 명령은 해당 그룹의 모든 사용자를 처리하고 가능한 경우 오류 상태를 해결합니다.

- 그룹 기반 라이선스는 Exchange Online에서 중복된 프록시 주소 구성으로 인해 사용자에게 라이선스 할당할 수 없는 경우 오류를 기록하지 않습니다. 이러한 사용자는 라이선스 할당 중에 건너뜁니다. 이 문제를 식별하고 해결하는 방법에 대한 자세한 내용은 [이 섹션](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online)을 참조하세요.

## <a name="next-steps"></a>다음 단계

그룹 기반 라이선스를 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)
