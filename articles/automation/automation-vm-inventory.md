---
title: 인벤토리 수집을 사용하여 Azure 가상 머신 관리 | Microsoft Docs
description: 인벤토리 수집을 사용하여 가상 컴퓨터 관리
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: 인벤토리, 자동화, 변경, 추적
author: jennyhunter-msft
ms.author: jehunte
ms.date: 03/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d5f9bae34dabba71861adc9b2aeb0d33b8a1e226
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>인벤토리 수집을 사용하여 Azure 가상 컴퓨터 관리

가상 컴퓨터의 리소스 페이지에서 Azure 가상 컴퓨터에 대한 인벤토리 추적을 활성화할 수 있습니다. 이 방법은 인벤토리 수집을 설정 및 구성하는 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

이 문서에서는 솔루션을 구성할 VM이 있다고 가정합니다. Azure 가상 머신이 없는 경우 [가상 머신을 만듭니다](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>가상 머신 리소스 페이지에서 인벤토리 수집 활성화

1. Azure Portal의 왼쪽 창에서 **가상 머신**를 선택합니다.
2. 가상 머신 목록에서 가상 머신을 선택합니다.
3. **작업** 아래의 **리소스** 메뉴에서 **인벤토리**를 선택합니다.
4. Log Analytics 작업 영역을 선택하여 데이터 로그를 저장합니다.
    해당 영역에 대해 사용할 수 있는 작업 영역이 없는 경우 기본 작업 영역 및 자동화 계정을 만들라는 메시지가 표시됩니다.
5. 컴퓨터 온보딩을 시작하려면 **활성화**를 선택합니다.

   ![온보딩 옵션 보기](./media/automation-vm-inventory/inventory-onboarding-options.png)

    상태 표시줄에 솔루션이 활성화했다는 메시지가 표시됩니다. 이 프로세스는 15분까지 걸릴 수 있습니다. 이 시간 동안 창을 닫을 수 있습니다. 또는 창을 열어 두면 솔루션이 활성화될 때 알림 메시지가 표시됩니다. 알림 창에서 배포 상태를 모니터링할 수 있습니다.

   ![온보딩 직후 인벤토리 솔루션 보기](./media/automation-vm-inventory/inventory-onboarded.png)

배포가 완료되면 상태 표시줄이 사라집니다. 이때 시스템은 여전히 인벤토리 데이터를 수집하며, 아직 데이터가 표시되지 않을 수 있습니다. 전체 데이터 수집에 24시간이 걸릴 수 있습니다.

## <a name="configure-your-inventory-settings"></a>인벤토리 설정 구성

기본적으로 수집을 위해 소프트웨어, Windows 서비스 및 Linux 데몬을 구성합니다. Windows 레지스트리 및 파일 인벤토리를 수집하기 위해 인벤토리 컬렉션 설정을 구성합니다.

1. **인벤토리** 보기에서 창 맨 위에 있는 **설정 편집** 단추를 선택합니다.
2. 새 수집 설정을 추가하려면 **Windows 레지스트리**, **Windows 파일** 및 **Linux 파일** 탭을 선택하여 추가할 설정 범주로 이동합니다.
3. 적절한 범주를 선택하고 창 위쪽에서 **추가**를 클릭합니다.

다음 표는 다양한 범주에 대해 구성할 수 있는 각 속성에 대한 정보를 제공합니다.

### <a name="windows-registry"></a>Windows 레지스트리

|자산  |설명  |
|---------|---------|
|사용     | 설정 적용 여부 결정        |
|Item Name     | 추적할 파일의 이름        |
|그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
|Windows 레지스트리 키   | 파일을 검사할 경로. 예: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows 파일

|자산  |설명  |
|---------|---------|
|사용     | 설정 적용 여부 결정        |
|Item Name     | 추적할 파일의 이름        |
|그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
|경로 입력     | 파일을 확인할 경로입니다. 예: "c:\temp\myfile.txt"

### <a name="linux-files"></a>Linux 파일

|자산  |설명  |
|---------|---------|
|사용     | 설정 적용 여부 결정        |
|Item Name     | 추적할 파일의 이름        |
|그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
|경로 입력     | 파일을 확인할 경로. 예: "/etc/*.conf"       |
|경로 유형     | 추적할 항목 유형입니다. 가능한 값은 파일 및 디렉터리입니다.        |
|재귀     | 추적할 항목을 찾을 때 재귀가 사용되는지 결정합니다.        |
|sudo 사용     | 항목을 확인할 때 sudo가 사용되는지 여부를 결정합니다.         |
|링크     | 디렉터리를 트래버스할 때 기호화된 링크에서 처리하는 방법을 결정합니다.<br> **Ignore** - 심볼 링크를 무시하고 참조된 파일/디렉터리를 포함하지 않음<br>**Follow** - 재귀 중에 심볼 링크를 따르고 참조된 파일/디렉터리도 포함<br>**Manage** - 심볼 링크를 따르고 반환된 콘텐츠의 처리를 변경하도록 허용      |

## <a name="disconnect-your-virtual-machine-from-management"></a>관리에서 가상 머신 분리

인벤토리 관리에서 가상 머신을 제거하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 창에서 **Log Analytics**를 선택한 다음 가상 머신을 온보딩할 때 사용한 작업 영역을 선택합니다.
2. **Log Analytics** 창의 **리소스** 메뉴에서 **작업 영역 데이터 소스** 범주에 있는 **가상 머신**를 선택합니다.
3. 목록에서 분리할 가상 머신을 선택합니다. 가상 머신은 **OMS 연결** 열의 **이 작업 영역** 옆에 녹색 확인 표시가 있습니다.
4. 다음 페이지 맨 위에서 **연결 끊기**를 선택합니다.
5. 확인 창에서 **예**를 선택합니다.
    이 작업으로 관리에서 컴퓨터 연결이 끊깁니다.

## <a name="next-steps"></a>다음 단계

* 가상 머신의 파일 및 레지스트리 설정에서 변경 관리에 대해 알아보려면 [변경 내용 추적 솔루션으로 사용자 환경에서 소프트웨어 변경 추적](../log-analytics/log-analytics-change-tracking.md)을 참조하세요.
* 가상 머신에서 Windows 및 패키지 업데이트 관리에 대해 알아보려면 [Azure의 업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 참조하세요.
