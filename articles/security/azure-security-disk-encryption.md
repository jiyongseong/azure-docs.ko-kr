---
title: Windows 및 Linux IaaS VM용 Azure Disk Encryption | Microsoft Docs
description: 이 문서에서는 Windows 및 Linux IaaS VM용 Microsoft Azure Disk Encryption에 대한 개요를 제공합니다.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: devtiw
ms.openlocfilehash: 813124ae7c0dd76a27dcbaea6f0d7aa19bc1e49c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Windows 및 Linux IaaS VM용 Azure 디스크 암호화
Microsoft Azure는 데이터 프라이버시, 데이터 독립성을 보장하기 위해 노력하고 있으며 암호화 키를 암호화, 제어 및 관리하고 데이터 액세스를 제어 및 감사하는 광범위한 고급 기술을 통해 Azure 호스팅 데이터를 제어할 수 있도록 합니다. 또한 Azure 고객에게 비즈니스 요구에 가장 잘 맞는 솔루션을 선택할 수 있는 유연성을 제공합니다. 이 문서에는 "Windows 및 Linux IaaS VM용 Azure 디스크 암호화"라는 새로운 기술 솔루션을 소개하여 조직의 보안 및 규정 준수 약정에 따라 데이터를 보호하도록 합니다. 이 문서에서는 지원되는 시나리오와 사용자 환경을 비롯하여 Azure 디스크 암호화 기능을 사용하는 방법에 대한 자세한 지침을 제공합니다.

> [!NOTE]
> 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다.

## <a name="overview"></a>개요
Azure Disk Encryption은 Windows 및 Linux IaaS 가상 머신 디스크를 암호화할 수 있도록 하는 새로운 기능입니다. Azure Disk Encryption은 업계 표준인 Windows의 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다. 또한 고객이 Key Vault 구독에서 디스크 암호화 키 및 암호를 관리 및 제어할 수 있도록 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)에 통합되어 있습니다. 이 솔루션은 가상 머신 디스크에 있는 모든 데이터가 미사용 시 Azure Storage에 암호화되도록 합니다.

Windows 및 Linux IaaS VM용 Azure Disk Encryption은 이제 Standard VM 및 Premium Storage를 사용하는 VM을 위한 모든 Azure 공용 지역 및 AzureGov 지역에서 **일반 공급**으로 제공됩니다.

### <a name="encryption-scenarios"></a>암호화 시나리오
Azure 디스크 암호화 솔루션은 다음의 고객 시나리오를 지원합니다.

* 미리 암호화된 VHD 및 암호화 키에서 만든 새 IaaS VM에서 암호화 사용
* 지원되는 Azure 갤러리 이미지에서 만든 새 IaaS VM에서 암호화 사용
* Azure에서 실행 중인 기존 IaaS VM에 대해 암호화 사용
* Windows IaaS VM에서 암호화 사용 안 함
* Linux IaaS VM에 대한 데이터 드라이브에서 암호화 사용 안 함
* 관리 디스크 VM의 암호화 사용
* 기존의 암호화된 Premium Storage VM 및 비 Premium Storage VM의 암호화 설정 업데이트
* 암호화된 VM 백업 및 복원

이 솔루션은 Microsoft Azure에서 사용되도록 설정될 경우 IaaS VM에 대해 다음 시나리오를 지원합니다.

