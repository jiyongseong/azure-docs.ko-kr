---
title: "Azure Security Center 자습서 - Azure Security Center로 리소스 보호 | Microsoft Docs"
description: "이 자습서는 Just-In-Time VM 액세스 정책과 응용 프로그램 제어 정책을 구성하는 방법을 보여 줍니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: cda204f5b54aef239cc0795b62c6fa484a27ebb5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>자습서: Azure Security Center로 리소스 보호
Security Center는 액세스 및 응용 프로그램 제어를 통해 악성 활동을 차단하여 위협에 대한 노출을 제한합니다. Just-In-Time VM(가상 머신) 액세스는 지속적인 VM 액세스를 거부할 수 있도록 하여 공격에 대한 노출을 줄입니다. 대신, 필요한 경우에만 VM에 대한 제어 및 감사된 액세스를 제공합니다. 적응형 응용 프로그램 제어는 VM에서 실행할 수 있는 응용 프로그램을 제어하여 맬웨어로부터 VM을 강화합니다. Security Center는 기계 학습을 통해 VM에서 실행 중인 프로세스를 분석하고 이러한 인텔리전스를 사용하여 허용 목록 규칙을 적용할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Just-In-Time VM 액세스 정책 구성
> * 응용 프로그램 제어 정책 구성

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
이 자습서에서 설명하는 기능을 단계별로 실행하려면 Security Center 표준 가격 책정 계층에 있어야 합니다. 처음 60일 동안 추가 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md) 빠른 시작을 통해 표준 계층으로 업그레이드하는 방법을 안내합니다.

## <a name="manage-vm-access"></a>VM 액세스 관리
Just-In-Time VM 액세스를 사용하면 Azure VM에 대한 인바운드 트래픽을 잠가 공격에 대한 노출을 줄이는 동시에 VM에 쉽게 액세스하여 필요할 때 연결할 수 있습니다.

Just-In-Time VM 액세스는 미리 보기로 제공됩니다.

관리 포트는 항상 열려 있을 필요가 없습니다. 예를 들어 관리 또는 유지 관리 작업을 수행하기 위해 VM에 연결되는 동안에만 열려 있어야 합니다. Just-In-Time을 사용하는 경우 Security Center는 관리 포트에 대한 액세스를 제한하는 NSG(네트워크 보안 그룹) 규칙을 사용하여 공격자의 대상이 되지 않도록 합니다.

1. Security Center 주 메뉴에서 **고급 클라우드 방어** 아래의 **Just-In-Time VM 액세스**를 선택합니다.

  ![Just-In-Time VM 액세스][1]

  **Just-In-Time VM 액세스**는 VM 상태에 대한 정보를 제공합니다.

  - **구성됨** - Just-In-Time VM 액세스를 지원하도록 구성된 VM입니다.
  - **권장** - Just-In-Time VM 액세스를 지원할 수 있지만 구성되지 않은 VM입니다.
  - **권장 사항 없음** - VM이 권장되지 않을 수 있는 이유입니다.

    - 누락된 NSG - Just-In-Time 솔루션을 사용하려면 NSG가 있어야 합니다.
    - 클래식 VM - Security Center Just-In-Time VM 액세스는 현재 Azure Resource Manager를 통해 배포된 VM만 지원합니다.
    - 기타 - Just-In-Time 솔루션이 구독 또는 리소스 그룹의 보안 정책에서 해제되거나 VM에 공용 IP가 없거나 NSG가 없는 경우 VM은 이 범주에 속합니다.

2. 권장 VM을 선택하고 **1 VM에서 JIT 사용**을 클릭하여 해당 VM에 대한 Just-In-Time 정책을 구성합니다.

  Security Center에서 권장하는 기본 포트를 저장하거나, Just-In-Time 솔루션을 사용하도록 설정할 새 포트를 추가 및 구성할 수 있습니다. 이 자습서에서는 **추가**를 선택하여 포트를 추가하겠습니다.

  ![포트 구성 추가][2]

3. **포트 구성 추가** 아래에서 다음을 식별합니다.

  - 포트
  - 프로토콜 유형
  - 허용된 원본 IP - 승인된 요청 시 액세스 권한을 얻도록 허용된 IP 범위
  - 최대 요청 시간 - 특정 포트를 열 수 있는 최대 기간

4. **확인**을 선택하여 저장합니다.

