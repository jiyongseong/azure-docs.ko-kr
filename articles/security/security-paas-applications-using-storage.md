---
title: Azure Storage를 사용하여 PaaS 응용 프로그램 보안 | Microsoft Docs
description: " PaaS 웹 및 모바일 응용 프로그램 보안을 위한 Azure Storage 보안 모범 사례에 대해 알아봅니다. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomShinder
ms.openlocfilehash: 9d4251e61b60d8da6ce5072ba66aeaedb60cb33a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Azure Storage를 사용하여 PaaS 웹 및 모바일 응용 프로그램 보안
이 문서에서는 PaaS 웹 및 모바일 응용 프로그램 보안을 위한 Azure Storage 보안 모범 사례에 대해 설명합니다. 이러한 모범 사례는 Azure에 대한 Microsoft와 고객의 경험에서 비롯된 것입니다.

[Azure Storage 보안 가이드](../storage/common/storage-security-guide.md)는 Azure Storage 및 보안에 대한 자세한 정보를 제공합니다.  이 문서에서는 보안 가이드에 나오는 일부 개념을 소개하고 좀 더 자세한 정보를 원하는 사용자를 위해 보안 가이드 및 기타 원본에 대한 링크를 제공합니다.

## <a name="azure-storage"></a>Azure Storage
Azure에서는 온-프레미스에서는 쉽게 달성할 수 없는 방식으로 저장소를 배포하고 사용할 수 있습니다. Azure Storage를 사용하면 비교적 적은 노력으로 높은 수준의 확장성 및 가용성을 얻을 수 있습니다. 뿐만 아니라 Azure Storage는 Windows 및 Linux Azure Virtual Machines의 기반일 뿐만 아니라 대규모 분산 응용 프로그램도 지원할 수 있습니다.

Azure 저장소 서비스는 Blob 저장소, 테이블 저장소, 큐 저장소 및 파일 저장소 등의 4가지 서비스를 제공합니다. 자세한 내용은 [Microsoft Azure Storage 소개](../storage/storage-introduction.md)를 참조하세요.

## <a name="best-practices"></a>모범 사례
이 문서에서는 다음과 같은 모범 사례를 다룹니다.

- 액세스 보호:
   - 공유 액세스 서명(SAS)
   - Managed Disk
   - 역할 기반 Access Control(RBAC)

- 저장소 암호화:
   - 고가치 데이터에 대한 클라이언트 쪽 암호화
   - 가상 머신에 대한 Azure Disk Encryption
   - Storage 서비스 암호화

## <a name="access-protection"></a>액세스 보호
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>저장소 계정 키 대신 공유 액세스 서명 사용

