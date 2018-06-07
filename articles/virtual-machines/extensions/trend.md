---
title: VM에 Trend Micro Deep Security 설치 | Microsoft Docs
description: 이 문서에서는 Azure에서 클래식 배포 모델을 사용하여 만든 VM에 Trend Micro 보안을 설치하고 구성하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: danis
ms.openlocfilehash: 6098d310bcc6fe5df2378688b78277fc7e4b16bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Windows VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
이 문서에서는 Windows Server가 실행되는 새 VM(가상 머신) 또는 기존 VM에서 Trend Micro Deep Security as a Service를 설치 및 구성하는 방법을 보여 줍니다. Deep Security as a Service는 맬웨어 방지 보호, 방화벽, 침입 방지 시스템 및 무결성 모니터링을 포함합니다.

이 클라이언트는 VM 에이전트를 통해 보안 확장 프로그램으로 설치됩니다. VM 에이전트는 Azure Portal에서 자동으로 생성되므로 새 가상 컴퓨터에서 Deep Security Agent를 설치합니다.

Azure Portal, Azure CLI 또는 PowerShell을 사용하여 만든 기존 VM에는 VM 에이전트가 없을 수 있습니다. VM 에이전트가 없는 기존 가상 머신에서는 이 에이전트를 먼저 다운로드하여 설치해야 합니다. 이 문서에서는 두 상황을 모두 다룹니다.

온-프레미스 솔루션용 Trend Micro의 현재 구독이 있는 경우 Azure Virtual Machines를 보호하는 데 사용할 수 있습니다. 아직 구독 고객이 아닌 경우에는 평가판 구독에 등록할 수 있습니다. 이 솔루션에 대한 자세한 내용은 Trend Micro 블로그 게시물 [Deep Security에 대한 Microsoft Azure VM 에이전트 확장](http://go.microsoft.com/fwlink/p/?LinkId=403945)을 참조하세요.

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>새 VM에 Deep Security Agent 설치

**Marketplace**의 이미지를 사용하여 가상 머신을 만드는 경우 [Azure Portal](http://portal.azure.com)에서 Trend Micro 보안 확장을 설치할 수 있습니다. 포털을 사용하면 단일 가상 컴퓨터를 만들 때 Trend Micro의 보호 기능을 쉽게 추가할 수 있습니다.

**Marketplace**의 항목을 사용하면 가상 머신 설치에 도움이 되는 마법사가 열립니다. 마법사의 세 번째 패널인 **설정** 블레이드를 사용하여 Trend Micro 보안 확장을 설치합니다.  일반적인 지침은 [Azure Portal에서 Windows를 실행하는 가상 머신 만들기](../windows/classic/tutorial.md)를 참조하세요.

마법사의 **설정** 블레이드로 이동한 후 다음 단계를 수행합니다.

1. **확장**을 클릭한 후 다음 창에서 **확장 추가**를 클릭합니다.

   ![확장 추가 시작][1]

2. **새 리소스** 창에서 **Deep Security Agent**를 선택합니다. Deep Security Agent 창에서 **만들기**를 클릭합니다.

   ![Deep Security Agent 식별][2]

3. 확장에 대한 **테넌트 식별자** 및 **테넌트 활성화 암호**를 입력합니다. 선택적으로 **보안 정책 식별자**를 입력할 수도 있습니다. 그런 후 **확인**을 클릭하여 클라이언트를 추가합니다.

   ![확장 세부 정보 제공][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>기존 VM에 Deep Security Agent 설치
기존 VM에 에이전트를 설치하려면 다음 항목이 필요합니다.

* Azure PowerShell 모듈 버전 0.8.2 이상이 로컬 컴퓨터에 설치되어 있어야 합니다. **Get-Module azure | format-table version** 명령을 사용하여 설치한 Azure PowerShell 버전을 확인할 수 있습니다. 지침 및 최신 버전에 대한 링크를 보려면 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. `Add-AzureAccount`를 사용하여 Azure 구독에 로그인합니다.
* VM 에이전트가 대상 가상 머신에 설치되어 있어야 합니다.

먼저 VM 에이전트가 이미 설치되어 있는지 확인합니다. 클라우드 서비스 이름과 가상 머신 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

클라우드 서비스 및 가상 머신 이름을 모르는 경우 **Get-AzureVM**을 실행하여 현재 구독의 모든 가상 머신에 대한 해당 정보를 표시합니다.

**write-host** 명령에서 **True**가 반환되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 반환되면 Azure 블로그 게시물 [VM 에이전트 및 확장 - 2부](http://go.microsoft.com/fwlink/p/?LinkId=403947)에서 지침 및 다운로드 링크를 참조합니다.

VM 에이전트가 설치되어 있는 경우 다음 명령을 실행합니다.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>다음 단계
에이전트가 설치되어 있는 경우 실행을 시작하는 데 몇 분 정도 걸립니다. 그 이후에는 Deep Security Manager를 통해 관리할 수 있도록 가상 머신에서 Deep Security를 정품 인증해야 합니다. 추가 지침은 다음 문서를 참조하세요.

* 이 솔루션과 관련된 Trend 문서, [Microsoft Azure에 대한 즉시 재생 가능한 클라우드 보안](http://go.microsoft.com/fwlink/?LinkId=404101)
* 가상 머신 구성을 위한 [샘플 Windows PowerShell 스크립트](http://go.microsoft.com/fwlink/?LinkId=404100)
* [지침](http://go.microsoft.com/fwlink/?LinkId=404099) 

## <a name="additional-resources"></a>추가 리소스
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[Azure VM 확장 및 기능]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]:../windows/classic/connect-logon.md
[Azure VM 확장 및 기능]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