* Azure Key Vault와 통합
* 표준 계층 VM: [A, D, DS, G, GS, F 등 시리즈 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
* 지원되는 Azure 갤러리 이미지의 Windows 및 Linux IaaS VM과 관리 디스크 VM에서 암호화 사용
* Windows IaaS VM 및 관리 디스크 VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* Linux IaaS VM 및 관리 디스크 VM에 대한 데이터 드라이브에서 암호화 사용 안 함
* Windows 클라이언트 OS를 실행하는 IaaS VM에서 암호화 사용
* 탑재 경로가 있는 볼륨에서 암호화 사용
* mdadm을 사용하여 디스크 스트라이프(RAID)로 구성된 Linux VM에서 암호화 사용
* 데이터 디스크에 대해 LVM을 사용하여 Linux VM에서 암호화 사용
* OS 및 데이터 디스크용 Linux LVM 7.3에서 암호화 사용 
* Storage 공간으로 구성된 Windows VM에서 암호화 사용
* 기존의 암호화된 Premium Storage VM 및 비 Premium Storage VM의 암호화 설정 업데이트
* KEK(키 암호화 키)를 사용하는 시나리오와 사용하지 않는 시나리오 모두에서 암호화된 VM의 백업 및 복원
* 모든 Azure 공용 지역 및 AzureGov 지역 지원됨

이 솔루션은 다음 시나리오, 기능 및 기술을 지원하지 않습니다.

* 기본 계층 IaaS VM
* Linux IaaS VM에 대한 OS 드라이브에서 암호화 사용 안 함
* OS 드라이브가 Linux Iaas VM에 대해 암호화되는 경우 데이터 드라이브에 암호화 사용 안 함
* 클래식 VM 만들기 방법을 사용하여 만든 IaaS VM
* Windows 및 Linux IaaS VM 고객 사용자 지정 이미지에 암호화를 사용하는 기능은 지원되지 않습니다.
* 온-프레미스 키 관리 서비스와의 통합
* Azure 파일(공유 파일 시스템), NFS(네트워크 파일 시스템), 동적 볼륨, 소프트웨어 기반 RAID 시스템으로 구성된 Windows VM

### <a name="encryption-features"></a>암호화 기능
Azure IaaS VM에 대한 Azure Disk Encryption을 사용하도록 설정하고 배포할 때 제공된 구성에 따라 다음 기능이 활성화됩니다.

* 사용자 저장소에서 미사용 부팅 볼륨을 보호하기 위해 OS 볼륨 암호화
* 사용자 저장소에서 미사용 데이터 볼륨을 보호하기 위해 데이터 볼륨 암호화
* Windows IaaS VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* Linux IaaS VM에 대한 데이터 드라이브에서 암호화 사용 안 함(OS 드라이브가 암호화되지 않는 경우에만)
* 사용자의 Key Vault 구독에서 암호화 키 및 비밀 보호
* 암호화된 IaaS VM의 암호화 상태 보고
* IaaS 가상 머신에서 디스크 암호화 구성 설정 제거
* Azure Backup 서비스로 암호화된 VM 백업 및 복원

Windows 및 Linux 솔루션용 IaaS VM에 대한 Azure Disk Encryption에는 다음 내용이 포함됩니다.

* Windows용 디스크 암호화 확장
* Linux용 디스크 암호화 확장
* 디스크 암호화 PowerShell cmdlet
* 디스크 암호화 Azure CLI(명령줄 인터페이스) cmdlet
* 디스크 암호화 Azure Resource Manager 템플릿

Azure Disk Encryption은 Windows 또는 Linux OS를 실행하는 IaaS VM에서 지원됩니다. 지원되는 운영 체제에 대한 자세한 내용은 "필수 조건" 섹션을 참조하세요.

> [!NOTE]
> Azure Disk Encryption으로 VM 디스크를 암호화하는 작업에 대한 추가 요금은 없습니다.

### <a name="value-proposition"></a>가치 제안
Azure Disk Encryption 관리 솔루션을 적용할 때 다음 비즈니스 요구 사항을 충족할 수 있습니다.

* 업계 표준 암호화 기술을 사용하여 조직의 보안 및 규정 준수 요구 사항을 충족할 수 있으므로 미사용 상태에서 IaaS VM이 보호됩니다.
* IaaS VM은 고객이 제어하는 키 및 정책에 따라 부팅되며 Key Vault에서 이러한 사용을 감사할 수 있습니다.

### <a name="encryption-workflow"></a>암호화 워크플로
Windows 및 Linux VM용 디스크 암호화를 사용하도록 설정하려면 다음을 수행합니다.

1. 위의 암호화 시나리오 중에서 암호화 시나리오를 선택합니다.
2. 고객은 Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 통해 디스크 암호화를 사용하도록 선택하고 암호화 구성을 지정합니다.

   * 고객 암호화 VHD 시나리오의 경우, 암호화된 VHD를 저장소 계정에, 암호화 키 자료를 Key Vault에 업로드합니다. 그런 다음 암호화 구성을 제공하여 새로운 IaaS VM에서 암호화를 사용하도록 합니다.
   * Marketplace에서 만든 새 VM과 Azure에서 이미 실행 중인 기존의 VM에 대해 암호화 구성을 제공하여 IaaS VM에서 암호화를 사용하도록 합니다.

3. Azure 플랫폼에 대한 액세스를 부여하여 Key Vault에서 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 읽고 IaaS VM에서 암호화를 사용하도록 합니다.

4. 암호화 키 자료를 Key Vault에 기록하기 위한 Azure Active Directory(Azure AD) 응용 프로그램 ID를 제공합니다. 이렇게 하면 2단계에서 설명된 시나리오인 경우 IaaS VM에서 암호화가 가능합니다.

5. Azure에서는 암호화 및 Key Vault 구성으로 VM 서비스 모델을 업데이트하고 암호화된 VM을 설정합니다.

 ![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>암호 해독 워크플로
IaaS VM용 디스크 암호화를 사용하지 않도록 설정하려면 다음 대략적인 단계를 완료합니다.

1. Azure Disk Encryption Resource Manager 템플릿 또는 PowerShell cmdlet을 통해 Azure에서 실행 중인 IaaS VM에 암호화(암호 해독)를 사용하지 않도록 선택하고 암호 해독 구성을 지정할 수 있습니다.

 이 단계는 실행 중인 Windows IaaS VM에서 OS 또는 데이터 볼륨이나 둘 모두의 암호화를 사용하지 않습니다. 그러나 이전 섹션에 나와 있는 것처럼 Linux에 대해 OS 디스크를 사용하지 않도록 설정하는 것은 지원되지 않습니다. 암호 해독 단계는 OS 디스크가 암호화되지 않는 한 Linux VM의 데이터 드라이브에만 허용됩니다.
2. Azure에서는 VM 서비스 모델을 업데이트하고 IaaS VM은 암호가 해독되었다고 표시합니다. VM의 콘텐츠는 더 이상 미사용 상태에서 암호화되지 않습니다.

> [!NOTE]
> 암호화 사용 안 함 작업은 Key Vault 및 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 삭제하지 않습니다.
 > Linux OS 디스크 암호화는 사용하지 않도록 설정할 수 없습니다. 암호 해독 단계는 Linux VM에서 데이터 드라이브에만 허용됩니다.
OS 드라이브가 암호화되는 경우 Linux에 데이터 디스크 암호화를 사용하지 않도록 설정할 수 없습니다.

## <a name="prerequisites"></a>필수 조건
"개요" 섹션에 설명된 지원되는 시나리오에 대해 Azure IaaS VM에서 Azure Disk Encryption를 사용하도록 설정하기 전에 다음 필수 조건을 참조하세요.

* 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.
* Azure Disk Encryption은 다음 Windows Server 버전: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에서 지원됩니다.
* Azure Disk Encryption은 다음 Windows 클라이언트 버전: Windows 8 클라이언트 및 Windows 10 클라이언트에서 지원됩니다.

> [!NOTE]
> Windows Server 2008 R2의 경우 Azure에서 암호화를 사용하도록 설정하기 전에 .NET Framework 4.5를 설치해야 합니다. 선택적 업데이트인 Windows Server 2008 R2 x64 기반 시스템용 Microsoft .NET Framework 4.5.2([KB2901983](https://support.microsoft.com/kb/2901983))를 설치하여 Windows 업데이트에서 설치할 수 있습니다.

* Azure Disk Encryption은 특정 Azure 갤러리 기반 Linux 서버 배포판 및 버전에서 지원됩니다.  현재 지원되는 버전 목록은 [Azure Disk Encryption FAQ](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq)를 참조하세요.

* Azure Disk Encryption은 Key Vault 및 VM이 동일한 Azure 하위 지역 및 구독에 있어야 합니다.

> [!NOTE]
> 별도 하위 지역에서 리소스를 구성하면 Azure Disk Encryption 기능 사용 시 오류가 발생합니다.

* Azure Disk Encryption을 위한 Key Vault를 설정하고 구성하려면 이 문서의 *필수 조건* 섹션에서 **Azure Disk Encryption을 위한 Key Vault 설정 및 구성** 섹션을 참조하세요.
* Azure Active Directory에 Azure Disk Encryption을 위한 Azure AD 응용 프로그램을 설정하고 구성하려면 이 문서의 *필수 조건* 섹션에서 **Azure Active Directory에서 Azure AD 응용 프로그램 설치**를 참조하세요.
* Azure AD 응용 프로그램에 대한 Key Vault 액세스 정책을 설정하고 구성하려면 이 문서의 *필수 조건* 섹션에서 **Azure AD 응용 프로그램에 대한 Key Vault 액세스 정책 설정**을 참조하세요.
* 미리 암호화된 Windows VHD를 준비하려면 *부록*에서 **미리 암호화된 Windows VHD 준비**를 참조하세요.
* 미리 암호화된 Windows VHD를 준비하려면 *부록*에서 **미리 암호화된 Windows VHD 준비** 섹션을 참조하세요.
* Azure 플랫폼은 가상 머신 OS 볼륨을 부팅하고 해독할 때 가상 머신에서 사용할 수 있도록 Key Vault에서 암호화 키 또는 암호에 액세스해야 합니다. Azure 플랫폼에 권한을 부여하려면 Key Vault에서 **EnabledForDiskEncryption** 속성을 설정합니다. 자세한 내용은 부록에서**Azure Disk Encryption을 위한 Key Vault 설정 및 구성**을 참조하세요.
* Key Vault 비밀 및 KEK URL 버전을 지정해야 합니다. Azure에서 이 버전 관리 제한을 적용합니다. 유효한 비밀과 KEK URL은 다음 예제를 참조하세요.

  * 올바른 비밀 URL의 예제: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 올바른 KEK URL의 예제: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption은 Key Vault 비밀 및 KEK URL의 일부로 포트 번호 지정을 지원하지 않습니다. 지원 또는 지원되지 않는 Key Vault URL에 대한 예는 다음을 참조하세요.

  * 허용되지 않는 키 자격 증명 모음 URL  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 허용 가능한 키 자격 증명 모음 URL  *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption 기능을 사용하도록 설정하려면 IaaS VM이 다음 네트워크 끝점 구성 요구 사항을 충족해야 합니다.
  * Key Vault에 연결할 토큰을 얻으려면 IaaS VM에서 Azure Active Directory 끝점인 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  * 암호화 키를 고객 Key Vault에 쓰려면 IaaS VM에서 Key Vault 끝점에 연결할 수 있어야 합니다.
  * IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 끝점 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.

  > [!NOTE]
  > 보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다.
  >
  >방화벽 뒤에 있는 Azure Key Vault를 구성 및 액세스하려면(https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Azure PowerShell SDK의 최신 버전을 사용하여 Azure 디스크 암호화를 구성합니다. 최신 버전의 [Azure PowerShell 릴리스](https://github.com/Azure/azure-powershell/releases) 다운로드

 > [!NOTE]
  > Azure Disk Encryption은 [Azure PowerShell SDK 버전 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016)에서 지원되지 않습니다. Azure PowerShell 1.1.0을 사용하는 경우 관련된 오류가 나타나면 [Azure PowerShell 1.1.0과 관련된 Azure Disk Encryption 오류](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx)를 참조하세요.

* Azure CLI 명령을 실행하고 Azure 구독에 연결하려면 먼저 Azure CLI를 설치해야 합니다.
  * Azure CLI를 설치하고 Azure 구독에 연결하려면 [Azure CLI 설치 및 구성 방법](../cli-install-nodejs.md)을 참조하세요.
  * Azure Resource Manager와 함께 Mac, Linux 및 Windows용 Azure CLI를 사용하려면 [Resource Manager 모드에서 Azure CLI 명령](../virtual-machines/azure-cli-arm-commands.md)을 참조하세요.

* 관리 디스크를 암호화할 때는 암호화를 사용하기 전에 Azure Disk Encryption 외부에 관리 디스크의 스냅숏을 만들고 디스크를 백업하는 것이 필수 조건입니다.  백업을 준비하지 않으면 암호화 중에 예기치 않은 오류가 발생하는 경우 복구 옵션 없이 디스크와 VM이 액세스할 수 없게 렌더링될 수 있습니다.  Set-AzureRmVMDiskEncryptionExtension은 현재 관리 디스크를 백업하지 않으며 -skipVmBackup 매개 변수가 지정되지 않은 경우 관리 디스크에 대해 사용될 경우 오류가 발생합니다.  이 매개 변수는 Azure Disk Encryption 외부에 아직 백업하지 않은 경우 사용하기에 안전하지 않습니다.   -skipVmBackup 매개 변수를 지정하면 cmdlet은 암호화되기 전에 관리 디스크를 백업하지 않습니다.  이러한 이유로 복구가 나중에 필요한 경우 Azure Disk Encryption을 사용하기 전에 관리 디스크 VM의 백업이 준비되어 있는지 확인하는 것이 필수 조건입니다.  
> [!NOTE]
 > 스냅숏 또는 백업이 Azure Disk Encryption 외부에 아직 만들어지지 않은 경우에는 -skipVmBackup 매개 변수를 사용해서는 안 됩니다. 

* Azure Disk Encryption 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 마십시오. "호환되는 TPM이 없이 BitLocker 허용"에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](https://technet.microsoft.com/library/ee706521)를 참조하세요.
* 사용자 지정 그룹 정책을 사용하는 도메인 가입 가상 머신의 Bitlocker 정책은 다음 설정을 포함해야 합니다. `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Bitlocker의 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 컴퓨터에서 새 정책을 적용하고 강제로 새 정책을 업데이트(gpupdate.exe /force)한 다음 다시 시작해야 합니다.  
* Azure AD 응용 프로그램을 만들고 Key Vault를 만들거나 기존 Key Vault를 설정하고 암호화를 사용하려면 [Azure Disk Encryption 필수 요소 PowerShell 스크립트](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)를 참조하세요.
* Azure CLI를 사용하여 디스크 암호화 필수 구성 요소를 구성하려면 [이 Bash 스크립트](https://github.com/ejarvi/ade-cli-getting-started)를 참조하세요.
* Azure Backup 서비스를 사용하여 암호화된 VM을 백업 및 복원하려는데, Azure Disk Encryption으로 암호화가 사용하도록 설정되어 있다면, Azure Disk Encryption 키 구성을 사용하여 VM을 암호화해야 합니다. Backup 서비스는 KEK 구성 및 KEK를 사용하지 않는 구성을 사용하여 암호화된 VM을 지원합니다. [Azure Backup 암호화로 암호화된 가상 머신을 백업 및 복원하는 방법](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)을 참조하세요.

* Linux OS 볼륨을 암호화할 때 프로세스가 끝나면 현재 VM을 다시 시작해야 합니다. 이 작업은 포털, PowerShell 또는 CLI를 통해 수행할 수 있습니다.   암호화의 진행률을 추적하려면 Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus에서 반환된 상태 메시지를 정기적으로 폴링합니다.  암호화가 완료되면 이 명령에 의해 반환되는 상태 메시지가 이를 나타냅니다. 예를 들어 "ProgressMessage: OS 디스크가 성공적으로 암호화되었으므로 VM을 재부팅하십시오."입니다. 이 시점에서 VM을 다시 시작하여 사용할 수 있습니다.  

* Linux용 Azure Disk Encryption을 사용하려면 암호화하기 전에 데이터 디스크에 Linux의 탑재된 파일 시스템이 있어야 합니다.

* 재귀적으로 탑재된 데이터 디스크는 Linux용 Azure Disk Encryption에서 지원하지 않습니다. 예를 들어 대상 시스템이 /foo/bar에 디스크를 탑재한 다음 /foo/bar/baz에 다른 디스크를 탑재한 경우 /foo/bar/baz의 암호화는 성공하지만 /foo/bar의 암호화는 실패합니다. 

* Azure Disk Encryption은 위에서 언급한 필수 조건을 충족하는 Azure 갤러리 지원 이미지에서만 지원됩니다. 고객 사용자 지정 이미지는 이러한 이미지에 있을 수 있는 사용자 지정 파티션 구성표 및 프로세스 동작으로 인해 지원되지 않습니다. 또한 초기에 필수 조건을 충족했지만 작성 후 수정된 갤러리 이미지 기반 VM도 호환되지 않을 수 있습니다.  이러한 이유로 Linux VM을 암호화하기 위한 권장 절차는 클린 갤러리 이미지에서 시작하여 VM을 암호화한 다음 필요에 따라 사용자 지정 소프트웨어 또는 데이터를 VM에 추가하는 것입니다.  

* Azure Disk Encryption 및 로컬 데이터 볼륨 - 암호화 키를 안전하게 보관하기 위한 Windows용 Bek 볼륨 및 Linux IaaS VM용 /mnt/azure_bek_disk. 이 디스크의 내용은 삭제하거나 편집하지 않아야 합니다. IaaS VM의 암호화 작업에 암호화 키가 필요하므로 이 디스크를 분리하지 않도록 합니다. 볼륨에 포함된 README 파일에 추가 세부 정보가 들어 있습니다.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Azure Active Directory에서 Azure AD 응용 프로그램 설정
Azure에서 실행 중인 VM에서 암호화를 사용하도록 설정해야 하는 경우 Azure Disk Encryption이 암호화 키를 생성하고 Key Vault에 기록합니다. Key Vault에서 암호화 키를 관리하려면 Azure AD 인증이 필요합니다.

이런 목적으로 Azure AD 응용 프로그램을 만듭니다. 응용 프로그램을 등록하는 자세한 단계는 블로그 게시물 [Azure Key Vault - 단계별](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)의 "응용 프로그램 ID 가져오기" 섹션에서 확인할 수 있습니다. 이 게시물에는 Key Vault 설정 및 구성에 대한 유용한 예제도 다수 포함되어 있습니다. 인증을 위해 클라이언트 비밀 기반 인증 또는 클라이언트 인증서 기반 Azure AD 인증을 사용할 수 있습니다.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Azure AD에 대한 클라이언트 비밀 기반 인증
이어지는 섹션을 통해 Azure AD에 대한 클라이언트 비밀 기반 인증을 구성할 수 있습니다.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure AD 응용 프로그램 만들기
Azure AD 응용 프로그램을 만들려면 다음 PowerShell cmdlet을 사용합니다.

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId는 Azure AD ClientID이며 $aadClientSecret은 나중에 Azure Disk Encryption을 설정하는 데 사용할 클라이언트 비밀입니다. Azure AD 클라이언트 비밀을 적절하게 보호합니다.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Azure Portal에서 Azure AD 클라이언트 ID 및 비밀 설정
Azure Portal을 사용하여 Azure AD 클라이언트 ID 및 비밀을 설정할 수도 있습니다. 이 작업을 수행하려면 다음을 수행합니다.

1. **모든 서비스 > Azure Active Directory**를 선택합니다.

 ![Azure 디스크 암호화](./media/azure-security-disk-encryption/aad-service.png)

2. **앱 등록 > 새 응용 프로그램 등록**을 선택합니다.

 ![Azure 디스크 암호화](./media/azure-security-disk-encryption/aad-app-registration.png)

3. 요청된 정보를 제공하고, 응용 프로그램을 만듭니다.

 ![Azure 디스크 암호화](./media/azure-security-disk-encryption/aad-create-app.png)

4. 새로 만든 응용 프로그램을 선택하여 응용 프로그램 ID를 포함한 해당 속성을 봅니다.  응용 프로그램에 대한 키를 만들려면 **설정 > 키**를 선택하고, 키에 대한 설명 및 만료를 추가하고, **저장**을 클릭합니다.

 ![Azure 디스크 암호화](./media/azure-security-disk-encryption/aad-create-pw.png)

5. 생성된 암호 값을 복사하고 적절하게 보호합니다.

 ![Azure 디스크 암호화](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>기존 응용 프로그램 사용
다음 명령을 실행하려면 [Azure AD PowerShell 모듈](https://technet.microsoft.com/library/jj151815.aspx)을 가져와 사용합니다.

> [!NOTE]
> 새 PowerShell 창에서 다음 명령을 실행해야 합니다. 이러한 명령을 실행하는 데 Azure PowerShell 또는 Azure Resource Manager 창을 사용하지 마세요. 이러한 cmdlet이 MSOnline 모듈 또는 Azure AD PowerShell에 있으므로 이 방법을 사용하는 것이 좋습니다.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Azure AD에 대한 인증서 기반 인증
> [!NOTE]
> Azure AD 인증서 기반 인증은 현재 Linux VM에서 지원되지 않습니다.

이어지는 섹션에서는 Azure AD에 대한 인증서 기반 인증을 구성하는 방법을 보여 줍니다.

##### <a name="create-an-azure-ad-application"></a>Azure AD 응용 프로그램 만들기
Azure AD 응용 프로그램을 만들려면 다음 PowerShell cmdlet을 실행합니다.

> [!NOTE]
> 다음 `yourpassword` 문자열을 보안 암호로 바꾸고 암호를 보호합니다.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

이 단계를 마친 후 PFX 파일을 Key Vault에 업로드하고 인증서를 VM에 배포하는 데 필요한 액세스 정책을 사용하도록 설정합니다.

##### <a name="use-an-existing-azure-ad-application"></a>기존 Azure AD 응용 프로그램 사용
기존 응용 프로그램을 위한 인증서 기반 인증을 구성하는 경우 아래 표시된 PowerShell cmdlet을 사용합니다. 새 PowerShell 창에서 실행해야 합니다.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

이 단계를 마친 후 PFX 파일을 Key Vault에 업로드하고 인증서를 VM에 배포하는 데 필요한 액세스 정책을 사용하도록 설정합니다.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>PFX 파일을 Key Vault에 업로드
이 프로세스에 대한 자세한 설명은 [공식 Azure Key Vault 팀 블로그](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)를 참조하세요. 그러나 다음 PowerShell cmdlet만으로도 이 작업을 충분히 수행할 수 있습니다. Azure PowerShell 콘솔에서 실행해야 합니다.

> [!NOTE]
> 다음 `yourpassword` 문자열을 보안 암호로 바꾸고 암호를 보호합니다.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Key Vault의 인증서를 기존 VM에 배포
PFX 업로드를 완료한 후 다음과 같이 Key Vault의 인증서를 기존 VM에 배포합니다.
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Azure AD 응용 프로그램에 대한 Key Vault 액세스 정책 설정
Azure AD 응용 프로그램에 자격 증명 모음의 키 또는 암호에 액세스할 권한이 필요합니다. _–ServicePrincipalName_ 매개 변수 값으로 클라이언트 ID(응용 프로그램이 등록될 때 생성됨)를 사용하여 [`Set-AzureKeyVaultAccessPolicy`](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) cmdlet으로 응용 프로그램에 권한을 부여합니다. 자세한 내용은 블로그 게시물 [Azure Key Vault - 단계별](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)을 참조하세요. 다음은 PowerShell을 통해 이 작업을 수행하는 방법에 대한 예입니다.

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption에서는 Azure AD 클라이언트 응용 프로그램에 _WrapKey_ 및 _Set_ 권한과 같은 액세스 정책을 구성해야 합니다.

## <a name="terminology"></a>용어
이 기술에 사용되는 일반적인 용어를 이해하려면 다음 용어 표를 사용하세요.

| 용어 | 정의 |
| --- | --- |
| Azure AD | Azure AD는 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)입니다. Azure AD 계정은 Key Vault에서 비밀을 인증, 저장 및 검색하기 위한 필수 구성 요소입니다. |
| Azure Key Vault | Key Vault는 암호화 키 및 중요한 비밀을 안전하게 보호하는 FIPS(Federal Information Processing Standard) 유효성을 검사한 하드웨어 보안 모듈 기반의 암호화 키 관리 서비스입니다. 자세한 내용은 [Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| ARM | Azure 리소스 관리자 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)는 Windows IaaS VM에서 디스크 암호화를 설정하는 데 사용되는 업계에서 공인된 Windows 볼륨 암호화 기술입니다. |
| BEK | BitLocker 암호화 키는 OS 부팅 볼륨 및 데이터 볼륨을 암호화하는 데 사용됩니다. BitLocker 키는 Key Vault에서 비밀 보안 수단입니다. |
| CLI | [Azure 명령줄 인터페이스](../cli-install-nodejs.md)을 참조하세요. |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)는 Linux IaaS VM에서 디스크 암호화를 설정하는 데 사용되는 Linux 기반의 투명한 디스크 암호화 하위 시스템입니다. |
| KEK | 주요 암호화 키는 비밀을 보호하거나 래핑하는 데 사용할 수 있는 비대칭 키(RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| PS cmdlet | [Azure PowerShell cmdlet](/powershell/azure/overview)을 참조하세요. |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 Key Vault 설정 및 구성
Azure Disk Encryption을 통해 Key Vault에서 디스크 암호화 키 및 비밀을 보호합니다. Azure Disk Encryption에 대한 Key Vault를 설명하려면 다음 각 섹션의 단계를 완료합니다.

#### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기
Key Vault를 만들려면 다음 옵션 중 하나를 사용합니다.

* ["101-Key-Vault-Create" Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Azure PowerShell Key Vault cmdlet](/powershell/module/azurerm.keyvault/#key_vault)
* Azure 리소스 관리자
* [Key Vault를 보호](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)하는 방법

> [!NOTE]
> 구독에 대한 Key Vault 설정이 이미 있는 경우 다음 섹션으로 건너뜁니다.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>주요 암호화 키 설정(선택 사항)
BitLocker 암호화 키에 대한 추가 보안 계층으로 KEK를 사용하려는 경우 KEK를 Key Vault에 추가합니다. [`Add-AzureKeyVaultKey`](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) cmdlet을 사용하여 Key Vault에 주요 암호화 키를 만듭니다. 또한 온-프레미스 키 관리 HSM에서 KEK를 가져올 수도 있습니다. 자세한 내용은 [Key Vault 설명서](https://azure.microsoft.com/documentation/services/key-vault/)를 참조하세요.

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Azure Resource Manager로 이동하거나 Key Vault 인터페이스를 사용하여 KEK를 추가할 수 있습니다.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Key Vault 사용 권한 설정
Azure 플랫폼은 VM을 부팅하고 볼륨을 해독할 수 있도록 Key Vault의 암호화 키 또는 비밀에 액세스해야 합니다. Azure 플랫폼에 권한을 부여하려면 Key Vault PowerShell cmdlet을 사용하여 **EnabledForDiskEncryption** 속성을 설정합니다.

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

또한 [Azure Resource Explorer](https://resources.azure.com)에 방문하여 **EnabledForDiskEncryption** 속성을 설정할 수도 있습니다.

앞서 설명한 것처럼 Key Vault에 **EnabledForDiskEncryption** 속성을 설정해야 합니다. 그렇지 않으면 배포에 실패합니다.

여기 표시된 것처럼, Key Vault 인터페이스에서 Azure AD 응용 프로그램에 대한 액세스 정책을 설정할 수 있습니다.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

**고급 액세스 정책** 탭에서 Azure Disk Encryption에 대해 Key Vault가 활성화되어 있는지 확인합니다.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>디스크 암호화 배포 시나리오 및 사용자 환경
수많은 디스크 암호화 시나리오를 사용할 수 있으며 단계는 시나리오에 따라 다를 수 있습니다. 다음 섹션에서는 이러한 시나리오를 자세히 설명합니다.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Marketplace에서 만든 새 IaaS VM에서 암호화를 사용하도록 설정
[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)을 사용하여 Azure에서 Marketplace의 새 IaaS Windows VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭하고 **매개 변수** 블레이드에 암호화 구성을 입력하고 **확인**을 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 **만들기**를 클릭하여 새 IaaS VM에서 암호화를 사용하도록 설정합니다.

> [!NOTE]
> 이 템플릿은 Windows Server 2012 갤러리 이미지를 사용하는 새 암호화된 Windows VM을 만듭니다.

이 [Resource Manager 템플릿](https://aka.ms/fde-rhel)을 사용하여 200GB RAID-0 배열이 있는 새 IaaS RedHat Linux 7.2 VM에서 디스크 암호화를 사용하도록 설정할 수 있습니다. 템플릿을 배포한 후 [실행 중인 Linux VM에서 OS 드라이브 암호화](#encrypting-os-drive-on-a-running-linux-vm) 섹션에 설명된 대로 `Get-AzureRmVmDiskEncryptionStatus` cmdlet을 사용하여 VM 암호화 상태를 확인합니다. 컴퓨터에서 _VMRestartPending_ 상태를 반환하면 VM을 다시 시작합니다.

다음 표에 Azure AD 클라이언트 ID를 사용하는 Marketplace 시나리오에서 새 VM에 대한 Resource Manager 템플릿 매개 변수 목록이 나와 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| adminUserName | 가상 머신의 관리 사용자 이름 |
| adminPassword | 가상 머신의 관리 사용자 암호 |
| newStorageAccountName | OS 및 데이터 VHD를 저장할 저장소 계정의 이름 |
| vmSize | VM의 크기. 현재 표준 A, D 및 G 시리즈만 지원됩니다. |
| virtualNetworkName | VM NIC가 속해야 하는 VNet의 이름 |
| subnetName | VM NIC가 속해야 하는 VNet의 서브넷 이름 |
| AADClientID | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 응용 프로그램의 클라이언트 ID |
| AADClientSecret | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 응용 프로그램의 클라이언트 ID |
| keyVaultURL | BitLocker 키가 업로드될 Key Vault의 URL. cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`를 사용하여 가져올 수 있습니다. |
| keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL(선택 사항) |
| keyVaultResourceGroup | Key Vault의 리소스 그룹 |
| vmName | 암호화 작업을 수행할 VM의 이름. |

> [!NOTE]
> _KeyEncryptionKeyURL_은 선택적 매개 변수입니다. Key Vault에서 데이터 암호화 키(암호 비밀)에 대한 보안을 강화하기 위해 고유한 KEK를 만들 수 있습니다.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>고객 암호화 VHD 및 암호화 키에서 만든 새 IaaS VM에서 암호화 사용
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 다음 섹션에서는 Resource Manager 템플릿과 CLI 명령에 대해 자세히 설명합니다.

Azure에서 사용할 수 있는 미리 암호화된 이미지를 준비하는 방법은 다음 섹션의 지침을 따르세요. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.

* [사전에 암호화된 Windows VHD 준비](#preparing-a-pre-encrypted-windows-vhd)
* [사전에 암호화된 Linux VHD 준비](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Resource Manager 템플릿 사용
[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)을 사용하여 암호화된 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭하고 **매개 변수** 블레이드에 암호화 구성을 입력하고 **확인**을 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 **만들기**를 클릭하여 새 IaaS VM에서 암호화를 사용하도록 설정합니다.

다음 표에 암호화된 VHD에 대한 Resource Manager 템플릿 매개 변수 목록이 나와 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| newStorageAccountName | 암호화된 OS VHD를 저장할 저장소 계정의 이름. 이 저장소 계정은 VM과 동일한 리소스 그룹 및 동일한 위치에 이미 만들어져 있어야 합니다. |
| osVhdUri | 저장소 계정에서 OS VHD의 URI |
| osType | OS 제품 유형(Windows/Linux) |
| virtualNetworkName | VM NIC가 속해야 하는 VNet의 이름 이 이름은 VM과 동일한 리소스 그룹 및 동일한 위치에 이미 만들어져 있어야 합니다. |
| subnetName | VM NIC가 속해야 하는 VNet의 서브넷 이름 |
| vmSize | VM의 크기. 현재 표준 A, D 및 G 시리즈만 지원됩니다. |
| keyVaultResourceID | Azure Resource Manager에서 Key Vault 리소스를 식별하는 ResourceID. PowerShell cmdlet `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`를 사용하여 가져올 수 있습니다. |
| keyVaultSecretUrl | Key Vault에 설정된 디스크 암호화 키의 URL |
| keyVaultKekUrl | 생성된 디스크 암호화 키를 암호화하기 위한 주요 암호화 키의 URL |
| vmName | IaaS VM의 이름 |

#### <a name="using-powershell-cmdlets"></a>PowerShell cmdlet 사용
PowerShell cmdlet [`Set-AzureRmVMOSDisk`](/powershell/module/azurerm.compute/set-azurermvmosdisk)를 사용하여 암호화된 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다.  

#### <a name="using-cli-commands"></a>CLI 명령 사용
CLI 명령을 사용하여 이 시나리오에 대한 디스크 암호화를 사용하도록 설정하려면 다음 단계를 따르세요.

1. Key Vault에 대한 액세스 정책 설정:

   * **EnabledForDiskEncryption** 플래그 설정:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Azure AD 응용 프로그램에 Key Vault에 비밀을 쓸 수 있는 권한 설정:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. 기존 또는 실행 중인 VM에서 암호화를 사용하려면 다음을 입력합니다.

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. 암호화 상태 가져오기:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. 암호화된 VHD에서 새 VM에 암호화를 사용하도록 설정하려면 `azure vm create` 명령에 다음 매개 변수를 사용합니다.

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Azure에서 기존 또는 실행 중인 IaaS Windows VM에서 암호화 사용
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 다음 섹션에서는 Resource Manager 템플릿과 CLI 명령을 사용하여 활성화하는 방법을 자세히 설명합니다.

#### <a name="using-the-resource-manager-template"></a>Resource Manager 템플릿 사용
[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Windows VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭하고 **매개 변수** 블레이드에 암호화 구성을 입력하고 **확인**을 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 **만들기**를 클릭하여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

다음 표에 Azure AD 클라이언트 ID를 사용하는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수 목록이 나와 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| AADClientID | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 응용 프로그램의 클라이언트 ID |
| AADClientSecret | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 응용 프로그램의 클라이언트 비밀 |
| keyVaultName | BitLocker 키가 업로드될 Key Vault의 이름. cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`을 사용하여 가져올 수 있습니다. |
|  keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL. UseExistingKek 드롭다운 목록에서 **nokek**를 선택하면 이 매개 변수가 선택 사항입니다. UseExistingKek 드롭다운 목록에서 **kek**를 선택하면 _keyEncryptionKeyURL_ 값을 반드시 입력해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_입니다. |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호화 작업이 수행될 때마다 이 버전 번호가 증가합니다. |
| vmName | 암호화 작업을 수행할 VM의 이름. |

> [!NOTE]
> _KeyEncryptionKeyURL_은 선택적 매개 변수입니다. Key Vault에서 데이터 암호화 키(BitLocker 암호화 비밀)에 대한 보안을 강화하기 위해 고유한 KEK를 만들 수 있습니다.

#### <a name="using-powershell-cmdlets"></a>PowerShell cmdlet 사용
PowerShell cmdlet을 사용하여 Azure Disk Encryption으로 암호화를 사용하는 방법은 블로그 게시물 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 1부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) 및 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 2부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)를 참조하세요.

#### <a name="using-cli-commands"></a>CLI 명령 사용
CLI 명령을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Windows VM에서 암호화를 사용하도록 설정하려면 다음을 수행하세요.

1. Key Vault에서 액세스 정책을 설정하려면
   * **EnabledForDiskEncryption** 플래그 설정:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Azure AD 응용 프로그램에 Key Vault에 비밀을 쓸 수 있는 권한 설정:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. 기존 또는 실행 중인 VM에서 암호화를 사용하려면

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. 암호화 상태를 가져오려면

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. 암호화된 VHD에서 새 VM에 암호화를 사용하도록 설정하려면 `azure vm create` 명령에 다음 매개 변수를 사용합니다.

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Azure에서 기존 또는 실행 중인 IaaS Linux VM에서 암호화 사용
[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Linux VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭하고 **매개 변수** 블레이드에 암호화 구성을 입력하고 **확인**을 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 **만들기**를 클릭하여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

다음 표에 Azure AD 클라이언트 ID를 사용하는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수 목록이 나와 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| AADClientID | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 응용 프로그램의 클라이언트 ID |
| AADClientSecret | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 응용 프로그램의 클라이언트 ID |
| keyVaultName | BitLocker 키가 업로드될 Key Vault의 이름. cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`을 사용하여 가져올 수 있습니다. |
|  keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL. UseExistingKek 드롭다운 목록에서 **nokek**를 선택하면 이 매개 변수가 선택 사항입니다. UseExistingKek 드롭다운 목록에서 **kek**를 선택하면 _keyEncryptionKeyURL_ 값을 반드시 입력해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 지원되는 유효한 값은 _OS_ 또는 _모두_입니다. (위의 필수 조건 섹션에서 지원되는 Linux 배포판과 OS 버전을 확인하세요.) |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호화 작업이 수행될 때마다 이 버전 번호가 증가합니다. |
| vmName | 암호화 작업을 수행할 VM의 이름. |
| passPhrase | 데이터 암호화 키로 강력한 암호를 입력합니다. |

> [!NOTE]
> _KeyEncryptionKeyURL_은 선택적 매개 변수입니다. Key Vault에서 데이터 암호화 키(암호 비밀)에 대한 보안을 강화하기 위해 고유한 KEK를 만들 수 있습니다.

#### <a name="cli-commands"></a>CLI 명령
[CLI 명령](../cli-install-nodejs.md)을 설치 및 사용하여 암호화된 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다. CLI 명령을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Linux VM에서 암호화를 사용하도록 설정하려면 다음을 수행하세요.

1. Key Vault에서 액세스 정책 설정

 * **EnabledForDiskEncryption** 플래그 설정:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Azure AD 응용 프로그램에 Key Vault에 비밀을 쓸 수 있는 권한 설정:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. 기존 또는 실행 중인 VM에서 암호화를 사용하려면

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. 암호화 상태 가져오기:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. 암호화된 VHD에서 새 VM에 암호화를 사용하도록 설정하려면 `azure vm create` 명령에 다음 매개 변수를 사용합니다.
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>암호화된 IaaS VM의 암호화 상태 가져오기
Azure Resource Manager, [PowerShell cmdlet](/powershell/azure/overview) 또는 CLI 명령을 사용하여 암호화 상태를 가져올 수 있습니다. 다음 섹션에서는 Azure Portal 및 CLI 명령을 사용하여 암호화 상태를 가져오는 방법에 대해 설명합니다.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 암호화된 Windows VM의 암호화 상태 가져오기
다음을 수행하여 Azure Resource Manager에서 IaaS VM의 암호화 상태를 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 왼쪽 메뉴에서 **가상 머신**를 클릭하여 구독에서 가상 머신의 요약 보기를 확인합니다. **구독** 드롭다운 목록에서 구독 이름을 선택하여 가상 머신 보기를 필터링할 수 있습니다.

2. **가상 머신** 페이지 위쪽에서 **열**을 클릭합니다.

3. **열 선택** 블레이드에서 **디스크 암호화**를 선택한 후 **업데이트**를 클릭합니다. 다음 그림에 표시된 것처럼 각 VM에 대한 _사용_ 또는 _사용 안 함_ 암호화 상태를 보여주는 디스크 암호화 열이 표시됩니다.

 ![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>디스크 암호화 PowerShell cmdlet을 사용하여 암호화된(Windows/Linux) IaaS VM의 암호화 상태 가져오기
디스크 암호화 PowerShell cmdlet `Get-AzureRmVMDiskEncryptionStatus`에서 IaaS VM의 암호화 상태를 가져올 수 있습니다. VM에 대한 암호화 설정을 가져오려면 다음을 입력합니다.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

암호화 키 URL에 대해 _Get-AzureRmVMDiskEncryptionStatus_의 출력을 검사할 수 있습니다.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

OSVolumeEncrypted 및 DataVolumesEncrypted 설정 값이 _Encrypted_로 설정되어 두 볼륨이 Azure Disk Encryption을 사용하여 암호화됨을 보여줍니다. PowerShell cmdlet을 사용하여 Azure Disk Encryption으로 암호화를 사용하는 방법은 블로그 게시물 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 1부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) 및 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 2부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)를 참조하세요.

> [!NOTE]
> Linux VM에서는 `Get-AzureRmVMDiskEncryptionStatus` cmdlet으로 암호화 상태를 보고하는 데 3-4분이 소요됩니다.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>디스크 암호화 CLI 명령에서 IaaS VM의 암호화 상태 가져오기
디스크 암호화 CLI 명령 `azure vm show-disk-encryption-status`에서 IaaS VM의 암호화 상태를 가져올 수 있습니다. VM에 대한 암호화 설정을 가져오려면 Azure CLI 세션을 입력합니다.

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>실행 중인 Windows IaaS VM에서 암호화 사용 안 함
Azure Disk Encryption Resource Manager 템플릿 또는 PowerShell cmdlet을 통해 실행 중인 Windows 또는 Linux IaaS VM에서 암호화를 사용하지 않고 암호 해독 구성을 지정할 수 있습니다.

##### <a name="windows-vm"></a>Windows VM
암호화 사용 안 함 단계는 실행 중인 Windows IaaS VM에서 OS, 데이터 볼륨이나 둘 모두의 암호화를 사용하지 않습니다. OS 볼륨을 사용하지 않고 암호화된 데이터 볼륨은 그대로 둘 수 없습니다. 암호화 사용 안 함 단계를 수행하면 Azure 클래식 배포 모델은 VM 서비스 모델을 업데이트하고 Windows IaaS VM은 암호가 해독되었다고 표시합니다. VM의 콘텐츠는 더 이상 미사용 상태에서 암호화되지 않습니다. 암호 해독은 Key Vault 및 암호화 키 자료(Windows의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 삭제하지 않습니다.

##### <a name="linux-vm"></a>Linux VM
암호화 사용 안 함 단계는 실행 중인 Linux IaaS VM에서 데이터 볼륨의 암호화를 사용하지 않습니다. 이 단계는 OS 디스크가 암호화되지 않은 경우에만 작동합니다.

> [!NOTE]
> OS 디스크에서 암호화를 사용하지 않도록 설정하는 것은 Linux VM에서 허용되지 않습니다.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>기존 또는 실행 중인 IaaS VM에서 암호화 사용 안 함
[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)을 사용하여 실행 중인 Windows IaaS VM에서 디스크 암호화를 사용하지 않을 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭하고 **매개 변수** 블레이드에 암호 해독 구성을 입력하고 **확인**을 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 **만들기**를 클릭하여 새 IaaS VM에서 암호화를 사용하도록 설정합니다.

Linux VM의 경우 [실행 중인 Linux VM에서 암호화 사용 안 함](https://aka.ms/decrypt-linuxvm) 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다.

다음 표는 실행 중인 IaaS VM에서 암호화를 사용하지 않는 Resource Manager 템플릿 목록입니다.

| 매개 변수 | 설명 |
| --- | --- |
| vmName | 암호화 작업을 수행할 VM의 이름.
| volumeType | 암호 해독 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_입니다. _Data_ 볼륨에서 암호화를 사용하고도 실행 중인 Windows IaaS VM OS/부팅 볼륨에서 암호화를 사용하지 않을 수 없습니다. OS 디스크에서 암호화를 사용하지 않도록 설정하는 것은 Linux VM에서 허용되지 않습니다. |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호 해독 작업이 수행될 때마다 이 버전 번호가 증가합니다. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>기존 또는 실행 중인 IaaS VM에서 암호화 사용 안 함
PowerShell cmdlet을 사용하여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용하지 않도록 설정하려면 [`Disable-AzureRmVMDiskEncryption`](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption)을 참조하세요. 이 cmdlet에는 Windows 및 Linux VM을 둘 다 지원합니다. 암호화를 사용하지 않도록 설정하려면 가상 머신에서 확장을 설치합니다. _이름_ 매개 변수를 지정하지 않으면 _Windows VM용 AzureDiskEncryption_ 기본 이름을 포함한 확장이 만들어집니다.

Linux VM에서는 AzureDiskEncryptionForLinux 확장이 사용됩니다.

> [!NOTE]
> 이 cmdlet은 가상 컴퓨터를 다시 부팅합니다.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Azure 관리 디스크를 사용하여 미리 암호화된 IaaS VM에 대한 암호화 사용
Azure 관리 디스크 ARM 템플릿을 사용하여 미리 암호화된 VHD를 통해 암호화된 VM 만들기(다음에 있는 ARM 템플릿 사용)   
[미리 암호화된 VHD/저장소 Blob에서 새로운 암호화된 관리 디스크 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Azure 관리 디스크를 사용하여 새로운 Linux IaaS VM에 대한 암호화 사용
Azure 관리 디스크 ARM 템플릿을 사용하여 암호화된 Linux IaaS VM 새로 만들기(다음에 있는 ARM 템플릿 사용)   
[전체 디스크 암호화로 RHEL 7.2 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Azure 관리 디스크를 사용하여 새로운 Windows IaaS VM에 대한 암호화 사용
 Azure 관리 디스크 ARM 템플릿을 사용하여 암호화된 Linux IaaS VM 새로 만들기(다음에 있는 ARM 템플릿 사용)   
 [갤러리 이미지에서 새로운 암호화된 Windows IaaS 관리 디스크 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅숏을 만들고 백업해야 합니다.  포털에서 관리 디스크의 스냅숏을 만들거나 Azure Backup을 사용할 수 있습니다.  Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다.  백업을 만들면 Set-AzureRmVMDiskEncryptionExtension cmdlet에 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화하는 데 사용할 수 있습니다.  이 명령은 백업을 만들고 이 매개 변수가 지정될 때까지 관리 디스크 기반 VM에 대해 실패합니다.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>기존의 암호화된 프리미엄 이외 VM의 암호화 설정 업데이트
  VM 실행에 사용되는 기존의 Azure Disk Encryption 지원 인터페이스[PS cmdlet, CLI 또는 ARM 템플릿]를 사용하여 Windows VM의 AAD 클라이언트 ID/비밀, 키 암호화 키[KEK], BitLocker 암호화 키 또는 Linux VM의 암호 같은 암호화 설정을 업데이트하세요. 암호화 설정 업데이트는 Premium Storage VM과 비 Premium Storage VM에서 모두 지원됩니다.

## <a name="appendix"></a>부록
### <a name="connect-to-your-subscription"></a>구독에 연결
진행하기 전에 이 문서의 *필수 조건* 섹션을 검토합니다. 모든 필수 조건이 충족되면 다음을 수행하여 구독에 연결합니다.

1. Azure PowerShell 세션을 시작하고 다음 명령을 사용하여 Azure 계정에 로그인합니다.

    `Connect-AzureRmAccount`

2. 여러 구독이 있고 사용할 구독을 지정하려면 다음을 입력하여 계정에 대한 구독을 확인합니다.

    `Get-AzureRmSubscription`

3. 사용할 구독을 지정하려면 다음을 입력합니다.

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. 구성된 구독이 올바른지 확인하려면 다음을 입력합니다.

    `Get-AzureRmSubscription`

5. Azure Disk Encryption cmdlet이 설치되어 있는지 확인하려면 다음을 입력합니다.

    `Get-command *diskencryption*`

6. 다음 출력은 Azure Disk Encryption PowerShell 설치를 확인합니다.

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>사전에 암호화된 Windows VHD 준비
Azure IaaS에서 암호화된 VHD로 배포용으로 사전에 암호화된 Windows VHD를 준비하려면 이어지는 섹션이 필요합니다. 이 정보를 사용하여 Azure Site Recovery 또는 Azure에서 최신 Windows VM(VHD)을 준비 및 부팅합니다.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>OS 보호를 위해 비-TPM을 허용하도록 그룹 정책 업데이트
**BitLocker 드라이브 암호화**라는 BitLocker 그룹 정책 설정을 구성하는데, **로컬 컴퓨터 정책** > **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** 아래에 있습니다. 다음 그림처럼 이 설정을 **운영 체제 드라이브** > **시작 시 추가 인증 요구** > **호환되는 TPM 없이 BitLocker 허용**으로 변경합니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>BitLocker 기능 구성 요소 설치
Windows Server 2012 이상에서는 다음 명령을 사용합니다.

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2에서는 다음 명령을 사용합니다.

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>`bdehdcfg`를 사용하여 BitLocker에 대한 OS 볼륨 준비
OS 파티션을 압축하고 BitLocker에 대한 컴퓨터를 준비하려면 다음 명령을 실행합니다.

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker를 사용하여 OS 볼륨 보호
[`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) 명령으로 외부 키 보호기를 사용하여 부팅 볼륨에서 암호화를 사용하도록 설정합니다. 도한 외부 드라이브 또는 볼륨에 외부 키(.bek 파일)를 배치합니다. 암호화는 다음 재부팅 후 시스템/부팅 볼륨에 사용하도록 설정됩니다.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> BitLocker를 사용하여 외부 키를 가져오기 위해서는 별도의 데이터/리소스 VHD로 VM을 준비합니다.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>실행 중인 Linux VM에서 OS 드라이브 암호화

##### <a name="prerequisites-for-os-disk-encryption"></a>OS 디스크 암호화를 위한 필수 조건

* VM은 [Azure Disk Encryption FAQ](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support)에 나열된 것과 같이 OS 디스크 암호화와 호환되는 배포를 사용해야 합니다. 
* Azure Resource Manager의 Marketplace 이미지에서 VM을 만들어야 합니다.
* 4GB 이상의 RAM이 있는 Azure VM(권장 크기는 7GB)이 있어야 합니다.
* (RHEL 및 CentOS) SELinux를 사용하지 않도록 설정합니다. SELinux를 사용하지 않도록 설정하려면 VM에 대한 [SELinux 사용자 및 관리자 가이드](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)에서 "4.4.2. SELinux 사용 안 함"을 참조하세요.
* SELinux를 사용하지 않도록 설정한 후 VM을 한 번 이상 다시 부팅합니다.

##### <a name="steps"></a>단계
1. 이전에 지정한 배포판 중 하나를 사용하여 VM을 만듭니다.

 CentOS 7.2의 경우 특수 이미지를 통해 OS 디스크 암호화가 지원됩니다. 이 이미지를 사용하려면 VM을 만들 때 SKU로 "7.2n"을 지정합니다.
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. 필요에 따라 VM을 구성합니다. 모든(OS + 데이터) 드라이브를 암호화하려는 경우 데이터 드라이브를 지정하고 /etc/fstab에서 탑재할 수 있어야 합니다.

 > [!NOTE]
 > 블록 장치 이름(예: /dev/sdb1)을 지정하는 대신, UUID=...를 사용하여 /etc/fstab에서 데이터 드라이브를 지정합니다. 암호화하는 동안 드라이브의 순서는 VM에서 변경됩니다. VM이 블록 장치의 특정 순서에 의존하는 경우 암호화 후 탑재하지 못합니다.

3. SSH 세션에서 로그아웃합니다.

4. OS를 암호화하려면 [암호화 사용 시](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure) volumeType을 **All** 또는 **OS**로 지정합니다.

 > [!NOTE]
 > `systemd` 서비스로 실행되지 않는 모든 사용자 공간 프로세스는 `SIGKILL`로 중지됩니다. VM을 다시 부팅합니다. 실행 중인 VM에서 OS 디스크 암호화를 사용할 경우 VM의 가동 중지 시간을 계획하세요.

5. [다음 섹션](#monitoring-os-encryption-progress)의 지침에 따라 암호화 진행 상태를 주기적으로 모니터링합니다.

6. Get-AzureRmVmDiskEncryptionStatus에 "VMRestartPending"이 표시되면 VM에 로그인하거나 포털, PowerShell 또는 CLI를 사용하여 VM을 다시 시작합니다.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
VM을 다시 부팅하기 전에 VM의 [부트 진단](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)을 저장하는 것이 좋습니다.

#### <a name="monitoring-os-encryption-progress"></a>OS 암호화 진행 상태 모니터링
OS 암호화 진행 상태를 모니터링하는 방법은 세 가지가 있습니다.

* `Get-AzureRmVmDiskEncryptionStatus` cmdlet을 사용하고 ProgressMessage 필드를 검사합니다.
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Premium 저장소 지원 VM의 경우 VM이 "OS 디스크 암호화 시작됨"에 도달한 후 약 40-50분이 소요됩니다.

 WALinuxAgent에서 [문제 #388](https://github.com/Azure/WALinuxAgent/issues/388)으로 인해 일부 배포판에서 `OsVolumeEncrypted` 및 `DataVolumesEncrypted`가 `Unknown`으로 표시됩니다. WALinuxAgent 2.1.5 버전 이상에서는 이 문제가 자동으로 수정됩니다. 출력에 `Unknown`이 표시되는 경우 Azure Resource Explorer를 사용하여 디스크 암호화 상태를 확인할 수 있습니다.

 [Azure Resource Explorer](https://resources.azure.com/)로 이동한 후 왼쪽의 선택 패널에서 이 계층 구조를 확장합니다.

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 InstanceView에서 아래로 스크롤하여 드라이브의 암호화 상태를 확인합니다.

 ![VM 인스턴스 보기](./media/azure-security-disk-encryption/vm-instanceview.png)

* [부트 진단](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)을 살펴봅니다. ADE 확장의 메시지에는 `[AzureDiskEncryption]`이라는 접두사가 붙습니다.

* SSH를 통해 VM에 로그온하고 다음에서 확장 로그를 가져옵니다.

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 OS 암호화가 진행 중인 동안에는 VM에 로그온하지 않는 것이 좋습니다. 다른 두 가지 방법이 실패한 경우에만 로그가 복사됩니다.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>사전에 암호화된 Linux VHD 준비
##### <a name="ubuntu-16"></a>Ubuntu 16
다음을 수행하여 배포 설치 중에 암호화를 구성합니다.

1. 디스크를 분할할 때 **암호화된 볼륨 구성**을 선택합니다.

 ![Ubuntu 16.04 설치](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 암호화되지 않아야 하는 별도의 부트 드라이브를 만듭니다. 루트 드라이브를 암호화합니다.

 ![Ubuntu 16.04 설치](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. 암호를 제공합니다. Key Vault에 업로드하는 암호입니다.

 ![Ubuntu 16.04 설치](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. 분할을 완료합니다.

 ![Ubuntu 16.04 설치](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. VM을 부팅하고 암호를 묻는 메시지가 표시되면 3단계에서 제공한 암호를 사용합니다.

 ![Ubuntu 16.04 설치](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. [이 지침](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)을 사용하여 Azure에 업로드하기 위한 VM을 준비합니다. 마지막 단계(VM 프로비전 해제)를 아직 실행하지 마세요.

다음을 수행하여 Azure로 작업할 암호화를 구성합니다.

1. 다음 스크립트 내용으로 /usr/local/sbin/azure_crypt_key.sh라는 파일을 만듭니다. Azure에서 사용된 암호 파일 이름이므로 KeyFileName에 주의해야 합니다.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. */etc/crypttab*에서 암호화 구성을 변경합니다. 다음과 같이 표시되어야 합니다.
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Windows에서 *azure_crypt_key.sh*를 편집하고 Linux에 복사한 경우 `dos2unix /usr/local/sbin/azure_crypt_key.sh`를 실행합니다.

4. 스크립트에 실행 권한을 추가합니다.
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. 줄을 추가하여 */etc/initramfs-tools/modules*를 편집합니다.
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. initramfs를 업데이트하는 `update-initramfs -u -k all`을 실행하여 `keyscript`를 적용합니다.

7. 이제 VM을 프로비전 해제할 수 있습니다.

 ![Ubuntu 16.04 설치](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 다음 단계를 계속하여 Azure에 [VHD를 업로드](#upload-encrypted-vhd-to-an-azure-storage-account)합니다.

##### <a name="opensuse-132"></a>openSUSE 13.2
배포 설치 중에 암호화를 구성하려면 다음을 수행합니다.
1. 디스크를 파티션하는 경우 **볼륨 그룹 암호화**를 선택하고 암호를 입력합니다. Key Vault에 업로드할 암호입니다.

 ![openSUSE 13.2 설치](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. 암호를 사용하여 VM을 부팅합니다.

 ![openSUSE 13.2 설치](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. [Azure용 SLES 또는 openSUSE 가상 머신 준비](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)의 지침에 따라 Azure에 업로드할 VM을 준비합니다. 마지막 단계(VM 프로비전 해제)를 아직 실행하지 마세요.

Azure로 작업할 암호화를 구성하려면 다음을 수행합니다.
1. /etc/dracut.conf를 편집하고 다음 줄을 추가합니다.
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. /usr/lib/dracut/modules.d/90crypt/module-setup.sh 파일의 끝에서 다음 줄을 주석 처리합니다.
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 파일의 시작 부분에 다음 줄을 추가합니다.
 ```
    DRACUT_SYSTEMD=0
 ```
다음 항목을 그 아래 항목으로 바꿉니다.
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
to:
```
    if [ 1 ]; then
```
4. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh를 편집하고 "# Open LUKS device"에 추가합니다.

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. `/usr/sbin/dracut -f -v`를 실행하여 initrd를 업데이트합니다.

6. 이제 VM을 프로비전 해제하고 [VHD를 Azure에 업로드](#upload-encrypted-vhd-to-an-azure-storage-account)할 수 있습니다.

##### <a name="centos-7"></a>CentOS 7
배포 설치 중에 암호화를 구성하려면 다음을 수행합니다.
1. 디스크를 분할할 때 **내 데이터 암호화**를 선택합니다.

 ![CentOS 7 설치](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. 루트 파티션에 대해 **암호화**가 선택되어 있는지 확인합니다.

 ![CentOS 7 설치](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. 암호를 제공합니다. Key Vault에 업로드할 암호입니다.

 ![CentOS 7 설치](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. VM을 부팅하고 암호를 묻는 메시지가 표시되면 3단계에서 제공한 암호를 사용합니다.

 ![CentOS 7 설치](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. [Azure용 CentOS 기반 가상 머신 준비](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)의 "CentOS 7.0+" 지침에 따라 Azure에 업로드할 VM을 준비합니다. 마지막 단계(VM 프로비전 해제)를 아직 실행하지 마세요.

6. 이제 VM을 프로비전 해제하고 [VHD를 Azure에 업로드](#upload-encrypted-vhd-to-an-azure-storage-account)할 수 있습니다.

Azure로 작업할 암호화를 구성하려면 다음을 수행합니다.

1. /etc/dracut.conf를 편집하고 다음 줄을 추가합니다.
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. /usr/lib/dracut/modules.d/90crypt/module-setup.sh 파일의 끝에서 다음 줄을 주석 처리합니다.
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 파일의 시작 부분에 다음 줄을 추가합니다.
```
    DRACUT_SYSTEMD=0
```
다음 항목을 그 아래 항목으로 바꿉니다.
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh를 편집하고 "# Open LUKS device" 뒤에 추가합니다.
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. "/usr/sbin/dracut -f -v"를 실행하여 initrd를 업데이트합니다.

![CentOS 7 설치](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Azure Storage 계정에 암호화된 VHD 업로드
BitLocker 암호화 또는 DM-Crypt 암호화를 사용하도록 설정한 후에는 로컬 암호화된 VHD를 저장소 계정에 업로드해야 합니다.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>사전에 암호화된 VM에 대한 디스크 암호화 비밀을 Key Vault에 업로드
이전에 가져온 디스크 암호화 비밀을 Key Vault에 비밀 업로드해야 합니다. Key Vault에는 디스크 암호화 및 Azure AD 클라이언트를 위해 설정된 사용 권한이 있어야 합니다.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>KEK로 암호화되지 않은 디스크 암호화 암호
Key Vault에서 비밀을 설정하려면 [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret)을 사용합니다. Windows 가상 머신의 경우 bek 파일이 base64 문자열로 인코딩된 후 `Set-AzureKeyVaultSecret` cmdlet을 사용하여 Key Vault로 업로드됩니다. Linux의 경우 암호는 base64 문자열로 인코딩된 후 Key Vault로 업로드됩니다. 또한 Key Vault에서 비밀을 만들 때 다음 태그가 설정되었는지 확인합니다.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

[KEK를 사용하지 않고 OS 디스크를 연결](#without-using-a-kek)하기 위해 다음 단계에서 `$secretUrl`을 사용합니다.

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK로 암호화된 디스크 암호화 암호
비밀을 Key Vault에 업로드하기 전에 주요 암호화 키를 사용하여 선택적으로 암호화할 수 있습니다. 먼저 래핑 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx)를 사용하여 주요 암호화 키로 비밀을 암호화합니다. 이 래핑 작업의 출력은 base64 URL 인코딩 문자열로, [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) cmdlet을 사용하여 비밀로 업로드할 수 있습니다.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

`$KeyEncryptionKey` 및 `$secretUrl`은 [KEK를 사용하여 OS 디스크를 연결](#using-a-kek)하기 위해 다음 단계에서 사용합니다.

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>OS 디스크를 연결할 때 비밀 URL 지정
#### <a name="without-using-a-kek"></a>KEK 사용 안 함
OS 디스크를 연결하는 동안 `$secretUrl`을 전달해야 합니다. URL은 "KEK로 암호화되지 않은 디스크 암호화 비밀" 섹션에서 생성했습니다.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>KEK 사용
OS 디스크를 연결할 때 `$KeyEncryptionKey` 및 `$secretUrl`을 전달합니다. URL은 "KEK로 암호화되지 않은 디스크 암호화 비밀" 섹션에서 생성했습니다.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="for-more-information"></a>Blob에 대한 자세한 내용은
[Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 1부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 2부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
