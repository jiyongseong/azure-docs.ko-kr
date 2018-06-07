---
title: 미사용 데이터에 대한 Azure Storage Service Encryption | Microsoft Docs
description: Azure Storage 서비스 암호화 기능을 사용하여 데이터를 저장할 때 서비스 쪽에서 Azure Blob Storage를 암호화하고 데이터를 검색할 때 암호 해독합니다.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: 5e4df176104111f44ca95df2b2d5d1c81ed3a4e3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>휴지 상태의 데이터에 대한 Azure Storage 서비스 암호화

미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure Storage는 데이터를 자동으로 암호화한 후 Azure Storage에 보관하고, 검색 전에 암호를 해독합니다. Storage 서비스 암호화의 암호화, 미사용 데이터의 암호화, 암호 해독, 키 관리는 완전히 투명한 방식으로 처리됩니다. Azure Storage에 쓴 모든 데이터는 사용 가능한 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 통해 암호화됩니다.

Storage 서비스 암호화는 모든 신규 및 기존 저장소 계정에 대해 사용되도록 설정되며 비활성화할 수 없습니다. 사용자 데이터는 기본적으로 보호되므로 Storage 서비스 암호화를 활용하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다.

이 기능은 다음의 데이터를 자동으로 암호화합니다.

- 두 성능 계층(Standard 및 Premium)
- 두 배포 모델(Azure Resource Manager 및 클래식)
- 모든 Azure Storage 서비스(Blob Storage, Queue Storage, Table Storage 및 Azure Files) 

Storage 서비스 암호화는 Azure Storage 성능에 영향을 주지 않습니다.

Storage 서비스 암호화를 사용하는 Microsoft 관리 암호화 키를 사용하거나 사용자 고유의 암호화 키를 사용할 수 있습니다. 자체 키 사용에 대한 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md)를 참조하세요.

## <a name="view-encryption-settings-in-the-azure-portal"></a>Azure Portal에서 암호화 설정 보기

Storage 서비스 암호화의 설정을 보려면 [Azure Portal](https://portal.azure.com)에 로그인한 후 저장소 계정을 선택합니다. **설정** 창에서 **암호화** 설정을 선택합니다.

![암호화 설정을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Storage 서비스 암호화에 대한 FAQ

**Q: 클래식 저장소 계정이 있습니다. 해당 계정에 대해 Storage 서비스 암호화를 사용하도록 설정할 수 있나요?**

A: Storage 서비스 암호화는 기본적으로 모든 저장소 계정(클래식 및 Resource Manager)에 대해 사용되도록 설정됩니다.

**Q: 내 클래식 저장소 계정에서 데이터를 암호화하려면 어떻게 해야 하나요?**

A: 암호화가 기본적으로 활성화되어 있으면 Azure Storage에서 새 데이터를 자동으로 암호화합니다. 

**Q: Resource Manager 저장소 계정이 있습니다. 해당 계정에 대해 Storage 서비스 암호화를 사용하도록 설정할 수 있나요?**

A: Storage 서비스 암호화는 모든 기존 Resource Manager 저장소 계정에서 기본적으로 활성화됩니다. 이 기능은 Blob Storage, Table Storage, Queue Storage 및 Azure Files에서 지원됩니다. 

**Q: Resource Manager 저장소 계정에서 데이터를 암호화하려면 어떻게 해야 하나요?**

A: Storage 서비스 암호화는 기본적으로 모든 저장소 계정(클래식 및 Resource Manager)에 대해 활성화되고, 암호화가 활성화되기 전에 만들어진 저장소 계정의 모든 기존 파일은 배경 암호화 프로세스에 의해 소급되어 암호화됩니다.

**Q: Azure PowerShell 및 Azure CLI를 사용하여 Storage 서비스 암호화가 사용되도록 설정된 저장소 계정을 만들 수 있나요?**

A: Storage 서비스 암호화는 기본적으로 모든 저장소 계정(클래식 또는 Resource Manager)을 만들 때 활성화됩니다. Azure PowerShell과 Azure CLI를 사용하여 계정 속성을 확인할 수 있습니다.

**Q: Storage 서비스 암호화를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?**

A: 추가 비용은 없습니다.

**Q: 나만의 암호화 키를 사용할 수 있나요?**

A: 예. 사용자 고유의 암호화 키를 사용할 수 있습니다. 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md)를 참조하세요.

**Q: 암호화 키에 대한 액세스를 해지할 수 있나요?**

A: 예. Azure Key Vault에서 [사용자 고유의 암호화 키를 사용하는](storage-service-encryption-customer-managed-keys.md) 경우입니다.

**Q: 저장소 계정을 만들 경우 Storage 서비스 암호화가 기본적으로 사용되나요?**

A: Storage 서비스 암호화는 기본적으로 모든 저장소 계정 및 모든 Azure Storage 서비스에 대해 사용되도록 설정됩니다.

**Q: Azure Disk Encryption과 어떻게 다른가요?**

A: Azure Disk Encryption은 IaaS VM에서 OS 및 데이터 디스크를 암호화하는 데 사용됩니다. 자세한 내용은 [저장소 보안 가이드](../storage-security-guide.md)를 참조하세요.

**Q: 내 데이터 디스크에 대해 Azure Disk Encryption을 사용하도록 설정하면 어떻게 되나요?**

A: 이것은 원활하게 작동합니다. 두 방법 모두 데이터를 암호화합니다.

**Q: 내 저장소 계정이 지역 중복되어 복제되도록 설정됩니다. Storage 서비스 암호화를 사용할 경우 중복 복사본도 암호화되나요?**

A: 예. 저장소 계정의 모든 복사본이 암호화됩니다. 로컬 중복 저장소, 영역 중복 저장소, 지역 중복 저장소 및 읽기 액세스 지역 중복 저장소를 비롯한 모든 중복 옵션이 지원됩니다.

**Q: 내 저장소 계정에서 암호화를 사용하지 않도록 설정할 수 있나요?**

A: 암호화는 기본적으로 사용되도록 설정되어 있으며 저장소 계정에 대한 암호화를 사용하지 않도록 설정하는 옵션은 없습니다. 

**Q: Storage 서비스 암호화는 특정 지역에서만 허용되나요?**

A: Storage 서비스 암호화는 모든 지역에서 모든 서비스에 사용할 수 있습니다. 

**Q: 문제가 있거나 피드백을 제공하고 싶은 경우 어떻게 연락하나요?**

A: Storage 서비스 암호화에 대한 문제나 의견이 있으면 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계
Azure Storage는 여러 개발자가 보안 응용 프로그램을 빌드하도록 지원하는 포괄적인 보안 기능을 제공합니다. 자세한 내용은 [저장소 보안 가이드](../storage-security-guide.md)를 참조하세요.
