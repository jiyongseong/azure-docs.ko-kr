---
title: Azure Security Center에서 Just-In-Time 가상 머신 액세스 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 Just-In-Time VM 액세스가 Azure 가상 머신에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 보여 줍니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2018
ms.author: terrylan
ms.openlocfilehash: e293f085eb6c4c90b26ac6035d50d74f5cdd7269
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366891"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Just-In-Time를 사용하여 가상 머신 액세스 관리

Just-In-Time VM(가상 컴퓨터) 액세스를 사용하면 Azure VM으로의 인바운드 트래픽을 잠글 수 있어 필요할 때 VM 연결을 위한 간편한 액세스를 제공하면서도 공격에 대한 노출을 줄일 수 있습니다.

> [!NOTE]
> Just-In-Time 기능은 Security Center의 표준 계층에서 사용할 수 있습니다.  Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
>
>

## <a name="attack-scenario"></a>공격 시나리오

무차별 암호 대입 공격(brute force attack)은 일반적으로 VM에 대한 액세스 권한을 얻는 방법으로 관리 포트를 대상으로 합니다. 성공하면 공격자가 VM을 제어할 수 있으며 사용자 환경으로의 발판을 마련하게 됩니다.

무차별 암호 대입 공격을 줄이기 위한 한 가지 방법은 포트가 열려 있는 시간의 양을 제한하는 것입니다. 관리 포트는 항상 열려 있을 필요가 없습니다. 예를 들어 관리 또는 유지 관리 작업을 수행하기 위해 VM에 연결되는 동안에만 열려 있어야 합니다. Just-In-Time을 사용하는 경우 Security Center는 관리 포트에 대한 액세스를 제한하는 [NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md#security-rules) 규칙을 사용하여 공격자의 대상이 되지 않도록 합니다.

![Just-In-Time 시나리오][1]

## <a name="how-does-just-in-time-access-work"></a>Just-In-Time 액세스는 어떻게 작동하나요?

Just-In-Time이 활성화되면 Security Center는 NSG 규칙을 만들어 Azure VM으로의 인바운드 트래픽을 잠급니다. VM에서 인바운드 트래픽을 잠글 포트를 선택합니다. 이러한 포트는 Just-In-Time 솔루션에 의해 제어됩니다.

사용자가 VM에 액세스를 요청하면 Security Center는 해당 사용자에게 VM에 대한 쓰기 액세스 권한을 제공하는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 권한이 있는지 확인합니다. 쓰기 권한이 있는 경우 요청이 승인되고 Security Center가 지정된 시간 동안 선택된 포트로의 인바운드 트래픽을 허용하도록 NSG(네트워크 보안 그룹)을 자동으로 구성합니다. 시간이 만료되면 Security Center에서 NSG를 이전 상태로 복원합니다.

> [!NOTE]
> Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다. 클래식 및 Resource Manager 배포 모델에 대한 자세한 내용은 [Azure Resource Manager 및 클래식 배포](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요.
>
>

## <a name="using-just-in-time-access"></a>Just-In-Time 액세스 사용

1. **Security Center** 대시보드를 엽니다.

2. 왼쪽 창에서 **Just-In-Time VM 액세스**를 선택합니다.

![Just-In-Time VM 액세스 타일][2]

**Just-In-Time VM 액세스** 창이 열립니다.

![Just-In-Time VM 액세스 타일][10]

**Just-In-Time VM 액세스**는 VM 상태에 대한 정보를 제공합니다.

- **구성됨** - Just-In-Time VM 액세스를 지원하도록 구성된 VM입니다. 표시된 데이터는 지난 주에 대한 데이터이며 각 VM에 대해 승인된 요청 수, 마지막 액세스 날짜 및 시간, 마지막 사용자를 포함합니다.
- **권장** - Just-In-Time VM 액세스를 지원할 수 있지만 구성되지 않은 VM입니다. 이러한 VM에는 Just-In-Time VM 액세스 제어를 사용하도록 설정하는 것이 좋습니다. [Just-In-Time 액세스 정책 구성](#configuring-a-just-in-time-access-policy)을 참조하세요.
- **권장 사항 없음** - VM이 권장되지 않을 수 있는 이유입니다.
  - 누락된 NSG - Just-In-Time 솔루션을 사용하려면 NSG가 있어야 합니다.
  - 클래식 VM - Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다. 클래식 배포는 Just-In-Time 솔루션에서 지원되지 않습니다.
  - 기타 - Just-In-Time 솔루션이 구독 또는 리소스 그룹의 보안 정책에서 해제되거나 VM에 공용 IP가 없거나 NSG가 없는 경우 VM은 이 범주에 속합니다.

## <a name="configuring-a-just-in-time-access-policy"></a>Just-In-Time 액세스 정책 구성

사용하도록 설정할 VM을 선택하려면

1. **Just-In-Time VM 액세스**에서 **권장** 탭을 선택합니다.

  ![Just-In-Time 액세스 사용][3]

2. **가상 머신**에서 사용하도록 설정할 VM을 선택합니다. 그러면 VM 옆에 있는 확인 표시가 선택됩니다.
3. **VM에서 JIT 사용**을 선택합니다.
4. **저장**을 선택합니다.

### <a name="default-ports"></a>기본 포트

Security Center에서 Just-In-Time 사용을 권장하는 기본 포트를 확인할 수 있습니다.

1. **Just-In-Time VM 액세스**에서 **권장** 탭을 선택합니다.

  ![기본 포트 표시][6]

2. **VM** 아래에서 VM을 선택합니다. 그러면 VM 옆에 있는 확인 표시가 선택되고 **JIT VM 액세스 구성**이 열립니다. 이 블레이드에 기본 포트가 표시됩니다.

### <a name="add-ports"></a>포트 추가

**JIT VM 액세스 구성**에서 Just-In-Time 솔루션을 사용하려는 새 포트를 추가 및 구성할 수 있습니다.

1. **JIT VM 액세스 구성**에서 **추가**를 선택합니다. 그러면 **포트 추가 구성**이 열립니다.

  ![포트 구성][7]

2. **포트 추가 구성**에서 포트, 프로토콜 유형, 허용되는 원본 IP 및 최대 요청 시간을 확인합니다.

  허용되는 원본 IP는 승인된 요청 시 액세스 권한을 얻도록 허용된 IP 범위입니다.

  최대 요청 시간은 특정 포트를 열 수 있는 최대 기간입니다.

3. **확인**을 선택합니다.

## <a name="requesting-access-to-a-vm"></a>VM에 대한 액세스 요청

VM에 대한 액세스를 요청하려면

1. **Just-In-Time VM 액세스**에서 **구성됨** 탭을 선택합니다.
2. **VM** 아래에서 액세스가 가능하도록 할 VM을 선택합니다. 그러면 VM 옆에 있는 확인 표시가 선택됩니다.
3. **액세스 요청**을 선택합니다. 이렇게 하면 **액세스 요청**이 열립니다.

  ![VM에 대한 액세스 요청][4]

4. **액세스 요청**에서 각 VM에 대해, 열어 둘 포트, 포트가 열려 있는 원본 IP 및 포트를 열어 둘 기간을 구성합니다. Just-In-Time 정책에 구성된 포트에만 액세스를 요청할 수 있습니다. 각 포트에는 Just-In-Time 정책에서 파생된 최대 허용된 시간이 있습니다.
5. **포트 열기**를 선택합니다.

> [!NOTE]
> 사용자가 VM에 액세스를 요청하면 Security Center는 해당 사용자에게 VM에 대한 쓰기 액세스 권한을 제공하는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 권한이 있는지 확인합니다. 쓰기 권한이 있는 경우 요청이 승인됩니다.
>
>

> [!NOTE]
> 액세스를 요청하는 사용자가 프록시 뒤에 있으면 "내 IP" 옵션이 작동하지 않습니다. 조직의 전체 범위를 정의해야 할 수 있습니다.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Just-In-Time 액세스 정책 편집

해당 VM에 대해 열 새 포트를 추가 및 구성하거나 이미 보호된 포트와 관련된 다른 매개 변수를 변경하여 VM의 기존 Just-In-Time 정책을 변경할 수 있습니다.

VM의 Just-In-Time 정책을 편집하기 위해 **구성됨** 탭이 사용됩니다.

1. **VM** 아래에서 해당 VM에 대한 행 안의 3개 점을 클릭하여 포트를 추가할 VM을 선택합니다. 그러면 메뉴가 열립니다.
2. 메뉴에서 **편집**을 선택합니다. 그러면 **JIT VM 액세스 구성**이 열립니다.

  ![정책 편집][8]

3. **JIT VM 액세스 구성**에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 **추가**를 선택할 수 있습니다. 그러면 **포트 추가 구성**이 열립니다.

  ![포트 추가][7]

4. **포트 추가 구성**에서 포트, 프로토콜 유형, 허용되는 원본 IP 및 최대 요청 시간을 확인합니다.
5. **확인**을 선택합니다.
6. **저장**을 선택합니다.

## <a name="auditing-just-in-time-access-activity"></a>Just-In-Time 액세스 활동 감사

로그 검색을 사용하여 VM 활동에 대한 정보를 얻을 수 있습니다. 로그를 보려면

1. **Just-In-Time VM 액세스**에서 **구성됨** 탭을 선택합니다.
2. **VM** 아래에서 해당 VM에 대한 행 안의 3개 점을 클릭하여 정보를 확인합니다. 그러면 메뉴가 열립니다.
3. 메뉴에서 **활동 로그**를 선택합니다. 이렇게 하면 **활동 로그**가 열립니다.

  ![활동 로그 선택][9]

  **활동 로그**는 시간, 날짜 및 구독과 함께 해당 VM에 대한 이전 작업의 필터링된 보기를 제공합니다.

  ![활동 로그 보기][5]

**여기를 클릭하여 모든 항목을 CSV로 다운로드하세요.** 를 선택하여 로그 정보를 다운로드할 수 있습니다.

필터를 수정하고 **적용**을 선택하여 검색 및 로그를 만듭니다.

## <a name="using-just-in-time-vm-access-via-powershell"></a>PowerShell을 통해 Just-In-Time VM 액세스 사용

PowerShell을 통해 Just-In-Time 솔루션을 사용하려면 [최신](/powershell/azure/install-azurerm-ps) 버전의 Azure PowerShell 버전이 있어야 합니다.
그러면 PowerShell 갤러리에서 [최신](https://aka.ms/asc-psgallery) Azure Security Center를 설치해야 합니다.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>VM에 대한 Just-In-Time 정책 구성

특정 VM에서 Just-In-Time 정책을 구성하려면 PowerShell 세션에서 Set-ASCJITAccessPolicy 명령을 실행해야 합니다.
cmdlet 설명서에서 자세히 알아보세요.

### <a name="requesting-access-to-a-vm"></a>VM에 대한 액세스 요청

Just-In-Time 솔루션으로 보호되는 특정 VM에 액세스하려면 PowerShell 세션에서 Invoke-ASCJITAccess를 실행해야 합니다.
cmdlet 설명서에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center에서 Just-In-Time VM 액세스가 Azure 가상 머신에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 알아보았습니다.

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
- [보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
- [보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
- [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
- [파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
- [Azure 보안 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
