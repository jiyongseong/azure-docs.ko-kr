---
title: "Azure Security Center 빠른 시작 - Security Center 표준에 Azure 구독 온보딩 | Microsoft Docs"
description: "이 빠른 시작에서는 보안 강화를 위해 Security Center의 표준 가격 책정 계층으로 업그레이드하는 방법을 보여 줍니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 0004db332ec13e23ed49f2c19e3454a516ca6a40
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>빠른 시작: Security Center 표준에 Azure 구독 온보딩
Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 위협 방지 기능을 제공합니다. 체험 계층은 Azure 리소스에 대한 제한된 보안만 제공하지만 표준 계층은 이러한 기능을 온-프레미스 및 기타 클라우드로 확장합니다. Security Center 표준을 사용하면 보안 취약성을 찾아서 수정하고, 액세스 및 응용 프로그램 제어를 적용하여 악성 활동을 차단하고, 분석 및 인텔리전스를 사용하여 위협을 검색하고, 공격을 받을 때 신속하게 대응할 수 있습니다. 처음 60일 동안 추가 비용 없이 Security Center 표준을 사용해 볼 수 있습니다.

이 문서에서는 보안 강화를 위해 표준 계층으로 업그레이드하고 가상 머신에 Microsoft Monitoring Agent를 설치하여 보안 취약성과 위협을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

구독을 표준 계층으로 업그레이드하려면 구독 소유자, 구독 참가자 또는 보안 관리자의 역할이 할당되어야 합니다.

## <a name="enable-your-azure-subscription"></a>Azure 구독 사용

1. [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.
2. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

 ![Security Center 개요][2]

**Security Center - 개요**에서는 하이브리드 클라우드 워크로드의 보안 태세를 총체적으로 확인하여 워크로드의 보안을 검색 및 평가하고 위험을 파악 및 완화할 수 있습니다. Security Center는 이전에 사용자 또는 다른 구독 사용자가 체험 계층에 온보딩하지 않은 Azure 구독을 자동으로 사용하도록 설정합니다.

**구독** 메뉴 항목을 클릭하여 구독 목록을 보고 필터링할 수 있습니다. 이제 Security Center에서 이러한 구독의 보안 평가를 시작하여 보안 취약성을 식별합니다. 평가 유형을 사용자 지정하기 위해 보안 정책을 수정할 수 있습니다. 보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다.

Security Center를 처음 시작하면 수분 내에 다음이 표시될 수 있습니다.

- **권장 사항** - Azure 구독의 보안을 향상하는 방법을 제공합니다. **권장 사항** 타일을 클릭하면 우선 순위가 지정된 목록이 시작됩니다.
- 현재 Security Center에서 평가 중인 **계산**, **네트워킹**, **저장소 및 데이터**, **응용 프로그램** 리소스 인벤토리 및 각 리소스의 보안 상태입니다.

Security Center를 최대한 활용하려면 아래 단계를 완료하여 표준 계층으로 업그레이드하고 Microsoft Monitoring Agent를 설치해야 합니다.

## <a name="upgrade-to-the-standard-tier"></a>표준 계층으로 업그레이드
Security Center 빠른 시작 및 자습서를 위해 표준 계층으로 업그레이드해야 합니다. 처음 60일은 체험이며, 언제든지 체험 계층으로 되돌릴 수 있습니다.

1. Security Center 주 메뉴에서 **고급 보안으로 온보딩**을 선택합니다.

2. Security Center의 **고급 보안으로 온보딩** 아래에 온보딩할 수 있는 구독 및 작업 영역이 나열됩니다. 목록에서 구독을 선택합니다.

  ![구독 선택][4]

3. 구독에 포함된 리소스 그룹에 대한 정보가 **보안 정책**에 제공됩니다. **가격 책정**도 열립니다.
4. **가격 책정** 아래에서 **표준**을 선택하여 체험에서 표준으로 업그레이드하고 **저장**을 클릭합니다.

  ![표준 선택][5]

이제 표준 계층으로 업그레이드했으므로 **적응형 응용 프로그램 제어**, **Just-In-Time VM 액세스**, **보안 경고**, **위협 인텔리전스**, **자동화 플레이북** 등을 비롯한 추가 Security Center 기능에 액세스할 수 있습니다. 보안 경고는 Security Center가 악성 활동을 감지하는 경우에만 나타납니다.

  ![보안 경고][7]

## <a name="automate-data-collection"></a>데이터 수집 자동화
Security Center는 Azure VM 및 비 Azure 컴퓨터에서 데이터를 수집하여 보안 취약성과 위협을 모니터링합니다. Microsoft Monitoring Agent를 사용하여 데이터를 수집합니다. Microsoft Monitoring Agent는 컴퓨터에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사합니다. 기본적으로 Security Center는 새 작업 영역을 만듭니다.

자동 프로비저닝을 사용하는 경우 Security Center에서 지원되는 모든 Azure VM 및 새로 만든 Azure VM에 Microsoft Monitoring Agent를 설치합니다. 자동 프로비저닝을 사용하는 것이 좋습니다.

Microsoft Monitoring Agent의 자동 프로비저닝을 사용하도록 설정하려면 다음을 수행합니다.

1. Security Center 주 메뉴 아래에서 **보안 정책**을 선택합니다.
2. 구독을 선택합니다.
3. **보안 정책** 아래에서 **데이터 수집**을 선택합니다.
4. **데이터 수집** 아래에서 **설정**을 선택하여 자동 프로비저닝을 사용하도록 설정합니다.
5. **저장**을 선택합니다.

  ![자동 프로비저닝 사용][6]

Azure VM에 대한 이 새로운 정보를 활용하여 Security Center는 시스템 업데이트 상태, OS 보안 구성, 끝점 보호와 관련된 추가 권장 사항을 제공하고 추가 보안 경고를 생성할 수 있습니다.

  ![권장 사항][8]

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
이 빠른 시작에서는 표준 계층으로 업그레이드하고 하이브리드 클라우드 워크로드에서 통합된 보안 관리 및 위협 방지를 위해 Microsoft Monitoring Agent를 프로비전했습니다. Security Center 사용 방법을 자세히 알아보려면 온-프레미스 및 다른 클라우드에 있는 Windows 컴퓨터를 온보딩하기 위한 빠른 시작을 계속 진행하세요.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Security Center에 Windows 컴퓨터 온보딩](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
