---
title: Azure에서 Linux 가상 머신의 부팅 진단 | Microsoft Docs
description: Azure의 Linux 가상 머신에 대한 두 가지 디버깅 기능 개요
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941856"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>부팅 진단을 사용하여 Azure의 Linux 가상 머신 문제를 해결하는 방법

이제 Azure에서 두 가지 디버깅 기능에 대한 지원이 제공됩니다. Azure Virtual Machines Resource Manager 배포 모델에 대한 콘솔 출력 및 스크린샷 지원. 

자신의 이미지를 Azure로 가져 오거나 플랫폼 이미지 중 하나를 부팅 할 때 Virtual Machines가 부팅 불가능한 상태가 되는 데에는 많은 이유가 있을 수 있습니다. 이 기능을 사용하면 부팅 오류에서 Virtual Machines를 쉽게 진단하고 복구할 수 있습니다.

Linux Virtual Machines의 경우 포털에서 콘솔 로그의 출력을 쉽게 볼 수 있습니다.

![Azure portal](./media/boot-diagnostics/screenshot1.png)
 
그러나 Windows 및 Linux Virtual Machines의 경우 Azure를 사용하면 하이퍼바이저에서 VM의 스크린샷을 볼 수도 있습니다.

![오류](./media/boot-diagnostics/screenshot2.png)

이 두 가지 기능이 모든 지역의 Azure Virtual Machines에서 지원됩니다. 참고로, 스크린샷 및 출력을 저장소 계정에 표시하는 데 최대 10분이 소요될 수 있습니다.

## <a name="common-boot-errors"></a>일반적인 부팅 오류

- [파일 시스템 문제](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [커널 문제](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB 오류](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>새 가상 머신에서 진단 사용
1. Azure Portal에서 새 가상 머신을 만드는 경우 배포 모델 드롭다운에서 **Azure Resource Manager**를 선택합니다.
 
    ![리소스 관리자](./media/boot-diagnostics/screenshot3.jpg)

2. **설정**에서 **부팅 진단**을 활성화한 다음, 이러한 진단 파일을 저장하려는 저장소 계정을 선택합니다.
 
    ![VM 만들기](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > 부팅 진단 기능은 프리미엄 저장소 계정을 지원하지 않습니다. 부팅 진단에 프리미엄 저장소 계정을 사용하는 경우 VM을 시작할 때 StorageAccountTypeNotSupported 오류가 발생할 수 있습니다. 
    >
    > 

3. Azure Resource Manager 템플릿에서 배포하는 경우 가상 머신 리소스로 이동하고 진단 프로필 섹션을 추가합니다. "2015-06-15" API 버전 헤더를 사용해야 합니다.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. 진단 프로필을 사용하면 이러한 로그를 저장할 저장소 계정을 선택할 수 있습니다.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

부팅 진단이 활성화된 샘플 가상 머신을 배포하려면 여기에서 리포지토리를 확인하세요.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>기존 가상 머신에서 부팅 진단 활성화 

기존 가상 머신에서 부팅 진단을 활성화하려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, 가상 머신을 선택합니다.
2. **지원 + 문제 해결**에서 **부팅 진단** > **설정**을 선택하고, 상태를 **On**으로 변경한 다음, 저장소 계정을 선택합니다. 
4. 부팅 진단 옵션이 선택되어 있는지 확인한 다음, 변경 내용을 저장합니다.

    ![기본 VM 업데이트](./media/boot-diagnostics/enable-for-existing-vm.png)

3. 적용하려면 VM을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

VM 부트 진단을 사용하는 경우 “로그의 콘텐츠를 가져오지 못했습니다” 오류가 표시되면 [VM 부팅 진단 로그에서 오류의 콘텐츠를 가져오지 못했습니다](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur)를 참조하세요.