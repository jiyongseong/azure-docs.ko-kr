---
title: Azure Automation 계정 관리
description: 이 문서에서는 인증서 갱신, 삭제 및 잘못된 구성과 같은 Automation 계정의 구성을 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be1b35d2e7dc3d3e2efab825f318983e2943b0d2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-azure-automation-account"></a>Azure Automation 계정 관리
Automation 계정 만료되기 전인 어떤 시점에서 인증서를 갱신해야 합니다. 실행 계정이 손상되었다고 생각하시면 삭제하고 다시 만들 수 있습니다. 이 섹션에서는 이러한 작업을 수행하는 방법을 설명합니다.

## <a name="self-signed-certificate-renewal"></a>자체 서명된 인증서 갱신
실행 계정에 만든 자체 서명된 인증서는 생성 날짜로부터 1년이 되는 날에 만료됩니다. 만료되기 전에 언제든지 갱신할 수 있습니다. 인증서를 갱신하면 큐에 대기하거나 활발하게 실행 중이며 실행 계정으로 인증되는 모든 Runbook이 부정적인 영향을 받지 않도록 현재 유효한 인증서가 보존됩니다. 인증서는 만료 날짜까지 유효합니다.

> [!NOTE]
> 엔터프라이즈 인증 기관에서 발급한 인증서를 사용하도록 Automation 실행 계정을 구성하고 이 옵션을 사용하는 경우 엔터프라이즈 인증서는 자체 서명된 인증서로 교체됩니다.

인증서를 갱신하려면 다음을 수행합니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. **Automation 계정**의 
3. **계정 속성** 창에서 **계정 설정** 아래에 있는 **실행 계정**을 선택합니다.

    ![Automation 계정 속성 창](media/automation-manage-account/automation-account-properties-pane.png)
3. **실행 계정** 속성 페이지에서 인증서를 갱신하려는 실행 계정 또는 클래식 실행 계정을 선택합니다.

4. 선택한 계정의 **속성** 창에서 **인증서 갱신**을 클릭합니다.

    ![실행 계정용 인증서 갱신](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. 인증서가 갱신되는 동안 메뉴의 **알림**에서 진행률을 추적할 수 있습니다.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>실행 또는 클래식 실행 계정 삭제
이 섹션에서는 실행 또는 클래식 실행 계정을 삭제하고 다시 만드는 방법을 설명합니다. 이 작업을 실행하는 경우 Automation 계정이 보존됩니다. 실행 또는 클래식 실행 계정을 삭제한 후에 Azure Portal에서 계정을 다시 만들 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. **Automation 계정** 페이지에서 **실행 계정**을 선택합니다.

3. **실행 계정** 속성 페이지에서 삭제하려는 실행 계정 또는 클래식 실행 계정을 선택합니다. 그런 다음 선택한 계정의 **속성** 창에서 **삭제**를 클릭합니다.

 ![실행 계정 삭제](media/automation-manage-account/automation-account-delete-runas.png)

4. 계정이 삭제되는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

5. 계정이 삭제되면 **실행 계정** 속성 페이지에서 **Azure 실행 계정** 만들기 옵션을 선택하여 계정을 다시 만들 수 있습니다.

 ![Automation 실행 계정 다시 만들기](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>잘못된 구성
실행 또는 클래식 실행 계정이 제대로 작동하는 데 필요한 일부 구성 항목이 초기 설정 중에 제대로 삭제되거나 생성되지 않았습니다. 항목은 다음과 같습니다.

* 인증서 자산
* 연결 자산
* 실행 계정이 참여자 역할에서 제거됨
* Azure AD의 서비스 주체 또는 응용 프로그램

잘못된 구성의 이전 및 다른 인스턴스에서 Automation 계정은 변경 사항을 감지하고 계정의 **실행 계정** 속성 창에서 *불완전*이라는 상태를 표시합니다.

![불완전 실행 계정 구성 상태](media/automation-manage-account/automation-account-runas-incomplete-config.png)

실행 계정을 선택하면 계정 **속성** 창은 다음과 같은 오류 메시지를 표시합니다.

![불완전 실행 계정 구성 경고 메시지](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png)에서도 확인할 수 있습니다.

계정을 삭제하고 다시 만들어서 이러한 실행 계정 문제를 신속하게 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)를 참조합니다.
* Azure Automation의 역할 기반 Access Control에 대한 자세한 내용은 [Azure Automation에서 역할 기반 Access Control](automation-role-based-access-control.md)을 참조하세요.
* 인증서 및 Azure 서비스에 대한 자세한 내용은 [Azure Cloud Services 인증서 개요](../cloud-services/cloud-services-certs-create.md)를 참조하세요.