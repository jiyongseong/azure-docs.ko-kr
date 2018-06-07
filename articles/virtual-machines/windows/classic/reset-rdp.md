---
title: Azure에서 Windows VM의 암호 또는 원격 데스크톱 구성 다시 설정 | Microsoft Docs
description: Azure Portal 또는 Azure PowerShell을 통해 클래식 배포 모델을 사용하여 만든 Windows VM에서 계정 암호 또는 원격 데스크톱 서비스를 다시 설정하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 516cb740f9acad19dac77db0239341b42a2b27fe
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 배포 모델을 사용하여 만든 VM에 대해서도 이러한 단계를 수행](../reset-rdp.md)할 수 있습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Windows 가상 컴퓨터에 연결할 수 없는 경우 로컬 관리자 암호를 다시 설정할 수도 있고 원격 데스크톱 서비스 구성을 다시 설정할 수도 있습니다. 암호를 다시 설정하려면 Azure 포털이나 Azure PowerShell의 VM 액세스 확장을 사용할 수 있습니다.

## <a name="ways-to-reset-configuration-or-credentials"></a>구성 또는 자격 증명을 다시 설정하는 방법
필요에 따라 몇 가지 방법으로 원격 데스크톱 서비스 및 자격 증명을 재설정할 수 있습니다.

- [Azure 포털을 사용하여 재설정](#azure-portal)
- [Azure PowerShell을 사용하여 재설정](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure portal
[Azure Portal](https://portal.azure.com)을 사용하여 원격 데스크톱 서비스를 초기화할 수 있습니다. 포털 메뉴를 확장하려면 왼쪽 위 구석에 있는 세 개의 막대를 클릭한 다음 **가상 머신(클래식)**를 클릭합니다.

![Azure VM 찾아보기](./media/reset-rdp/Portal-Select-Classic-VM.png)

Windows 가상 머신을 선택하고 **원격으로 다시 설정...**을 클릭합니다. 원격 데스크톱 구성을 재설정하도록 다음 대화 상자가 나타납니다.

![RDP 구성 다시 설정 페이지](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

로컬 관리자 계정의 이름 및 암호를 다시 설정할 수도 있습니다. VM에서 **지원 + 문제 해결** > **암호 다시 설정**을 클릭합니다. 암호 다시 설정 블레이드가 표시됩니다.

![암호 다시 설정 페이지](./media/reset-rdp/Portal-PW-Reset-Windows.png)

새 사용자 이름 및 암호를 입력한 후 **저장**을 클릭합니다.

## <a name="vmaccess-extension-and-powershell"></a>VMAccess 확장 및 PowerShell
VM 에이전트가 가상 머신에 설치되어 있어야 합니다. VM 에이전트를 사용할 수 있는 한 VMAccess 확장은 설치하지 않아도 사용할 수 있습니다. 다음 명령을 사용하여 VM 에이전트가 이미 설치되어 있는지 확인합니다. "myCloudService" 및 "myVM"을 각각 클라우드 서비스 및 VM의 이름으로 바꿉니다. 매개 변수 없이 `Get-AzureVM` 을 실행하면 이러한 이름을 파악할 수 있습니다.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

**write-host** 명령에서 **True**가 표시되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 표시되면 [VM 에이전트 및 확장 - 2부](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure 블로그 게시물에서 지침 및 다운로드 링크를 참조합니다.

포털에서 가상 머신을 만든 경우 `$vm.GetInstance().ProvisionGuestAgent` 이 **True**를 반환하는지 확인합니다. 그렇지 않은 경우 다음 명령을 사용하여 설정할 수 있습니다.

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

이 명령은 다음 단계인 “IaaS VM 액세스 확장을 설정하기 전에 VM 개체에서 게스트 에이전트 프로비전을 사용하도록 설정”에서 **Set-AzureVMExtension** 명령을 실행할 때 다음과 같은 오류를 방지합니다.

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**
현재 로컬 관리자 계정 이름 및 새 암호로 로그인 자격 증명을 만들고 다음과 같이 `Set-AzureVMAccessExtension` 을 실행합니다.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

현재 계정이 아닌 다른 이름을 입력하는 경우 VMAccess 확장은 로컬 관리자 계정의 이름을 바꾸며, 해당 계정에 암호를 할당하고 원격 데스크톱 로그아웃을 실행합니다. 로컬 관리자 계정이 비활성화 된 경우 VMAccess 확장으로 사용합니다.

이 명령은 원격 데스크톱 서비스 구성을 다시 설정합니다.

### <a name="reset-the-remote-desktop-service-configuration"></a>**원격 데스크톱 서비스 구성을 다시 설정**
원격 데스크톱 서비스 구성을 재설정하려면 다음 명령을 실행합니다.

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

VMAccess 확장은 가상 머신에서 다음 두 명령을 실행합니다.

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

이 명령은 들어오는 원격 데스크톱 트래픽을 허용하는 기본 제공 Windows 방화벽 그룹을 활성화하며 TCP 포트 3389를 사용합니다.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

이 명령은 원격 데스크톱 연결을 사용하도록 설정하는 fDenyTSConnections 레지스트리 값을 0으로 설정합니다.

## <a name="next-steps"></a>다음 단계
Azure VM 액세스 확장이 응답하지 않고 암호를 다시 설정할 수 없는 경우 [오프라인으로 로컬 Windows 암호를 다시 설정](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수 있습니다. 이 방법은 좀 더 고급 프로세스로, 문제가 있는 VM의 가상 하드 디스크를 다른 VM에 연결해야 합니다. 먼저 이 문서에 설명된 단계를 수행하고, 오프라인 암호 다시 설정 방법은 최후의 수단으로만 시도합니다.

[Azure VM 확장 및 기능](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows 기반 Azure 가상 머신에 대한 원격 데스크톱 연결 문제 해결](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