## <a name="harden-vms-against-malware"></a>맬웨어로부터 VM 강화
적응형 응용 프로그램 제어는 구성된 리소스 그룹에서 실행할 수 있는 응용 프로그램 집합을 정의하는 데 도움이 되며, 이로 인해 특히 맬웨어로부터 VM을 강화할 수 있는 혜택이 있습니다. Security Center는 기계 학습을 통해 VM에서 실행 중인 프로세스를 분석하고 이러한 인텔리전스를 사용하여 허용 목록 규칙을 적용할 수 있습니다.

적응형 응용 프로그램 제어는 미리 보기로 제공됩니다. 이 기능은 Windows 컴퓨터에서만 사용할 수 있습니다.

1. Security Center 주 메뉴로 돌아갑니다. **고급 클라우드 방어** 아래에서 **적응형 응용 프로그램 제어**를 선택합니다.

   ![적응 응용 프로그램 컨트롤][3]

  **리소스 그룹** 섹션에는 세 개의 탭이 있습니다.

  - **구성됨**: 응용 프로그램 제어로 구성된 VM을 포함하는 리소스 그룹 목록입니다.
  - **권장**: 응용 프로그램 제어가 권장되는 리소스 그룹 목록입니다.
  - **권장 사항 없음**: 응용 프로그램 제어 권장 사항이 없는 VM을 포함하는 리소스 그룹 목록입니다. 예를 들어 응용 프로그램이 항상 변경되어 안정적인 상태에 도달하지 않았던 VM이 있습니다.

2. **권장** 탭을 선택하여 응용 프로그램 제어 권장 사항이 있는 리소스 그룹 목록을 확인합니다.

  ![응용 프로그램 제어 권장 사항][4]

3. 리소스 그룹을 선택하여 **응용 프로그램 제어 규칙 만들기** 옵션을 엽니다. **VM 선택**에서 권장된 VM 목록을 검토하고 응용 프로그램 컨트롤을 적용하지 않으려는 것을 선택 취소합니다. **허용 목록 규칙에 대한 프로세스 선택**에서 권장된 응용 프로그램의 목록을 검토하고 적용하지 않으려는 것을 선택 취소합니다. 이 목록에는 다음과 같은 정보가 포함됩니다.

  - **이름**: 전체 응용 프로그램 경로
  - **프로세스**: 모든 경로 내에 있는 응용 프로그램 수
  - **일반**: "예"는 이러한 프로세스가 리소스 그룹에 있는 대부분의 VM에서 실행되었음을 나타냅니다.
  - **악용 가능**: 경고 아이콘은 응용 프로그램 허용 목록을 우회하기 위해 공격자가 사용할 수 응용 프로그램임을 나타냅니다. 승인 전에 이러한 응용 프로그램을 검토하는 것이 좋습니다.

4. 선택이 완료되면 **만들기**를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리
이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작과 자습서를 계속 사용하려면 표준 계층을 계속 실행하고 자동 프로비저닝을 설정된 상태로 유지합니다. 계속하지 않거나 체험 계층으로 되돌리려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **보안 정책**을 선택합니다.
2. 체험 계층으로 되돌리려는 구독 또는 정책을 선택합니다. **보안 정책**이 열립니다.
3. **정책 구성 요소** 아래에서 **가격 책정 계층**을 선택합니다.
4. **체험**을 선택하여 표준 계층에서 체험 계층으로 구독을 변경합니다.
5. **저장**을 선택합니다.

자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **보안 정책**을 선택합니다.
2. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 선택합니다.
3. **보안 정책 - 데이터 수집**에서 **온보딩** 아래의 **해제**를 선택하여 자동 프로비저닝을 사용하지 않도록 설정합니다.
4. **저장**을 선택합니다.

>[!NOTE]
> 자동 프로비저닝을 해제해도 Microsoft Monitoring Agent가 프로비전된 Azure VM에서 Agent가 제거되지는 않습니다. 자동 프로비저닝을 사용하지 않도록 설정하면 리소스에 대한 보안 모니터링이 제한됩니다.
>

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음을 통해 위협에 대한 노출을 제한하는 방법을 살펴보았습니다.

> [!div class="checklist"]
> * Just-In-Time VM 액세스 정책을 구성하여 필요할 때만 VM에 대한 제어 및 감사된 액세스 제공
> * 적응형 응용 프로그램을 구성하여 VM에서 실행할 수 있는 응용 프로그램 제어

보안 인시던트에 대응하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 보안 인시던트에 대응](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
