---
title: Azure Automation DSC 시작하기
description: Azure Automation DSC(필요한 상태 구성)에서 가장 일반적인 작업의 설명 및 예제
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd26c4b070ed7b92a73e43c1568805a9ac679d12
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>Azure Automation DSC 시작하기

이 문서에서는 Azure Automation DSC(필요한 상태 구성)로 만들기, 가져오기 및 구성 컴파일링, 관리할 컴퓨터 온보드 및 보고서 보기 등과 같은 가장 일반적인 작업을 수행하는 방법을 설명합니다. Azure Automation DSC가 무엇인지의 개요는 [Azure Automation DSC 개요](automation-dsc-overview.md)를 참조하세요. DSC 설명서는 [Windows PowerShell 필요한 상태 구성 개요](/powershell/dsc/overview)를 참조하세요.

이 문서에서는 Azure Automation DSC 사용에 대한 단계별 가이드를 제공합니다. 이 문서에서 설명하는 단계를 수행하지 않고 이미 설정한 샘플 환경을 원하는 경우 뒤에 나오는 Resource Manager 템플릿을 사용할 수 있습니다. 이 템플릿은 Azure 자동화 DSC에서 관리되는 Azure VM을 포함하여 완성된 Azure 자동화 DSC 환경을 설정합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 예제를 완료하려면 다음이 필요합니다.

