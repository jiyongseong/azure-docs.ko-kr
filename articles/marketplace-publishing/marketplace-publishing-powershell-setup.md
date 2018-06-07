---
title: Marketplace에 대한 VM을 만들기 위해 PowerShell 설정 | Microsoft Docs
description: Azure PowerShell을 설정하고 선택적 프로세스 흐름으로 사용하여 Azure Marketplace에 배포하고 여기에서 판매할 VM 이미지를 만들기 위한 지침
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Azure Marketplace에 대한 제품을 만들기 위한 Azure PowerShell 설정
Azure에서 PowerShell을 설정하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 간단한 방법은 인증 방법을 사용하는 것으로, 인증에 필요한 인증서를 다운로드하고 가져옵니다. 필요한 인증서를 가져오려면 **Get-AzurePublishSettingsFile** cmdlet을 사용합니다. 메시지가 나타나면 파일을 저장합니다. 인증서를 PowerShell 세션으로 가져오려면 **Import-AzurePublishSettingsFile** cmdlet을 사용합니다.

PowerShell 세션에 대한 일반적인 Microsoft Azure 구독 설정을 구성하고 저장하려면 **Set-AzureSubscription** 및 **Select-AzureSubscription** cmdlet을 사용합니다.

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

첫 번째 명령은 기본 저장소 계정을 구독에 연결합니다(일부 VM 프로비저닝 작업에 필요).  두 번째 명령은 구독을 현재 구독으로 만듭니다(다른 cmdlet에서 인식).

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)
* [Marketplace에 대한 가상 머신 이미지 만들기](marketplace-publishing-vm-image-creation.md)

