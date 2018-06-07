---
title: Azure Disk Encryption 문제 해결| Microsoft Docs
description: 이 문서에서는 Windows 및 Linux IaaS VM용 Microsoft Azure Disk Encryption에 대한 문제 해결 팁을 제공합니다.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: DevTiw
ms.openlocfilehash: 4bc1f7cf64a2a08215cd82d8e72e40eba8db3093
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption 문제 해결 가이드

이 가이드는 조직에서 Azure Disk Encryption을 사용하고 디스크 암호화 관련 문제를 해결하기 위한 지침이 필요한 IT 전문가, 정보 보안 분석가 및 클라우드 관리자를 위한 것입니다.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS 디스크 암호화 문제 해결

Linux OS(운영 체제) 디스크 암호화에서는 전체 디스크 암호화 프로세스를 실행하기 전에 OS 드라이브를 분리해야 합니다. 드라이브를 분리할 수 없는 경우 "... 후 분리하지 못했습니다." 라는 오류 메시지가 발생할 수 있습니다.

이 오류는 지원되는 재고 갤러리 이미지에서 수정되거나 변경된 대상 VM 환경에서 OS 디스크 암호화를 시도할 때 발생할 수 있습니다. OS 확장을 분리하는 확장의 기능을 방해할 수 있는 지원되는 이미지로부터의 편차 예는 다음과 같습니다.
- 지원되는 파일 시스템 또는 파티션 구성표와 더 이상 일치하지 않는 사용자 지정 이미지
- SAP, MongoDB, Apache Cassandra 및 Docker와 같은 대규모 응용 프로그램이 암호화하기 전에 OS에 설치되고 실행되는 경우 지원되지 않습니다.  Azure Disk Encryption은 디스크 암호화에서 OS 드라이브를 준비할 때 필요한 대로 이러한 프로세스를 안전하게 종료할 수 없습니다.  OS 드라이브에 대한 오픈 파일 핸들을 보유하는 활성 프로세스가 있는 경우 OS 드라이브는 탑재될 수 없습니다. 따라서 OS 드라이브를 암호화하는 데 실패합니다. 
- 암호화가 활성화된 시간에 근접하여 실행하는 사용자 지정 스크립트 또는 암호화 프로세스 중에 다른 변경 작업이 VM에서 수행되고 있는 경우 이 충돌은 Azure Resource Manager 템플릿에서 동시에 실행하도록 여러 확장을 정의하거나 사용자 지정 스크립트 확장 또는 다른 작업을 디스크 암호화와 동시에 실행할 때 발생할 수 있습니다. 이러한 단계를 직렬화하고 격리하면 문제가 해결될 수 있습니다.
- 암호화를 활성화하기 전에 SELinux(Security Enhanced Linux)가 비활성화되지 않았으므로 분리 단계가 실패합니다. 암호화가 완료되면 SELinux를 다시 활성화할 수 있습니다.
- OS 디스크는 LVM(논리 볼륨 관리자) 구성표를 사용합니다. 제한된 LVM 데이터 디스크 지원은 사용할 수 있지만, LVM OS 디스크는 사용할 수 없습니다.
- 최소 메모리 요구 사항이 충족되지 않습니다(OS 디스크 암호화에 권장되는 메모리 크기는 7GB임).
- 데이터 드라이브가 /mnt/ 디렉터리 또는 다른 디렉터리(예: /mnt/data1, /mnt/data2, /data3 + /data3/data4) 아래에 재귀적으로 탑재되었습니다.
- Linux에 대한 다른 Azure Disk Encryption [필수 구성 요소](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)가 충족되지 않습니다.

## <a name="unable-to-encrypt"></a>암호화할 수 없음

경우에 따라 Linux 디스크 암호화가 "OS 디스크 암호화 시작됨" 상태에서 중단된 것으로 나타나고, SSH가 비활성화됩니다. 암호화 프로세스를 재고 갤러리 이미지에 대해 완료하는 데 3-16시간이 걸릴 수 있습니다. 다중 테라바이트 크기 데이터 디스크가 추가될 경우 이 프로세스는 며칠이 걸릴 수도 있습니다.

Linux OS 디스크 암호화 시퀀스는 OS 드라이브를 일시적으로 탑재 해제합니다. 그런 다음 암호화된 상태로 다시 탑재하기 전에 전체 OS 디스크의 블록 단위로 암호화를 수행합니다. Windows의 Azure Disk Encryption과 달리 Linux 디스크 암호화는 암호화를 진행하는 동안 VM을 동시에 사용할 수 없습니다. VM의 성능 특성은 암호화를 완료하는 데 필요한 시간에 큰 차이를 만들 수 있습니다. 이러한 특성은 디스크 크기 및 저장소 계정이 표준 또는 프리미엄(SSD) 저장소인지 여부를 포함합니다.

