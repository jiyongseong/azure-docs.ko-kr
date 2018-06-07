---
title: "PowerShell을 사용하여 클라우드 서비스 컨테이너 만들기 | Microsoft Docs"
description: "이 문서에서는 PowerShell을 사용하여 클라우드 서비스 컨테이너를 만드는 방법을 설명합니다. 컨테이너는 웹 및 작업자 역할을 호스트합니다."
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Azure PowerShell 명령을 사용하여 빈 클라우드 서비스 컨테이너 만들기
이 문서에서는 Azure PowerShell cmdlet을 사용하여 신속하게 Cloud Services 컨테이너를 만드는 방법을 설명합니다. 다음 단계를 따르세요.

1. [Azure PowerShell 다운로드](http://aka.ms/webpi-azps) 페이지에서 Microsoft Azure PowerShell cmdlet을 설치합니다.
2. PowerShell 명령 프롬프트를 엽니다.
3. [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) 를 사용하여 로그인합니다.

   > [!NOTE]
   > Azure PowerShell cmdlet을 설치하고 Azure 구독에 연결하는 방법에 대한 자세한 지침은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.
   >
   >
4. **New-AzureService** cmdlet을 사용하여 빈 Azure 클라우드 서비스 컨테이너를 만듭니다.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. 이 예제를 따라 cmdlet을 호출합니다.

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Azure 클라우드 서비스 만들기에 대한 자세한 내용을 보려면 다음을 실행하세요.

```
Get-help New-AzureService
```

### <a name="next-steps"></a>다음 단계
* 클라우드 서비스 배포를 관리하려면 [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0) 및 [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0) 명령을 참조하세요. 더욱 자세한 내용을 보려면 [클라우드 서비스를 구성하는 방법](cloud-services-how-to-configure-portal.md) 을 참조하세요.
* 클라우드 서비스 프로젝트를 Azure에 게시하려면, **PublishCloudService.ps1** 코드 샘플을 [보관된 클라우드 서비스 리포지토리](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)에서 참조하세요.