일반적으로 Windows Server 또는 Linux 가상 머신을 실행하는 IaaS 솔루션에서 파일은 액세스 제어 메커니즘을 사용하여 노출 및 변조 위협으로부터 보호됩니다. Windows에서는 [ACL(액세스 제어 목록)](../virtual-network/virtual-networks-acl.md), Linux에서는 [chmod](https://en.wikipedia.org/wiki/Chmod)를 사용할 수 있습니다. 기본적으로 이 작업은 사용자 고유의 데이터 센터에 있는 서버에서 파일을 보호하는 경우에 수행하는 작업입니다.

PaaS는 다릅니다. Microsoft Azure에서 파일을 저장하는 가장 일반적인 방법 중 하나는 [Azure Blob Storage](../storage/storage-dotnet-how-to-use-blobs.md)를 사용하는 것입니다. Blob Storage 및 기타 파일 저장소 간의 차이점은 파일 I/O 및 함께 수반되는 보호 방법입니다.

액세스 제어는 중요합니다. Azure Storage에 대한 액세스를 제어하는 데 도움을 주기 위해 시스템에서는 사용자가 [저장소 계정을 만들 때](../storage/common/storage-create-storage-account.md) 2개의 512비트 SAK(저장소 계정 키)를 생성합니다. 이러한 키 중복 수준으로 일상적인 키 순환 중에 서비스 중단을 방지할 수 있습니다.

저장소 액세스 키는 우선 순위가 높은 비밀로, 저장소 액세스 제어를 담당하는 사용자만 액세스할 수 있어야 합니다. 잘못된 사용자가 이러한 키에 액세스하면 저장소를 완전히 제어하고 저장소에서 파일을 바꾸거나 삭제하거나 추가할 수 있게 됩니다. 여기에는 조직 또는 고객에게 손상을 줄 수 있는 맬웨어 및 다른 유형의 콘텐츠가 포함됩니다.

저장소의 개체에 대한 액세스를 제공하는 방법이 필요합니다. 보다 세부적인 액세스 권한을 제공하려면 SAS([공유 액세스 서명](../storage/common/storage-dotnet-shared-access-signature-part-1.md))를 활용할 수 있습니다. SAS를 사용하면 미리 정의된 시간 간격 동안 특정 사용 권한을 사용하여 저장소의 특정 개체를 공유할 수 있습니다. 공유 액세스 서명을 통해 다음을 정의할 수 있습니다.

- SAS가 유효한 경우 시작 시간 및 만료 시간을 포함하는 간격입니다.
- SAS에서 부여된 권한입니다. 예를 들어 blob의 SAS는 해당 blob에 사용자 읽기 및 쓰기 권한을 부여할 수 있지만 삭제 권한은 부여하지 않습니다.
- Azure Storage가 SAS를 수락하는 선택적 IP 주소 또는 IP 주소 범위입니다. 예를 들어 조직에 속한 IP 주소 범위를 지정할 수 있습니다. 이것은 SAS에 대한 보안의 또 다른 측정값을 제공합니다.
- Azure Storage가 SAS을 수락하는 프로토콜입니다. HTTPS를 사용하여 클라이언트에 대한 액세스를 제한하려면 이 선택적 매개 변수를 사용할 수 있습니다.

SAS를 사용하여 Storage 계정 키를 제공하지 않고도 공유하려는 방식으로 콘텐츠를 공유할 수 있습니다. 응용 프로그램에서 항상 SAS를 사용하는 것은 저장소 계정 키를 손상시키지 않고 저장 리소스를 공유할 수 있는 보안 방법입니다.

자세한 내용은 [SAS(공유 액세스 서명) 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요. 잠재적인 위험과 이러한 위험을 완화하는 권장 지침에 대한 자세한 내용은 [SAS를 사용하는 경우 모범 사례](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

### <a name="use-managed-disks-for-vms"></a>VM에 Managed Disks 사용

[Azure Managed Disks](../storage/storage-managed-disks-overview.md)를 선택하면 Azure에서 VM 디스크에 사용되는 저장소 계정을 관리해 줍니다. 디스크 유형(프리미엄 또는 표준)과 디스크 크기만 선택하면 나머지 작업은 Azure Storage에서 자동으로 수행됩니다. 다른 경우라면 여러 저장소 계정이 필요할 수 있는 확장성 제한 문제에 대해서도 걱정할 필요가 없습니다.

자세한 내용은 [프리미엄 관리 및 비관리 디스크에 대한 질문과 대답](../storage/storage-faq-for-disks.md)을 참조하세요.

### <a name="use-role-based-access-control"></a>역할 기반 Access Control 사용

앞에서 SAS(공유 액세스 서명)를 사용하여 계정 키를 노출하지 않고 다른 클라이언트에게 저장소 계정의 개체에 대한 제한된 액세스 권한을 부여하는 방법을 살펴보았습니다. 저장소 계정에 대한 특정 작업 관련 위험이 SAS의 이점을 능가하는 경우도 있습니다. 다른 방법으로 액세스를 관리하는 것이 더 간단한 경우도 있습니다.

액세스를 관리하는 또 다른 방법은 Azure RBAC([역할 기반 Access Control](../role-based-access-control/overview.md))를 사용하는 것입니다. RBAC를 사용하는 경우 알아야 할 사항 및 최소 권한 보안 원칙에 따라 직원들에게 필요한 정확한 권한을 부여하는 데 주안점을 둡니다. 권한이 너무 많으면 공격자에게 계정이 노출될 수 있고, 권한이 너무 적으면 직원이 업무를 효율적으로 수행할 수 없습니다. RBAC는 Azure에 대한 세밀한 액세스 관리를 제공하여 이 문제를 해결하도록 도와줍니다. 이는 데이터 액세스에 대한 보안 정책을 적용하려는 조직에 필수적입니다.

Azure의 기본 제공 RBAC 역할을 활용하여 사용자에게 권한을 할당할 수 있습니다. Storage 계정을 관리해야 하는 클라우드 운영자를 위한 Storage 계정 참여자 및 클래식 Storage 계정 참여자 역할을 사용하여 클래식 Storage 계정을 관리하는 방법을 고려해 볼 수 있습니다. VM을 관리해야 하지만 VM이 연결된 가상 네트워크 또는 저장소 계정은 관리할 필요가 없는 클라우드 운영자의 경우 Virtual Machine 참여자 역할에 추가하는 것이 좋습니다.

RBAC 같은 기능을 활용하여 데이터 액세스 제어를 적용하지 않는 조직은 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 그로 인해 일부 사용자가 원래는 액세스할 수 없어야 하는 데이터에 액세스할 수 있게 되어 데이터 손상이 발생할 수 있습니다.

RBAC에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)
- [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/built-in-roles.md)
- [Azure Storage 보안 가이드](../storage/common/storage-security-guide.md): RBAC를 사용하여 저장소 계정을 보호하는 방법

## <a name="storage-encryption"></a>저장소 암호화
### <a name="use-client-side-encryption-for-high-value-data"></a>고가치 데이터에 대해 클라이언트 쪽 암호화 사용

클라이언트 쪽 암호화를 사용하여 Azure Storage에 데이터를 업로드하기 전에 전송 중인 데이터를 프로그래밍 방식으로 암호화하고, 저장소에서 가져올 때 프로그래밍 방식으로 데이터를 해독할 수 있습니다.  이를 통해 전송 중인 데이터와 미사용 데이터를 모두 암호화할 수 있습니다.  클라이언트 쪽 암호화는 데이터를 암호화하는 가장 안전한 방법이지만 응용 프로그램을 프로그래밍 방식으로 변경하고 키 관리 프로세스를 배치해야 합니다.

또한 클라이언트 쪽 암호화를 사용하면 암호화 키를 단독으로 제어할 수 있습니다.  자체 암호화 키를 생성하고 관리할 수 있습니다.  클라이언트 쪽 암호화는 Azure Storage 클라이언트 라이브러리가 CEK(콘텐츠 암호화 키)를 생성하고 KEK(키 암호화 키)를 사용하여 래핑(암호화)하는 봉투(envelope) 기술을 사용합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리되거나 [Azure Key Vault](../key-vault/key-vault-whatis.md)에 저장됩니다.

클라이언트 쪽 암호화는 Java 및 .NET 저장소 클라이언트 라이브러리에 기본적으로 제공되어 있습니다.  클라이언트 응용 프로그램 내에서 데이터를 암호화하고 자체 암호화 키를 생성 및 관리하는 방법에 대한 자세한 내용은 [Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault](../storage/storage-client-side-encryption.md)를 참조하세요.

### <a name="azure-disk-encryption-for-vms"></a>VM에 대한 Azure Disk Encryption
Azure Disk Encryption은 Windows 및 Linux IaaS 가상 머신 디스크를 암호화할 수 있도록 하는 기능입니다. Azure 디스크 암호화는 업계 표준인 Windows의 BitLocker 기능과 Linux의 DM-Crypt 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다. 또한 고객이 Key Vault 구독에서 디스크 암호화 키 및 암호를 관리 및 제어할 수 있도록 Azure Key Vault에 통합되어 있습니다. 이 솔루션은 가상 머신 디스크에 있는 모든 데이터가 미사용 시 Azure Storage에 암호화되도록 합니다.

[Windows 및 Linux IaaS VM용 Azure Disk Encryption](azure-security-disk-encryption.md)을 참조하세요.

### <a name="storage-service-encryption"></a>Storage 서비스 암호화
File Storage에 대해 [Storage 서비스 암호화](../storage/storage-service-encryption.md)가 사용되도록 설정되면 데이터는 AES-256 암호화를 사용하여 자동으로 암호화됩니다. Microsoft는 모든 암호화, 해독 및 키 관리를 처리합니다. 이 기능은 LRS 및 GRS 중복 유형에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 PaaS 웹 및 모바일 응용 프로그램 보안을 위한 Azure Storage 보안 모범 사례 모음을 소개했습니다. PaaS 배포 보안 유지에 대한 자세한 내용은 다음을 참조하세요.

- [PaaS 배포 보안](security-paas-deployments.md)
- [Azure App Services를 사용하여 PaaS 웹 및 모바일 응용 프로그램 보안](security-paas-applications-using-app-services.md)
- [Azure에서 PaaS 데이터베이스 보안 유지](security-paas-applications-using-sql.md)
