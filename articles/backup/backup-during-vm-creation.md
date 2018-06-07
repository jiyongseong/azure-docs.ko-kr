---
title: Azure VM을 만드는 동안 백업 사용 | Microsoft Docs
description: Azure Virtual Machine을 만드는 동안 백업을 사용하도록 설정하는 단계를 참조하세요.
services: backup, virtual-machines
documentationcenter: ''
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: e16714eee697d9dc8102b5f29d9639d650517562
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Azure Virtual Machine을 만드는 동안 백업 사용 

Azure Backup 서비스는 클라우드에 대한 백업을 만들고 구성하기 위한 인터페이스를 제공합니다. 정기적으로 복구 지점이라고 하는 백업을 수행하여 데이터를 보호합니다. Azure Backup은 지역 중복 복구 자격 증명 모음에 저장될 수 있는 복구 지점을 만듭니다. 이 문서에서는 Azure Portal에서 VM(가상 머신)을 만드는 동안 백업을 사용하도록 설정하는 방법을 자세히 설명합니다.  

## <a name="log-in-to-azure"></a>Azure에 로그인 

계정에 아직 로그인하지 않은 경우 [Azure Portal](http://portal.azure.com)에 로그인합니다.
 
## <a name="create-virtual-machine-with-backup-configured"></a>백업이 구성된 가상 머신 만들기 

1. Azure Portal의 왼쪽 위 모서리에서 **새로 만들기**를 클릭합니다. 

2. **계산**을 선택한 후 가상 머신의 이미지를 선택합니다.   

3. 가상 머신 정보를 입력합니다. 제공한 사용자 이름과 암호는 가상 머신에 로그인하는 데 사용됩니다. 완료되면 **확인**을 클릭합니다. 

4. VM의 크기를 선택합니다.  

5. **설정 > 백업**에서 **사용**을 클릭하여 백업 구성 설정을 표시합니다. 설정 페이지에서 기본값을 그대로 적용하고 **확인**을 클릭하여 요약 페이지로 이동한 후 VM을 만들 수 있습니다. 값을 변경하려면 다음 단계를 수행합니다.  

6. 가상 머신의 백업을 포함하는 Recovery Services 자격 증명 모음을 만들거나 선택합니다. Recovery Services 자격 증명 모음을 만드는 경우 자격 증명 모음에 대한 리소스 그룹을 선택할 수 있습니다.  

    ![VM 만들기 페이지의 백업 구성](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Recovery Services 자격 증명 모음에 대한 리소스 그룹은 가상 머신에 대한 리소스 그룹과 다를 수 있습니다.  
    > 
    > 

7. 기본적으로 VM을 신속하게 보호할 수 있도록 하는 백업 정책이 선택됩니다. 백업 정책은 백업 스냅숏을 작성하는 빈도와 해당 백업 복사본을 보존하는 기간을 지정합니다. 기본 정책을 그대로 사용하거나 다른 백업 정책을 만들거나 선택할 수 있습니다. 백업 정책을 편집하려면 **백업 정책**을 선택하고 정책의 값을 변경합니다.  

8. 백업 구성 값이 만족스러우면 설정 페이지에서 **확인**을 클릭합니다.  

9. 요약 페이지에서 유효성 검사를 통과하면 **만들기**를 클릭하여 구성된 백업 설정을 사용하는 가상 머신을 만듭니다. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>VM을 만든 후 백업 시작 

백업 정책이 만들어졌어도 초기 백업을 만드는 것이 좋습니다. 가상 머신 만들기 템플릿이 완료된 후에 VM에 대한 백업 세부 정보를 보려면 왼쪽 메뉴의 **작업** 설정에서 **백업**을 클릭합니다. 이를 사용하여 요청 시 백업을 트리거하거나, 전체 VM 또는 모든 디스크를 복원하거나, VM 백업에서 파일을 복원하거나, 가상 머신과 연결된 백업 정책을 변경할 수 있습니다.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager 템플릿을 사용하여 보호된 VM 배포

이전 단계에서는 Azure Portal을 사용하여 가상 머신을 만들고 Recovery Services 자격 증명 모음에 대해 이를 보호하는 방법을 설명했습니다. 하나 이상의 가상 머신을 신속하게 배포하고 Recovery Services 자격 증명 모음에 대해 이를 보호하려면 [Windows VM 배포 및 백업 사용](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/) 템플릿을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>VM을 만들 때 어떤 VM 이미지가 백업을 사용 가능하게 설정하나요? 

Microsoft에서 게시한 다음과 같은 주요 이미지가 VM 생성 중에 백업을 설정하도록 지원됩니다. 다른 VM의 경우, VM이 만들어진 후에 백업을 사용 가능하게 설정할 수 있습니다. [VM을 만든 후 백업 사용](quick-backup-vm-portal.md)에 대해 자세히 알아보기 

- **Windows** - Windows Server 2016 Data center, Windows Server 2016 Data Center Core, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS), Ubuntu Server 1404(LTS) 
- **Redhat** - RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** - Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>VM 비용에 백업 비용이 포함되나요? 

아니요. 백업 비용은 가상 머신 비용과는 별도입니다. 백업 가격 책정에 대한 자세한 내용은 [백업 가격 책정 사이트](https://azure.microsoft.com/pricing/details/backup/)를 참조하세요.
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>VM에서 백업을 사용하도록 설정하는 데 필요한 권한은 무엇인가요? 

가상 머신 참가자인 경우 VM에서 백업을 사용하도록 설정할 수 있습니다. 사용자 지정 역할을 사용하는 경우에는 VM에서 백업을 사용하도록 설정하기 위해 다음 권한이 필요합니다. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Recovery Services 자격 증명 모음 및 가상 머신에 다른 리소스 그룹이 있으면 Recovery Services 자격 증명 모음 리소스 그룹에 쓰기 권한이 있어야 합니다.  

## <a name="next-steps"></a>다음 단계 

VM을 보호했으므로 다음 문서를 확인하여 VM 관리 작업과 VM 복원 방법을 알아봅니다. 

- [가상 머신 관리 및 모니터링](backup-azure-manage-vms.md) 
- [가상 머신 복원](backup-azure-arm-restore-vms.md) 
