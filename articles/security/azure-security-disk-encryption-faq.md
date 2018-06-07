---
title: Azure Disk Encryption FAQ | Microsoft Docs
description: 이 문서에서는 Windows 및 Linux IaaS VM용 Microsoft Azure Disk Encryption에 대한 질문과 대답을 제공합니다.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: 47ccf91a64653c928cc4da01bc98535c97440d37
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187694"
---
# <a name="azure-disk-encryption-faq"></a>Azure Disk Encryption FAQ

이 문서에서는 Windows 및 Linux IaaS VM용 Microsoft Azure Disk Encryption에 대한 FAQ(질문과 대답)를 제공합니다. 이 서비스에 대한 자세한 내용은 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)을 참조하세요.

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>GA(일반 공급)에서 Azure Disk Encryption은 어디에 있나요?

Windows 및 Linux IaaS VM용 Azure Disk Encryption은 모든 Azure 공용 지역의 일반 공급에서 사용할 수 있습니다.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure Disk Encryption에서 사용할 수 있는 사용자 환경은 무엇인가요?

Azure Disk Encryption GA에서는 Azure Resource Manager 템플릿, Azure PowerShell 및 Azure CLI를 지원합니다. 이렇게 하면 많은 유연성을 제공합니다. IaaS VM에 대한 디스크 암호화를 사용하기 위한 세 가지 옵션이 있습니다. Azure Disk Encryption에서 사용할 수 있는 사용자 환경 및 단계별 지침에 대한 자세한 내용은 [Azure Disk Encryption 배포 시나리오 및 환경](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)을 참조하세요.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk Encryption 비용은 얼마인가요?

Azure Disk Encryption을 사용한 VM 디스크 암호화에는 요금이 청구되지 않으나, Azure Key Vault 사용과 관련된 요금은 청구됩니다. Azure Key Vault 요금에 대한 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/) 페이지를 참조하세요.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Azure Disk Encryption에서 지원하는 가상 머신 계층은 무엇인가요?

Azure Disk Encryption은 [A, D, DS, G, GS 및 F](https://azure.microsoft.com/pricing/details/virtual-machines/) 시리즈 IaaS VM을 포함하여 표준 계층 VM에서 사용할 수 있습니다. 프리미엄 저장소가 있는 VM에 사용할 수도 있습니다. 기본 계층 VM에서는 사용할 수 없습니다.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Azure Disk Encryption은 어떤 Linux 배포판을 지원하나요?

Azure Disk Encryption은 다음과 같은 Linux 서버 배포 및 버전에서 지원됩니다.

| Linux 배포 | 버전 | 암호화에 지원되는 볼륨 유형|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS 및 데이터 디스크 |
| Ubuntu | 14.04.5-DAILY-LTS | OS 및 데이터 디스크 |
| RHEL | 7.4 | 데이터 디스크* |
| RHEL | 7.3 | 데이터 디스크* |
| RHEL | 7.2 | 데이터 디스크* |
| RHEL | 6.8 | 데이터 디스크* |
| RHEL | 6.7 | 데이터 디스크* |
| CentOS | 7.3 | OS 및 데이터 디스크 |
| CentOS | 7.2n | OS 및 데이터 디스크 |
| CentOS | 6.8 | OS 및 데이터 디스크 |
| CentOS | 7.1 | 데이터 디스크  |
| CentOS | 7.0 | 데이터 디스크  |
| CentOS | 6.7 | 데이터 디스크  |
| CentOS | 6.6 | 데이터 디스크  |
| CentOS | 6.5 | 데이터 디스크  |
| openSUSE | 13.2 | 데이터 디스크  |
| SLES | 12 SP1 | 데이터 디스크  |
| SLES | 우선 순위: 12-SP1 | 데이터 디스크  |
| SLES | HPC 12 | 데이터 디스크  |
| SLES | 우선 순위: 11-SP4 | 데이터 디스크  |
| SLES | 11 SP4 | 데이터 디스크  |

*__ADE는 데이터 디스크의 RHEL에 대해 지원됩니다. 현재 ADE 구현은 OS 디스크와 호환되지만 현재는 공동 지원되지 않습니다. Microsoft와 Red Hat은 공동 지원되는 솔루션을 개발 중입니다. 그 때까지는 [여기](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)서 Linux OS 디스크 암호화에 대한 ADE 백서를 참조하시기 바랍니다.__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk Encryption을 사용하기 시작하려면 어떻게 해야 하나요?

시작하려면 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 백서를 참조하세요.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Azure Disk Encryption을 사용하여 부팅 볼륨과 데이터 볼륨을 모두 암호화할 수 있나요?

예, Windows 및 Linux IaaS VM용 부팅 볼륨과 데이터 볼륨을 암호화할 수 있습니다. Windows VM의 경우 OS 볼륨을 먼저 암호화해야 데이터 볼륨을 암호화할 수 있습니다. Linux VM의 경우 OS 볼륨을 먼저 암호화하지 않고도 데이터 볼륨을 암호화할 수 있습니다. Linux용 OS 볼륨을 암호화하면 Linux IaaS VM용 OS 볼륨에 대한 암호화 사용 해제가 지원되지 않습니다.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Azure Disk Encryption에서 BYOK(Bring Your Own Key) 기능을 가져올 수 있나요?

네, 사용자 고유의 키 암호화 키를 제공할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure Disk Encryption 배포 시나리오 및 환경](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)을 참조하세요.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure에서 만든 키 암호화 키를 사용할 수 있나요?