암호화 상태를 확인하기 위해 [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) 명령에서 반환된 **ProgressMessage** 필드를 폴링합니다. OS 드라이브가 암호화되는 동안 VM은 서비스 상태가 되고 진행 중인 프로세스의 중단을 방지하기 위해 SSH를 비활성화합니다. **EncryptionInProgress** 메시지는 암호화가 진행 중인 동안 대부분의 시간에 대해 보고합니다. 몇 시간 후에 **VMRestartPending** 메시지는 VM을 다시 시작하라는 프롬프트를 표시합니다. 예: 


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

VM을 다시 부팅하라는 메시지가 표시된 후, VM이 다시 시작된 후 재부팅 및 대상에서 수행되어야 하는 마지막 단계를 위해 2~3분 정도 기다려야 합니다. 암호화가 마지막으로 완료되는 경우 상태 메시지가 변경됩니다. 이 메시지가 제공되면 암호화된 OS 드라이브가 사용할 준비가 되었으며 VM을 다시 사용할 수 있게 됩니다.

다음과 같은 경우에 스냅숏 또는 암호화 직전에 수행된 백업으로 VM을 다시 복원하는 것이 좋습니다.
   - 이전에 설명된 다시 부팅 시퀀스가 발생하지 않는 경우
   - 부팅 정보, 프로세스 메시지 또는 기타 오류 표시기가 이 프로세스 도중에 OS 암호화에 실패했다고 보고하는 경우 메시지의 예는 이 가이드에 설명되어 있는 "분리하지 못했습니다." 오류입니다.

다음 시도를 수행하기 전에 VM의 특성을 다시 평가하고 모든 필수 조건이 충족되는지 확인합니다.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Disk Encryption 문제 해결
방화벽, 프록시 요구 사항 또는 NSG(네트워크 보안 그룹) 설정으로 연결이 제한되면 필요한 작업을 수행할 수 있는 확장의 기능이 중단될 수 있습니다. 이로 인해 "VM에서 사용할 수 없는 확장 상태"와 같은 상태 메시지가 표시됩니다. 예상된 시나리오에서 암호화가 완료되지 못할 수 있습니다. 다음 섹션에서는 조사할 수 있는 몇 가지 일반적인 방화벽 문제가 나와 있습니다.

### <a name="network-security-groups"></a>네트워크 보안 그룹
적용되는 모든 네트워크 보안 그룹 설정은 끝점에서도 디스크 암호화에 대해 문서화된 네트워크 구성 [필수 조건](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites)을 충족해야 합니다.

### <a name="azure-key-vault-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Key Vault
VM에서 키 자격 증명 모음에 액세스할 수 있어야 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) 팀이 유지 관리하는 방화벽 뒤에서 키 자격 증명 모음에 액세스하는 방법에 대한 지침을 참조하세요.

### <a name="linux-package-management-behind-a-firewall"></a>방화벽 뒤에 있는 Linux 패키지 관리

런타임 시 Linux용 Azure Disk Encryption은 대상 배포판의 패키지 관리 시스템을 사용하여 암호화를 사용하기 전에 필요한 필수 구성 요소를 설치합니다. 방화벽 설정으로 인해 VM에서 이러한 구성 요소를 다운로드하여 설치할 수 없으면 뒤이어서 실패하게 됩니다. 이 패키지 관리 시스템을 구성하는 단계는 배포에 따라 달라질 수 있습니다. Red Hat에서 프록시가 필요한 경우 subscription-manager와 yum이 올바르게 설정되었는지 확인해야 합니다. 자세한 내용은 [구독 관리자 및 yum 문제를 해결하는 방법](https://access.redhat.com/solutions/189533)을 참조하세요.  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core 문제 해결

Windows Server 2016 Server Core에서 bdehdcfg 구성 요소는 기본적으로 사용할 수 없습니다. 이 구성 요소는 Azure Disk Encryption에 필요합니다. VM의 수명 동안 한 번만 수행되는 OS 볼륨에서 시스템 볼륨을 분할하는 데 사용됩니다. 이러한 이진 파일은 이후 암호화 작업 중 필요하지 않습니다.

이 문제를 해결하려면 Windows Server 2016 Data Center VM의 다음 4개 파일을 Server Core의 동일한 위치에 복사합니다.

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. 다음 명령을 입력합니다.

   ```
   bdehdcfg.exe -target default
   ```

   3. 이 명령은 550MB 시스템 파티션을 만듭니다. 시스템 다시 부팅합니다.

   4. DiskPart를 사용하여 볼륨을 확인한 다음 계속합니다.  

예: 

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="troubleshooting-encryption-status"></a>암호화 상태 문제 해결

예상된 암호화 상태가 포털에서 보고되는 것과 일치하지 않는 경우 [암호화 상태가 Azure 관리 포털에 올바르게 표시되지 않습니다.](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) 지원 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 일반적인 Azure Disk Encryption 문제와 해당 문제 해결 방법에 대해 자세히 알아보았습니다. 이 서비스 및 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Security Center에서 디스크 암호화 적용](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure 가상 머신 암호화](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [휴지 상태의 Azure 데이터 암호화](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
