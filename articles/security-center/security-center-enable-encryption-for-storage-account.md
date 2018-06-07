---
title: Azure Security Center에서 저장소 계정에 암호화 사용 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **Azure Storage 계정에 암호화 사용**을 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
ms.openlocfilehash: 82bb201c0b518d0b45e06a1eb25d54f60cb3e028
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Azure Security Center에서 Azure Storage 계정에 암호화 사용
Azure Security Center에서는 미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하도록 권장합니다.

SSE(Storage 서비스 암호화)는 Azure Storage에 기록되는 데이터를 암호화하고 검색 전 해당 데이터를 해독하는 방식으로 작동합니다.  SSE는 현재 Azure Blob service에만 사용할 수 있으며 블록 blob, 페이지 blob 및 추가 blob에 사용할 수 있습니다.  자세한 내용을 알아보려면 [미사용 데이터에 대한 Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.


> [!Note]
> 암호화를 사용하도록 설정하면 새 데이터만 암호화됩니다. 저장소 계정의 모든 기존 blob은 암호화되지 않은 상태로 유지됩니다. 기존 blob을 암호화하려면 [Storage 서비스 암호화 FAQ](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption)를 참조하세요.
>
>

Storage 서비스 암호화는 Resource Manager 저장소 계정에만 지원됩니다. 클래식 저장소 계정은 현재 지원되지 않습니다. 클래식 및 Resource Manager 배포 모델을 이해하려면 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항** 블레이드에서 **Azure Storage 계정에 암호화 사용**을 선택합니다.
   ![저장소 계정에 암호화 사용][1]
2. **저장소 암호화 사용** 블레이드가 열립니다. 이 블레이드에는 저장소 암호화를 사용하지 않도록 설정된 Azure Storage 계정이 나열됩니다. 이 예제에서는 **storageacct1**을 선택해 보겠습니다.
   ![저장소 암호화 사용][2]
3. **storageacct1**에 대한 **암호화** 블레이드가 열립니다. **사용**을 선택합니다.
   ![암호화 블레이드][3]
4. **저장**을 선택합니다.

이제 **storageacct1**에 대한 저장소 암호화가 사용하도록 설정되었습니다.


## <a name="see-also"></a>참고 항목
이 문서에서는 Security Center 권장 사항 "Azure Storage 계정에 암호화 사용"을 구현하는 방법을 보여 줍니다. Azure Storage 서비스 암호화에 대한 자세한 내용은 다음을 참조하세요.

* [휴지 상태의 데이터에 대한 Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
