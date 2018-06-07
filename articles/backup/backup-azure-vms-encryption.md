---
title: Azure Backup으로 암호화된 VM 백업 및 복원
description: 이 문서에서는 Azure Disk Encryption을 사용하여 암호화된 VM의 백업 및 복원 환경에 대해 설명합니다.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c788720e046c2efef954ef77f7b52854439b7515
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Azure Backup으로 암호화된 가상 머신을 백업 및 복원 
이 문서에서는 Azure Backup을 사용하여 VM(가상 머신)을 백업하고 복원하는 단계에 대해 설명합니다. 또한 지원되는 시나리오, 필수 조건 및 오류 사례에 대한 문제 해결 조치에 대한 자세한 정보도 제공합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

 * 암호화된 VM의 백업 및 복원은 Azure Resource Manager 배포 모델을 사용하는 VM에 대해서만 지원됩니다. 클래식 배포 모델을 사용하는 VM에는 지원되지 않습니다. <br>
 * 암호화된 VM의 백업 및 복원은 Azure Disk Encryption을 사용하는 Windows 및 Linux VM 모두에 지원됩니다. 디스크 암호화는 업계 표준인 Windows의 BitLocker 기능과 Linux의 dm-crypt 기능을 사용하여 디스크 암호화를 제공합니다. <br>
 
 다음 표에서는 BEK(BitLocker 암호화 키) 전용 및 KEK(Key Encryption Key) 암호화된 VM에 지원되는 시나리오를 나타냅니다.
 
 
   |  | BEK + KEK VM | BEK 전용 VM |
   | --- | --- | --- |
   | **관리되지 않는 VM**  | 예 | 예  |
   | **관리되는 VM**  | 예 | 예  |

## <a name="prerequisites"></a>필수 조건
* VM이 [Azure Disk Encryption](../security/azure-security-disk-encryption.md)을 사용하여 암호화되었습니다. 

* [백업 환경 준비](backup-azure-arm-vms-prepare.md)의 단계를 따라 Recovery Services 자격 증명 모음이 만들어졌으며 저장소 복제가 설정되었습니다.

