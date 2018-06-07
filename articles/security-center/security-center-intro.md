---
title: "Azure Security Center란? | Microsoft Docs"
description: "Azure Security Center, 주요 기능 및 작동 방법에 대해 알아봅니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-security-center"></a>Azure Security Center란?
Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 제공합니다. Security Center를 사용하여 워크로드에 보안 정책을 적용하고, 위협에 대한 노출을 제한하고, 공격을 검색 및 대응할 수 있습니다.

보안 센터를 사용해야 하는 이유

- **중앙 집중식 정책 관리** – 모든 하이브리드 클라우드 작업에 걸쳐 보안 정책을 중앙에서 관리하여 회사 또는 규정 보안 요구 사항을 준수할 수 있습니다.
- **연속적인 보안 평가** – 컴퓨터, 네트워크, 저장소 및 데이터 서비스, 응용 프로그램의 보안을 모니터링하여 잠재적인 보안 문제를 찾아낼 수 있습니다.
- **실행 가능한 권장 지침** – 우선 순위가 지정된 실행 가능한 보안 권장 지침을 사용하여 공격자들이 악용하기 전에 보안 취약성을 수정할 수 있습니다.
- **고급 클라우드 방어** – 관리 포트 및 허용 목록에 대한 Just-In-Time 액세스를 통해 VM에서 실행되는 응용 프로그램을 제어함으로써 위협을 줄일 수 있습니다.
- **경고 및 인시던트 우선 순위 지정** - 우선 순위가 지정된 보안 경고와 인시던트를 통해 가장 중요한 위협부터 중점적으로 확인할 수 있습니다.
- **통합 보안 솔루션** - 연결된 파트너 솔루션을 비롯한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.

**Security Center - 개요**에서는 Azure 및 비 Azure 작업의 보안 태세를 빠르게 확인하여 작업의 보안을 검색 및 평가하고 위험을 파악 및 완화할 수 있습니다. 기본 제공 대시보드는 주의가 필요한 보안 경고 및 취약성에 대한 즉각적인 정보를 제공합니다.

![개요][1]

## <a name="centralized-policy-management"></a>중앙 집중식 정책 관리
보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Security Center에서 정책을 정의하고 워크로드의 유형 또는 데이터의 민감도에 맞게 조정합니다.

Security Center 정책에는 다음 구성 요소가 포함되어 있습니다.

- **데이터 컬렉션**: 에이전트 프로비전 및 보안 [데이터 컬렉션](security-center-enable-data-collection.md) 설정을 결정합니다.
- **보안 정책**: [보안 정책](security-center-policies.md)을 편집하여 Security Center에서 모니터링 및 권장하는 컨트롤을 결정합니다.
- **전자 메일 알림**: 보안 연락처 및 [전자 메일 알림](security-center-provide-security-contact-details.md) 설정을 결정합니다.
- **가격 책정 계층**: 무료 또는 표준 [가격 선택](security-center-pricing.md)을 정의합니다. 선택한 계층에 따라 범위의 리소스에 대해 사용할 수 있는 Security Center 기능이 결정됩니다.

![보안 정책][2]

자세한 내용은 [보안 정책 개요](security-center-policies-overview.md)를 참조하세요.

## <a name="continuous-security-assessment"></a>지속적인 보안 평가
Security Center는 계산 리소스, 가상 네트워크, 저장소 및 데이터 서비스, 응용 프로그램의 보안 상태를 분석합니다. 지속적인 평가를 통해 보안 업데이트가 누락된 시스템이나 노출된 네트워크 포트와 같은 잠재적인 보안 문제를 찾아낼 수 있습니다. 방지 섹션에서 타일을 선택하여 리소스 및 식별된 취약성 목록을 비롯한 자세한 정보를 봅니다.

![보안 상태 모니터링][3]

자세한 내용은 [보안 상태 모니터링](security-center-monitoring.md)을 참조하세요.

## <a name="actionable-recommendations"></a>실행 가능한 권장 사항
Security Center는 Azure 및 비 Azure 리소스의 보안 상태를 분석하여 잠재적인 보안 취약성을 식별합니다. 우선 순위가 지정된 보안 권장 사항 목록은 보안 문제 해결 과정을 안내합니다.

![권장 사항][4]

자세한 내용은 [보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.

## <a name="just-in-time-vm-access"></a>Just-In-Time VM 액세스
Azure VM의 관리 포트에 대한 제어되는 Just-In-Time 액세스를 사용하여 네트워크 공격에 대한 취약성을 낮춤으로써 무차별 암호 대입 공격(brute force attack) 및 기타 네트워크 공격에 대한 노출을 크게 줄일 수 있습니다.

![Just-In-Time VM 액세스][5]

사용자가 가상 머신에 연결할 수 있는 방법에 대한 규칙을 지정합니다. 필요한 경우 Security Center에서 또는 PowerShell을 통해 액세스를 요청할 수 있습니다. 요청이 규칙에 부합되기만 하면, 요청된 시간 동안 액세스 권한이 자동으로 부여됩니다.

자세한 내용은 [Just-In-Time를 사용하여 가상 머신 액세스 관리](security-center-just-in-time.md)를 참조하세요.

## <a name="adaptive-application-controls"></a>적응 응용 프로그램 컨트롤
Machine Learning을 통해 제공되며 특정 Azure 작업에 맞게 조정되는 허용 목록 권장 사항을 적용하여 맬웨어 및 기타 원치 않는 응용 프로그램을 차단할 수 있습니다.

![적응 응용 프로그램 컨트롤][6]

Security Center에서 생성된 권장되는 응용 프로그램 허용 목록 규칙을 검토한 후 클릭하여 적용하거나 이미 구성된 규칙을 편집합니다.

자세한 내용은 [적응형 응용 프로그램 제어](security-center-adaptive-application.md)를 참조하세요.

## <a name="prioritized-alerts-and-incidents"></a>우선 순위가 지정된 경고 및 인시던트
Security Center에서는 고급 분석 및 전역 위협 인텔리전스를 사용하여 들어오는 공격 및 위반 후 활동을 감지합니다. 경고는 우선 순위가 지정된 후 인시던트로 그룹화되므로, 가장 중요한 위협에 우선적으로 집중할 수 있습니다. 사용자 지정 보안 경고를 만들 수도 있습니다.

![우선 순위가 지정된 경고 및 인시던트][7]

시각적, 대화형 조사 경험을 통해 공격의 범위 및 영향을 빠르게 평가하고, 미리 정의된 쿼리나 임시 쿼리를 통해 보안 데이터를 보다 상세하게 탐색할 수 있습니다.

자세한 내용은 [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.

## <a name="integrate-your-security-solutions"></a>보안 솔루션 통합
Security Center에서 네트워크 방화벽과 같은 연결된 파트너 솔루션 및 기타 Microsoft 서비스를 비롯한 여러 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.

![보안 솔루션 통합][8]

자세한 내용은 [보안 솔루션 통합](security-center-partner-integration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 보안 센터를 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- Security Center의 무료 가격 책정 계층은 Azure 구독을 사용하여 사용하도록 설정됩니다. 고급 보안 관리 및 위협 검색 기능을 활용하려면 표준 가격 책정 계층으로 업그레이드해야 합니다. 표준 계층은 처음 60일 동안 무료입니다. 자세한 내용은 [Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.
- 지금 Security Center 표준을 설정할 준비가 된 경우 [빠른 시작: Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md)의 지침을 따릅니다.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