* Azure Automation 계정. Azure Automation 실행 계정 만들기에 대한 지침은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 참조하세요.
* Windows Server 2008 R2 이상을 실행하는 Azure Resource Manager VM(클래식 아님). VM 만들기에 대한 지침은 [Azure 포털에서 첫 번째 Windows 가상 머신 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>DSC 구성 만들기

노드를 할당하는 방법에 따라 [웹 서버](/powershell/dsc/configurations) Windows 기능(IIS)의 존재 또는 부재를 확인하는 간단한 **DSC 구성**을 만듭니다.

1. Windows PowerShell ISE(또는 다른 텍스트 편집기)를 시작합니다.
1. 다음 텍스트를 입력합니다.

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true

            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'

            }
        }
    }
    ```
1. 파일을 `TestConfig.ps1`(으)로 저장합니다.

이 구성은 각 노드 블록에서 [웹 서버](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource)기능의 존재 또는 부재를 확인하는 하나의 리소스, **WindowsFeature 리소스** 를 호출합니다.

## <a name="importing-a-configuration-into-azure-automation"></a>Azure Automation으로 구성 가져오기

다음으로 Automation 계정으로 구성을 가져옵니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서**DSC 구성**을 선택합니다.
1. **DSC 구성** 페이지에서 **+ 구성 추가**를 클릭합니다.
1. **구성 가져오기** 페이지에서 컴퓨터의 `TestConfig.ps1` 파일로 이동합니다.

    ![**구성 가져오기** 블레이드의 스크린샷](./media/automation-dsc-getting-started/AddConfig.png)
1. **확인**을 클릭합니다.

## <a name="viewing-a-configuration-in-azure-automation"></a>Azure Automation에서 구성 보기

구성을 가져온 후에 Azure 포털에서 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서**DSC 구성**을 선택합니다.
1. **DSC 구성** 페이지에서 **TestConfig**(이전 절차에서 가져온 구성의 이름임)를 클릭합니다.
1. **TestConfig 구성** 페이지에서 **구성 원본 보기**를 클릭합니다.

    ![TestConfig 구성 블레이드의 스크린샷](./media/automation-dsc-getting-started/ViewConfigSource.png)

    **TestConfig 구성 원본** 블레이드가 열리고 구성에 대한 PowerShell 코드를 표시합니다.

## <a name="compiling-a-configuration-in-azure-automation"></a>Azure Automation에서 구성 컴파일

노드에 원하는 상태를 적용하려면 먼저 해당 상태를 정의하는 DSC 구성을 하나 이상의 노드 구성(MOF 문서)으로 컴파일한 다음 Automation DSC 끌어오기 서버에 배치해야 합니다. Azure Automation DSC에서 구성 컴파일의 자세한 설명은 [Azure Automation DSC에서 구성을 컴파일](automation-dsc-compile.md)을 참조하세요. 구성 컴파일에 대한 자세한 내용은 [DSC 구성](/powershell/dsc/configurations)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서**DSC 구성**을 클릭합니다.
1. **DSC 구성** 페이지에서 **TestConfig**(이전에 가져온 구성의 이름)를 클릭합니다.
1. **TestConfig 구성** 페이지에서 **컴파일**을 클릭한 다음 **예**를 클릭합니다. 컴파일 작업이 시작됩니다.

    ![컴파일 단추를 강조 표시하는 TestConfig 구성 페이지의 스크린샷](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Automation에서 구성을 컴파일하면 생성된 모든 노드 구성 MOF를 끌어오기 서버에 자동으로 배포합니다.

## <a name="viewing-a-compilation-job"></a>컴파일 작업 보기

컴파일을 시작한 후에 **구성** 블레이드의 **컴파일 작업** 타일에서 볼 수 있습니다. **컴파일 작업** 타일은 현재 실행 중인, 완료된 및 실패한 작업을 표시합니다. 컴파일 작업 블레이드를 열면 발생한 모든 오류 또는 경고, 구성에서 사용된 입력 매개 변수 및 컴파일 로그를 포함한 해당 작업에 대한 정보를 표시합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서**DSC 구성**을 클릭합니다.
1. **DSC 구성** 페이지에서 **TestConfig**(이전에 가져온 구성의 이름)를 클릭합니다.
1. **컴파일 작업**에서 보려는 컴파일 작업을 선택합니다. 컴파일 작업이 시작된 날짜로 레이블이 지정된 **컴파일 작업** 페이지가 열립니다.

    ![컴파일 작업 페이지의 스크린샷](./media/automation-dsc-getting-started/CompilationJob.png)
1. **컴파일 작업** 페이지에서 타일을 클릭하여 작업에 대한 추가 세부 정보를 확인합니다.

## <a name="viewing-node-configurations"></a>노드 구성 보기

컴파일 작업을 성공적으로 완료하면 하나 이상의 새 노드 구성을 만듭니다. 노드 구성은 끌어오기 서버에 배포되고 하나 이상의 노드에서 가져오고 적용될 준비가 된 MOF 문서입니다. **DSC 노드 구성** 블레이드의 Automation 계정에서 노드 구성을 볼 수 있습니다. 노드 구성은 *ConfigurationName*.*NodeName*의 형식으로 이름을 가집니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 허브 메뉴에서 **모든 리소스** 를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드에서 **DSC 노드 구성**을 클릭합니다.

    ![DSC 노드 구성 블레이드의 스크린샷](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Azure Automation DSC를 통한 관리를 위한 Azure VM 온보드

Azure Automation DSC를 사용하여 Azure VM(클래식 및 Resource Manager), 온-프레미스 VM, Linux 컴퓨터, AWS VM 및 온-프레미스 물리적 컴퓨터를 관리할 수 있습니다. 이 문서에서는 Azure Resource Manager VM만 온보딩하는 방법을 배웁니다. 다른 유형의 컴퓨터 온보드에 대한 정보는 [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Azure Automation DSC를 통한 관리를 위한 Azure Resource Manager VM 온보드하기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리** 아래에서 **DSC 노드**를 클릭합니다.
1. **DSC 노드** 페이지에서 **Azure VM 추가**를 클릭합니다.

    ![Azure VM 추가 단추를 강조 표시하는 DSC 노드 페이지의 스크린샷](./media/automation-dsc-getting-started/OnboardVM.png)
1. Virtual Machines 페이지에서 VM을 선택합니다. **Azure VM 추가** 페이지에서 **온보드할 가상 머신 선택**을 클릭합니다.
1. **Connect**를 클릭합니다.

   > [!IMPORTANT]
   > Windows Server 2008 R2 이상을 실행하는 Azure Resource Manager VM이어야 합니다.

1. **등록** 페이지에서 VM에 적용하려는 노드 구성의 이름을 **노드 구성 이름** 상자에 입력합니다. Automation 계정의 노드 구성 이름과 정확하게 일치해야 합니다. 이 시점에서 이름을 제공하는 것은 선택 사항입니다. 노드를 온보드한 후 할당된 노드 구성을 변경할 수 있습니다.
   **필요한 경우 노드 다시 부팅**을 선택한 다음 **확인**을 클릭합니다.

    ![등록 블레이드의 스크린샷](./media/automation-dsc-getting-started/RegisterVM.png)

    지정한 노드 구성이 **구성 모드 빈도**에서 지정된 간격으로 VM에 적용되고 VM은 **새로 고침 빈도**에서 지정된 간격으로 노드 구성에 대한 업데이트를 확인합니다. 이러한 값을 사용하는 방법에 대한 자세한 내용은 [로컬 구성 관리자 구성](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)을 참조하세요.
1. **Azure VM 추가** 블레이드에서 **만들기**를 클릭합니다.

Azure는 VM 온보딩 프로세스를 시작합니다. 완료되면 VM이 Automation 계정의 **DSC 노드** 블레이드에 나타납니다.

## <a name="viewing-the-list-of-dsc-nodes"></a>DSC 노드 목록 보기

**DSC 노드** 블레이드의 Automation 계정에서 관리에 온보드된 모든 컴퓨터의 목록을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
3. **Automation 계정** 페이지에서 **DSC 노드**를 클릭합니다.

## <a name="viewing-reports-for-dsc-nodes"></a>DSC 노드에 대한 보고서 보기

Azure Automation DSC가 관리되는 노드에 대한 일관성 검사를 수행할 때마다 노드는 끌어오기 서버에 상태 보고서를 보냅니다. 해당 노드에 대한 페이지에서 이러한 보고서를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
3. **Automation 계정** 페이지에서 **DSC 노드**를 클릭합니다.
4. **DSC 노드** 목록에서 보려는 노드를 선택합니다.
5. **노드** 페이지의 **보고서**에서 보려는 보고서를 클릭합니다.

    ![보고서 블레이드의 스크린샷](./media/automation-dsc-getting-started/NodeReport.png)

개별 보고서에 대한 블레이드에서 해당 일관성 검사에 대한 다음과 같은 상태 정보를 확인할 수 있습니다.

* 보고서 상태 — 노드의 "준수" 여부, 구성 "실패" 또는 노드는 "비준수"입니다.(노드가 **applyandmonitor** 모드에 있는 경우 및 컴퓨터는 원하는 상태에 있지 않습니다.)
* 일관성 검사에 대한 시작 시간.
* 일관성 검사에 대한 총 런타임.
* 일관성 검사의 형식.
* 오류 코드 및 오류 메시지를 포함하는 모든 오류.
* 구성에서 사용되는 모든 DSC 리소스 및 각 리소스의 상태(노드가 해당 리소스에 대해 원하는 상태 여부) — 각 리소스를 클릭하여 해당 리소스에 대한 자세한 정보를 가져올 수 있습니다.
* 노드의 이름, IP 주소 및 구성 모드.

**원시 보고서 보기** 를 클릭하여 노드가 서버에 전송하는 실제 데이터를 확인할 수 있습니다. 해당 데이터 사용에 대한 자세한 내용은 [DSC 보고서 서버 사용](https://msdn.microsoft.com/powershell/dsc/reportserver)을 참조하세요.

노드가 온보드된 후 첫 번째 보고서를 사용할 수 있기 전까지 다소 시간이 걸릴 수 있습니다. 노드를 온보드한 후 첫 번째 보고서에 대해 최대 30분을 기다려야 합니다.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>다른 노드 구성에 노드 다시 할당

처음에 할당한 것과 다른 노드 구성을 사용하도록 노드를 할당할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
3. **Automation 계정** 페이지에서 **DSC 노드**를 클릭합니다.
4. **DSC 노드** 페이지에서 다시 할당하려는 노드의 이름을 클릭합니다.
5. 해당 노드에 대한 페이지에서 **노드 할당**을 클릭합니다.

    ![노드 할당 단추를 강조 표시하는 노드 블레이드의 스크린샷](./media/automation-dsc-getting-started/AssignNode.png)
6. **노드 구성 할당** 페이지에서 노드를 할당하려는 노드 구성을 선택한 다음 **확인**을 클릭합니다.

    ![노드 구성 할당 블레이드의 스크린샷](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>노드 등록 취소

Azure Automation DSC에 의해 노드를 더 이상 관리하지 않으려는 경우 등록 취소할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
3. **Automation 계정** 페이지에서 **DSC 노드**를 클릭합니다.
4. **DSC 노드** 페이지에서 등록 취소하려는 노드의 이름을 클릭합니다.
5. 해당 노드에 대한 페이지에서 **등록 취소**를 클릭합니다.

    ![등록 취소 단추를 강조 표시하는 노드 블레이드의 스크린샷](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>관련 문서

* [Azure Automation DSC 개요](automation-dsc-overview.md)
* [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)
* [Windows PowerShell 필요한 상태 구성 개요](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC cmdlets](/powershell/module/azurerm.automation/#automation)
* [Azure Automation DSC 가격 책정](https://azure.microsoft.com/pricing/details/automation/)
