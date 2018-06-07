---
title: Azure Security Center 자습서 - 보안 정책 정의 및 평가 | Microsoft Docs
description: Azure Security Center 자습서 - 보안 정책 정의 및 평가
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: yurid
ms.openlocfilehash: 16dc8553fdc1209d1973934a87660ff61df8e68a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-define-and-assess-security-policies"></a>자습서: 보안 정책 정의 및 평가
Security Center는 보안 정책을 사용하여 원하는 작업 구성을 정의함으로써 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Azure 구독에 대한 정책을 정의하고 이 정책을 작업 유형 또는 데이터의 민감도에 적용하면, Security Center에서 계산, 네트워킹, SQL/저장소 및 응용 프로그램 리소스에 대한 보안 권장 사항을 제공할 수 있습니다. 이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 보안 정책 구성
> * 리소스 보안 평가

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
이 자습서에서 설명하는 기능을 단계별로 실행하려면 Security Center 표준 가격 책정 계층에 있어야 합니다. 처음 60일 동안 추가 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md) 빠른 시작을 통해 표준 계층으로 업그레이드하는 방법을 안내합니다.

## <a name="configure-security-policy"></a>보안 정책 구성
Security Center는 각 Azure 구독에 대한 기본 보안 정책을 자동으로 만듭니다. 보안 정책은 해당 구독의 보안 요구 사항에 따라 설정하거나 해제할 수 있는 권장 사항으로 구성됩니다. 기본 보안 정책을 변경하려면 구독의 소유자, 참가자 또는 보안 관리자여야 합니다.

1. Security Center 주 메뉴에서 **보안 정책**을 선택합니다. 사용할 구독을 선택합니다. **정책 구성 요소** 아래에서 **보안 정책**을 선택합니다.

  ![보안 정책](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

2. 모니터링하려는 각 보안 구성에 대해 **설정**을 선택합니다. Security Center에서는 사용자 환경의 구성을 지속적으로 평가하고, 취약성이 존재하면 보안 권장 사항을 생성합니다. 보안 구성이 권장되지 않거나 관련이 없는 경우 **해제**를 선택합니다. 예를 들어 개발/테스트 환경에서는 프로덕션 환경과 동일한 보안 수준이 필요하지 않을 수도 있습니다. 사용자 환경에 적용할 수 있는 정책을 선택한 후 **저장**을 클릭합니다.

Security Center에서 이러한 정책을 처리하고 권장 사항을 생성할 때까지 기다립니다. 시스템 업데이트 및 OS 구성과 같은 일부 구성에는 최대 12시간이 걸릴 수 있지만, 네트워크 보안 그룹 및 암호화 구성은 거의 즉시 평가할 수 있습니다. Security Center 대시보드에 권장 사항이 표시되면 다음 단계를 진행할 수 있습니다.

## <a name="assess-security-of-resources"></a>리소스 보안 평가
1. 사용하도록 설정된 보안 정책에 따라 Security Center에서 필요에 따라 일단의 보안 권장 사항을 제공합니다. 가상 머신 및 컴퓨터 권장 사항을 검토함으로써 시작합니다. Security Center 대시보드에서 **개요**를 클릭하고 **계산**을 클릭합니다.

  ![컴퓨팅](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  권장 사항 우선 순위를 빨간색(높은 우선 순위)으로 지정하여 각 권장 사항을 검토합니다. 이러한 권장 사항 중 일부에는 [엔드포인트 보호 문제](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection)와 같이 Security Center에서 직접 구현할 수 있는 수정이 있습니다. 다른 권장 사항에는 누락된 디스크 암호화 권장 사항과 같은 수정을 적용하기 위한 지침만 있습니다.

2. 모든 관련 계산 권장 사항이 처리되면 다음 작업인 '네트워킹'으로 이동해야 합니다. Security Center 대시보드에서 **개요**를 클릭하고 **네트워킹**을 클릭합니다.

  ![네트워킹](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  네트워킹 권장 사항 페이지에는 네트워크 구성, 인터넷 연결 엔드포인트 및 네트워크 토폴로지에 대한 보안 문제 목록이 있습니다. **계산**과 마찬가지로 일부 네트워킹 권장 사항에서 통합된 수정을 제공하고, 다른 일부는 제공하지 않습니다.

3. 모든 관련 네트워킹 권장 사항이 처리되면 다음 작업인 '저장소 및 데이터'로 이동해야 합니다. Security Center 대시보드에서 **개요**를 클릭하고 **저장소 및 데이터**를 클릭합니다.

  ![데이터 리소스](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  **데이터 리소스** 페이지에는 Azure SQL 서버 및 데이터베이스에 대한 감사 사용 설정, SQL 데이터베이스에 대한 암호화 사용 설정 및 Azure 저장소 계정 암호화 사용 설정에 대한 권장 사항이 포함되어 있습니다. 이러한 작업이 없는 경우 권장 사항이 표시되지 않습니다. **계산**과 마찬가지로 일부 SQL 및 저장소 권장 사항에서 통합된 수정을 제공하고, 다른 일부는 제공하지 않습니다.

4. 모든 관련 SQL 및 저장소 권장 사항이 처리되면 다음 작업인 '응용 프로그램'으로 이동해야 합니다. Security Center 대시보드에서 **개요**를 클릭하고 **응용 프로그램**을 클릭합니다.

  ![응용 프로그램](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  **응용 프로그램** 페이지에는 웹 응용 프로그램 방화벽 배포에 대한 권장 사항 및 응용 프로그램을 강화하기 위한 일반 지침이 포함되어 있습니다. IIS(인터넷 정보 서비스)에서 실행되는 웹 응용 프로그램이 있는 가상 머신 또는 컴퓨터가 없는 경우 이러한 권장 사항이 표시되지 않습니다.

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
이 자습서에서는 Security Center를 사용하여 다음과 같은 작업의 기본 정책 정의 및 보안 평가에 대해 알아보았습니다.

> [!div class="checklist"]
> * 회사 또는 규정 보안 요구 사항을 준수하도록 보장하는 보안 정책 구성
> * 계산, 네트워킹, SQL/저장소 및 응용 프로그램 리소스에 대한 보안 평가

Security Center를 사용하여 리소스를 보호하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [리소스 보호](tutorial-protect-resources.md)