* Backup에 암호화된 VM에 대한 키 및 비밀이 담긴 [키 자격 증명 모음 액세스 권한](#provide-permissions-to-backup)이 부여되었습니다.

## <a name="backup-encrypted-vm"></a>암호화된 VM 백업
다음 단계를 사용하여 백업 목표를 설정하고, 정책을 정의하며, 항목을 구성하고, 백업을 트리거합니다.

### <a name="configure-backup"></a>백업 구성
1. 이미 Recovery Services 자격 증명 모음이 열려 있으면 다음 단계로 진행합니다. Recovery Services 자격 증명 모음이 열려 있지 않지만 Azure Portal에 있는 경우 **모든 서비스**를 선택합니다.

   a. 리소스 목록에서 **Recovery Services**를 입력합니다.

   나. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services 자격 증명 모음**이 표시되면 이를 선택합니다.

      ![Recovery Services 자격 증명 모음](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    다. Recovery Services 자격 증명 모음의 목록이 표시됩니다. 목록에서 자격 증명 모음을 선택합니다.

     선택한 자격 증명 모음 대시보드가 열립니다.
2. 자격 증명 모음 아래쪽에 나타나는 항목 목록에서 **백업**을 선택하여 암호화된 VM 백업을 시작합니다.

      ![백업 블레이드 ](./media/backup-azure-vms-encryption/select-backup.png)
3. **백업** 타일에서 **백업 목표**를 선택합니다.

      ![시나리오 블레이드](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. **작업이 실행되는 위치**에서 **Azure**를 선택합니다. **백업할 항목**에서 **가상 컴퓨터**를 선택합니다. 그런 다음 **확인**을 선택합니다.

   ![시나리오 블레이드 열기](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. **백업 정책 선택**에서 자격 증명 모음에 적용할 백업 정책을 선택합니다. 그런 다음 **확인**을 선택합니다.

      ![백업 정책 선택](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    기본 정책의 세부 정보가 나열됩니다. 정책을 만들려는 경우 드롭다운 목록에서 **새로 만들기**를 선택합니다. **확인**을 선택하면 백업 정책이 자격 증명 모음과 연결됩니다.

6. 지정된 정책과 연결할 암호화된 VM을 선택하고 **확인**을 선택합니다.

      ![암호화된 VM 선택](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. 이 페이지에서는 선택한 암호화된 VM에 연결된 키 자격 증명 모음에 대한 메시지를 보여 줍니다. 백업을 사용하려면 키 자격 증명 모음의 키와 비밀에 대한 읽기 전용 액세스 권한이 있어야 합니다. 이 권한을 사용하여 연결된 VM과 함께 키와 비밀을 백업합니다.<br>
**멤버 사용자**인 경우, 백업 사용 프로세스는 원활하게 키 자격 증명 모음에 대한 액세스를 가져와서 사용자 간섭을 물어보지 않고도 암호화된 VM을 백업할 수 있게 됩니다.

   ![암호화된 VM 메시지](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   **게스트 사용자**인 경우 백업 서비스에 대한 권한이 있어야 백업을 수행하는 데 필요한 키 자격 증명 모음에 액세스할 수 있습니다. [다음 섹션에 설명된 단계](#provide-permissions-to-backup)를 수행하여 이러한 권한을 제공할 수 있습니다.

   ![암호화된 VM 메시지](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    이제 자격 증명 모음의 모든 설정을 정의했으므로 페이지 아래쪽의 **백업 사용**을 선택합니다. **백업 사용**은 정책을 자격 증명 모음 및 VM에 배포합니다.
  
8. 다음 준비 단계는 VM 에이전트를 설치하거나 VM 에이전트가 설치되었는지 확인하는 단계입니다. 똑같이 하려면 [백업 환경 준비](backup-azure-arm-vms-prepare.md)의 단계를 따릅니다.

### <a name="trigger-a-backup-job"></a>백업 작업 트리거
백업 작업을 트리거하려면 [Recovery Services 자격 증명 모음에 Azure VM 백업](backup-azure-arm-vms.md)의 단계를 따릅니다.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>암호화를 사용하도록 설정한 상태로 이미 백업한 VM 백업 진행  
Recovery Services 자격 증명 모음에서 VM이 이미 백업되었으며 나중에 암호화가 설정된 경우 백업이 계속될 수 있게 Backup에 Key Vault 액세스 권한을 부여해야 합니다. [다음 섹션의 단계](#provide-permissions-to-azure-backup)를 사용하여 이러한 권한을 제공할 수 있습니다. 또는 [PowerShell 설명서](backup-azure-vms-automation.md)의 "백업 사용" 섹션에 있는 PowerShell 단계를 따릅니다. 

## <a name="provide-permissions-to-backup"></a>Backup에 사용 권한 제공
키 자격 증명 모음에 액세스하고 암호화된 VM의 백업을 수행하기 위해 다음 단계를 사용하여 Backup에 관련 사용 권한을 제공합니다.
1. **모든 서비스**를 선택하고 **Key Vault**를 검색합니다.

    ![Key Vault](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Key Vault 목록에서 백업해야 하는 암호화된 VM에 관련된 키 자격 증명 모음을 선택합니다.

     ![키 자격 증명 모음 선택](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. **액세스 정책**을 선택하고 **새로 추가**를 선택합니다.

    ![새로 추가](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. **보안 주체 선택**을 선택하고 검색 상자에 **백업 관리 서비스**를 입력합니다. 

    ![백업 서비스 검색](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. **백업 관리 서비스**를 선택하고 **선택** 단추를 클릭합니다.

    ![백업 서비스 선택](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. **템플릿에서 구성(선택 사항)** 에서 **Azure Backup**을 선택합니다. **키 권한** 및 **비밀 권한**에서 필요한 권한이 미리 입력됩니다. VM이 **BEK만** 사용하여 암호화되는 경우 비밀에 대한 권한이 필요하므로 **키 권한**에 대한 선택 항목을 제거해야 합니다.

    ![Azure Backup 선택](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. **확인**을 선택합니다. **백업 관리 서비스**가 **액세스 정책**에 추가됩니다. 

    ![액세스 정책](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. **저장**을 선택하여 Backup에 필요한 권한을 제공합니다.

    ![백업 액세스 정책](./media/backup-azure-vms-encryption/save-access-policy.png)

권한이 성공적으로 주어지면 암호화된 VM에 백업을 사용하도록 진행할 수 있습니다.

## <a name="restore-an-encrypted-vm"></a>암호화된 VM 복원
암호화된 VM을 복원하려면 먼저 [VM 복원 구성 선택](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)의 "백업된 디스크 복원" 섹션의 단계를 따라 디스크를 복원합니다.  그런 후에 다음 옵션 중 하나를 사용할 수 있습니다.

* [복원된 디스크에서 VM 만들기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)의 PowerShell 단계를 따라 복원된 디스크에서 전체 VM을 만듭니다.
* 또는 [템플릿을 사용하여 복원된 VM을 사용자 지정](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)하여 복원된 디스크에서 VM을 만듭니다. 템플릿은 2017년 4월 26일 이후에 만들어진 복원 지점에 대해서만 사용할 수 있습니다.

## <a name="troubleshooting-errors"></a>문제 해결 오류
| 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
|Backup | Backup에 암호화된 VM의 백업을 위한 Key Vault에 대한 충분한 권한이 없습니다. | [이전 섹션의 단계](#provide-permissions-to-azure-backup)에 따라 이러한 권한을 Backup에 제공해야 합니다. 또는 PowerShell 설명서의 [ AzureRM.RecoveryServices.Backup cmdlet을 사용하여 가상 머신 백업](backup-azure-vms-automation.md#back-up-azure-vms)에서 “보호 사용” 섹션의 PowerShell 단계를 따를 수 있습니다. |  
| 복원 |이 VM과 연결된 키 자격 증명 모음이 없기 때문에 이 암호화된 VM을 복원할 수 없습니다. |키 자격 증명 모음을 만들려면 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 사용합니다. 없는 경우 이를 복원하려면 [Azure Backup으로 키 자격 증명 모음 키 및 비밀 복원](backup-azure-restore-key-secret.md)을 참조하여 키와 비밀을 복원합니다. |
| 복원 |이 VM과 연결된 키와 비밀이 없기 때문에 이 암호화된 VM을 복원할 수 없습니다. |없는 경우 이를 복원하려면 [Azure Backup으로 키 자격 증명 모음 키 및 비밀 복원](backup-azure-restore-key-secret.md)을 참조하여 키와 비밀을 복원합니다. |
| 복원 |Backup은 구독의 리소스에 액세스할 수 있는 권한이 없습니다. |앞에서 설명한 대로 [VM 복원 구성 선택](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)의 "백업된 디스크 복원" 섹션의 단계를 따라 먼저 디스크를 복원합니다.  그런 후에 PowerShell을 사용하여 [복원된 디스크에서 VM을 만듭니다](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