예, Azure Key Vault를 사용하여 Azure Disk Encryption을 사용하기 위한 키 암호화 키를 생성할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure Disk Encryption 배포 시나리오 및 환경](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)을 참조하세요.

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>온-프레미스 키 관리 서비스 또는 HSM을 사용하여 암호화 키를 보호할 수 있나요?

온-프레미스 키 관리 서비스 또는 HSM은 Azure Disk Encryption을 통해 암호화 키를 보호하는 데 사용할 수 없습니다. Azure Key Vault 서비스만 사용하여 암호화 키를 보호할 수 있습니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure Disk Encryption 배포 시나리오 및 환경](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences)을 참조하세요.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk Encryption을 구성하기 위한 필수 구성 요소는 무엇인가요?

필수 구성 요소 PowerShell 스크립트가 있습니다. 이 스크립트를 사용하여 Azure Active Directory 응용 프로그램을 만들거나 새 키 자격 증명 모음을 만들거나, 디스크 암호화 액세스에 대한 기존 키 자격 증명 모음을 설정하여 암호화를 사용하도록 설정하고 비밀과 키를 보호합니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure Disk Encryption 필수 구성 요소, 배포 시나리오 및 환경](azure-security-disk-encryption.md#prerequisites)을 참조하세요.

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>PowerShell을 사용하여 Azure Disk Encryption을 구성하는 방법에 대한 자세한 정보는 어디서 얻을 수 있나요?

Azure Disk Encryption 기본 작업을 수행하는 방법과 고급 시나리오에 대한 몇 가지 훌륭한 아티클이 있습니다. 기본 작업은 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 1부](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)를 참조하세요. 고급 시나리오는 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 2부](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)를 참조하세요.

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure Disk Encryption에서 지원하는 Azure PowerShell 버전은 무엇인가요?

최신 버전의 Azure PowerShell SDK를 사용하여 Azure Disk Encryption을 구성합니다. 최신 버전의 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)을 다운로드합니다. Azure SDK 버전 1.1.0에서는 Azure Disk Encryption을 지원하지 *않습니다*.

> [!NOTE]
> Linux Azure Disk Encryption 미리 보기 확장은 더 이상 사용되지 않습니다. 자세한 내용은 [Linux IaaS VM에 대한 Azure 디스크 암호화 미리 보기 확장 사용 중단](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)을 참조하세요.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>사용자 지정 Linux 이미지에 Azure Disk Encryption을 적용할 수 있나요?

사용자 지정 Linux 이미지에는 Azure Disk Encryption을 적용할 수 없습니다. 이전에 언급한 지원 배포판에 대한 Linux 갤러리 이미지만 지원합니다. 현재 사용자 지정 Linux 이미지는 지원하지 않습니다.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>yum 업데이트를 사용하는 Linux Red Hat VM에 업데이트를 적용할 수 있나요?

예, 업데이트를 수행하거나 Red Hat Linux VM을 패치할 수 있습니다. 자세한 내용은 [yum 업데이트를 사용하여 암호화된 Azure IaaS Red Hat VM에 업데이트 적용](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)을 참조하세요.

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>권장되는 Linux용 Azure Disk Encryption 워크플로는 무엇인가요?

Linux에서 최상의 결과를 얻기 위해 다음 워크플로가 권장됩니다.
* 원하는 OS 배포판 및 버전에 해당하는 수정되지 않은 주식 갤러리 이미지에서 시작
* 암호화되는 탑재된 드라이브를 백업합니다.  오류가 발생한 경우, 예를 들어 암호화가 완료되기 전에 VM을 다시 부팅하는 경우 복구를 허용합니다.
* 암호화(VM 특성 및 연결된 데이터 디스크의 크기에 따라 여러 시간 또는 심지어 며칠이 걸릴 수 있음)
* 필요에 따라 소프트웨어를 사용자 지정하고 이미지에 추가합니다.

이 워크플로가 가능하지 않은 경우 플랫폼 저장소 계정 계층에서 SSE([저장소 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)) 사용은 dm-crypt를 사용하는 전체 디스크 암호화에 대한 대안이 될 수 있습니다.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>디스크 "Bek Volume" 또는 "/mnt/azure_bek_disk"란 무엇인가요?

Windows용 "Bek volume" 또는 Linux용 "/mnt/azure_bek_disk"는 암호화된 Azure IaaS VM에 대한 암호화 키를 안전하게 저장하는 로컬 데이터 볼륨입니다.
> [!NOTE]
> 이 디스크의 내용은 삭제하거나 편집하지 않아야 합니다. IaaS VM의 암호화 작업에 암호화 키가 필요하므로 이 디스크를 분리하지 않도록 합니다.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>질문을 하거나 의견을 제출할 수 있는 곳은 어디인가요?

[Azure Disk Encryption 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)에서 질문하거나 피드백을 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Disk Encryption와 관련하여 가장 자주 묻는 질문에 대해 자세히 알아 보았습니다. 이 서비스 및 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Security Center에서 디스크 암호화 적용](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure 가상 머신 암호화](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [휴지 상태의 Azure 데이터 암호화](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
