---
title: Azure 에이전트 없이 로컬 Windows 암호 재설정 | Microsoft Docs
description: Azure 게스트 에이전트가 설치되어 있지 않거나 VM에서 작동하지 않는 경우 로컬 Windows 사용자 계정의 암호를 재설정하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: iainfou
ms.openlocfilehash: ad892aee646b1a5f8c96d5bdeca24b7a0d88f38e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Azure VM 오프라인의 로컬 Windows 암호 재설정
Azure 게스트 에이전트 설치가 제공되는 [Azure Portal 또는 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하여 Azure에서 VM의 로컬 Windows 암호를 재설정할 수 있습니다. 이 방법은 Azure VM의 암호를 재설정하는 기본 방법입니다. Azure 게스트 에이전트가 응답하지 않거나 사용자 지정 이미지를 업로드한 후 설치에 실패하는 문제가 발생하는 경우 Windows 암호를 수동으로 재설정할 수 있습니다. 이 문서에는 다른 VM에 원본 OS 가상 디스크를 연결하여 로컬 계정 암호를 재설정하는 방법을 자세히 설명합니다. 이 문서에 설명된 단계는 Windows 도메인 컨트롤러에는 적용되지 않습니다. 

> [!WARNING]
> 이 프로세스는 마지막 수단으로만 사용합니다. 항상 [Azure Portal 또는 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 먼저 사용하여 암호를 재설정하도록 시도합니다.
> 
> 

## <a name="overview-of-the-process"></a>프로세스의 개요
Azure 게스트 에이전트에 대한 액세스가 없는 경우 Azure에서 Windows VM에 대한 로컬 암호 재설정을 수행하기 위한 핵심 단계는 다음과 같습니다.

* 원본 VM을 삭제합니다. 가상 디스크는 유지됩니다.
* Azure 구독 내에서 동일한 위치의 다른 VM에 원본 VM의 OS 디스크를 연결합니다. 이 VM은 문제 해결 VM이라고 합니다.
* 문제 해결 VM을 사용하여 원본 VM의 OS 디스크에 일부 구성 파일을 만듭니다.
* 문제 해결 VM에서 VM의 OS 디스크를 분리합니다.
* Resource Manager 템플릿을 사용하여 기존 가상 디스크를 사용하는 VM을 만듭니다.
* 새 VM이 부팅하면 만든 구성 파일은 필요한 사용자의 암호를 업데이트합니다.

## <a name="detailed-steps"></a>자세한 단계

> [!NOTE]
> 이 단계는 Windows 도메인 컨트롤러에는 적용되지 않습니다. 독립 실행형 서버 또는 도메인의 멤버인 서버에만 작동합니다.
> 
> 

다음 단계를 시도하기 전에 항상 [Azure Portal 또는 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하여 암호를 재설정하도록 시도합니다. 시작하기 전에 VM을 백업했는지 확인합니다. 

1. Azure Portal에서 영향을 받는 VM을 삭제합니다. VM을 삭제하면 Azure 내에서 메타데이터, VM의 참조만 삭제됩니다. 가상 디스크는 VM이 삭제될 때 유지됩니다.
   
   * Azure Portal에서 VM을 선택하고 *삭제*를 클릭합니다.
     
     ![기존 VM 삭제](./media/reset-local-password-without-agent/delete_vm.png)
2. 문제 해결 VM에 원본 VM의 OS 디스크를 연결합니다. 문제 해결 VM은 원본 VM의 OS 디스크와 동일한 지역에 있어야 합니다(예: `West US`).
   
   * Azure Portal에서 문제 해결 VM을 선택합니다. *디스크* | *기존 연결*을 클릭합니다.
     
     ![기존 디스크 연결](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     *VHD 파일*을 선택한 다음 원본 VM을 포함하는 저장소 계정을 선택합니다.
     
     ![저장소 계정 선택](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     원본 컨테이너를 선택합니다. 원본 컨테이너는 일반적으로 *vhds*입니다.
     
     ![저장소 컨테이너 선택](./media/reset-local-password-without-agent/disks_select_container.png)
     
     연결할 OS vhd를 선택합니다. *선택*을 클릭하여 프로세스를 완료합니다.
     
     ![원본 가상 디스크 선택](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. 원격 데스크톱을 사용하여 문제 해결 VM에 연결하고 원본 VM의 OS 디스크가 표시되는지 확인합니다.
   
   * Azure Portal에서 문제 해결 VM을 선택하고 *연결*을 클릭합니다.
   * 다운로드하는 RDP 파일을 엽니다. 문제 해결 VM의 사용자 이름 및 암호를 입력합니다.
   * 파일 탐색기에서 연결한 데이터 디스크를 찾습니다. 원본 VM의 VHD가 문제 해결 VM에 연결된 유일한 데이터 디스크인 경우 F: 드라이브여야 합니다.
     
     ![연결된 데이터 디스크 보기](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. 원본 VM 드라이브의 `\Windows\System32\GroupPolicy`에서 `gpt.ini`를 만듭니다.(gpt.ini가 있는 경우 gpt.ini.bak으로 이름을 변경합니다.)
   
   > [!WARNING]
   > C:\Windows(문제 해결 VM에 대한 OS 드라이브)에 다음 파일을 실수로 만들지 않도록 합니다. 데이터 디스크로 연결된 원본 VM의 OS 드라이브에 다음 파일을 만듭니다.
   > 
   > 
   
   * 만든 `gpt.ini` 파일에 다음 줄을 추가합니다.
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![gpt.ini 만들기](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. `\Windows\System32\GroupPolicy\Machine\Scripts`에 `scripts.ini`를 만듭니다. 숨겨진 폴더가 표시되어 있는지 확인합니다. 필요한 경우 `Machine` 또는 `Scripts` 폴더를 만듭니다.
   
   * 만든 `scripts.ini` 파일에 다음 줄을 추가합니다.
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![scripts.ini 만들기](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. 다음 내용으로 `<username>` 및 `<newpassword>`를 고유한 값으로 교체하여 `\Windows\System32`에 `FixAzureVM.cmd`를 만듭니다.
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd 만들기](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    새 암호를 정의하는 경우 VM에 대해 구성된 암호 복잡성 요구 사항을 충족해야 합니다.
7. Azure Portal에서 문제 해결 VM에서 디스크를 분리합니다.
   
   * Azure Portal에서 문제 해결 VM을 선택하고 *디스크*를 클릭합니다.
   * 2단계에서 연결된 데이터 디스크를 선택하고 *분리*를 클릭합니다.
     
     ![디스크 분리](./media/reset-local-password-without-agent/detach_disk.png)
8. VM을 만들기 전에 원본 OS 디스크에 URI를 가져옵니다.
   
   * Azure Portal에서 저장소 계정을 선택하고 *Blob*을 클릭합니다.
   * 컨테이너를 선택합니다. 원본 컨테이너는 일반적으로 *vhds*입니다.
     
     ![저장소 계정 Blob 선택](./media/reset-local-password-without-agent/select_storage_details.png)
     
     원본 VM OS VHD를 선택하고 *URL* 이름 옆의 *복사* 단추를 클릭합니다.
     
     ![디스크 URI 복사](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. 원본 VM의 OS 디스크에서 VM을 만듭니다.
   
   * [이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)을 사용하여 전문화된 VHD에서 VM을 만듭니다. `Deploy to Azure` 단추를 클릭하여 템플릿 세부 정보로 채워진 Azure Portal을 엽니다.
   * VM에 대한 모든 이전 설정을 유지하려는 경우 *템플릿 편집*을 선택하여 기존 VNet, 서브넷, 네트워크 어댑터 또는 공용 IP를 제공합니다.
   * `OSDISKVHDURI` 매개 변수 입력란에 이전 단계에서 가져온 원본 VHD의 URI를 붙여 넣습니다.
     
     ![템플릿에서 VM 만들기](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. 새 VM을 실행한 후 `FixAzureVM.cmd` 스크립트에서 지정한 새 암호로 원격 데스크톱을 사용하여 VM에 연결합니다.
11. 새 VM에 대한 원격 세션에서 다음 파일을 제거하여 환경을 정리합니다.
    
    * %windir%\System32에서
      * FixAzureVM.cmd 제거
    * %windir%\System32\GroupPolicy\Machine\에서
      * scripts.ini 제거
    * %windir%\System32\GroupPolicy에서
      * gpt.ini 제거(이전에 gpt.ini가 있었고 gpt.ini.bak으로 이름을 변경한 경우 .bak 파일을 gpt.ini로 다시 이름 변경)

## <a name="next-steps"></a>다음 단계
원격 데스크톱을 사용하여 계속 연결할 수 없는 경우 [RDP 문제 해결 가이드](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. [자세한 RDP 문제 해결 가이드](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)는 특정 단계보다 문제 해결 방법을 살펴봅니다. 직접적인 도움을 위해 [Azure 지원 요청](https://azure.microsoft.com/support/options/)을 개설할 수도 있습니다.

