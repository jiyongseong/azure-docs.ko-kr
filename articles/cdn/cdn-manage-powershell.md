---
title: PowerShell을 사용하여 Azure CDN 관리 | Microsoft Docs
description: Azure PowerShell cmdlet을 사용하여 Azure CDN을 관리하는 방법에 대해 알아봅니다.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5634ecdec04f023d9eb901c4ad0fb21b13bcfdc1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="manage-azure-cdn-with-powershell"></a>PowerShell을 사용하여 Azure CDN 관리
PowerShell은 Azure CDN 프로필 및 끝점을 관리하는 매우 유연한 방법 중 하나를 제공합니다.  PowerShell을 대화형으로 또는 관리 작업을 자동화하는 스크립트를 작성하여 사용할 수 있습니다.  이 자습서에서는 PowerShell을 사용하여 Azure CDN 프로필 및 끝점의 관리를 달성할 수 있는 가장 일반적인 작업 몇 가지를 보여줍니다.

## <a name="prerequisites"></a>필수 조건
PowerShell을 사용하여 Azure CDN 프로필 및 끝점을 관리하려면 Azure PowerShell 모듈이 설치되어 있어야 합니다.  Azure PowerShell을 설치하고 `Connect-AzureRmAccount` cmdlet을 사용하여 Azure에 연결하는 방법을 알아보려면 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

> [!IMPORTANT]
> `Connect-AzureRmAccount`에 로그인해야 Azure PowerShell cmdlet을 실행할 수 있습니다.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Azure CDN cmdlet 목록화
`Get-Command` cmdlet을 사용하여 모든 Azure CDN cmdlet을 나열할 수 있습니다.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>도움말 보기
`Get-Help` cmdlet을 사용하여 이러한 cmdlet에 대한 도움말을 볼 수 있습니다.  `Get-Help` 는 사용량 및 구문을 제공하며 선택적으로 예제를 표시합니다.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>기존 Azure CDN 프로필 목록화
매개 변수 없는 `Get-AzureRmCdnProfile` cmdlet은 모든 기존 CDN 프로필을 검색합니다.

```powershell
Get-AzureRmCdnProfile
```

이 출력은 열거형에 대한 cmdlet으로 파이프될 수 있습니다.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

또한 프로필 이름 및 리소스 그룹을 지정하여 단일 프로필을 반환할 수 있습니다.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> 다른 리소스 그룹에 속하기만 하다면 같은 이름을 가진 여러 CDN 프로필이 있을 수 있습니다.  `ResourceGroupName` 매개 변수를 생략함년 일치하는 이름을 가진 모든 프로필이 반환됩니다.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>기존 CDN 끝점 목록화
`Get-AzureRmCdnEndpoint` 은 개별 끝점 또는 프로필의 모든 끝점을 검색할 수 있습니다.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN 프로필 및 끝점 만들기
`New-AzureRmCdnProfile` 및 `New-AzureRmCdnEndpoint`는 CDN 프로필 및 끝점을 만드는 데 사용됩니다.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>끝점 이름 가용성 확인
`Get-AzureRmCdnEndpointNameAvailability` 은 끝점 이름을 사용할 수 있는지 여부를 나타내는 개체를 반환합니다.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>사용자 지정 도메인 추가
`New-AzureRmCdnCustomDomain` 은 기존 끝점에 사용자 지정 도메인 이름을 추가합니다.

> [!IMPORTANT]
> [사용자 지정 도메인을 Content Delivery Network(CDN) 끝점에 매핑하는 방법](cdn-map-content-to-custom-domain.md)에서 설명한 대로 DNS 공급자를 통해 CNAME을 설정해야 합니다.  `Test-AzureRmCdnCustomDomain`를 사용하여 끝점을 수정하기 전에 매핑을 시험해야 합니다.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>끝점 수정
`Set-AzureRmCdnEndpoint` 은 기존 끝점을 수정합니다.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>CDN 자산 제거/사전 로드
`Unpublish-AzureRmCdnEndpointContent`은 캐시된 자산을 제거하며, `Publish-AzureRmCdnEndpointContent`는 자산을 지원되는 끝점에 사전 로드합니다.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>CDN 끝점 시작/중지
`Start-AzureRmCdnEndpoint` 및 `Stop-AzureRmCdnEndpoint`를 사용하여 개별 끝점 또는 끝점 그룹을 시작 및 중지할 수 있습니다.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN 리소스 삭제
`Remove-AzureRmCdnProfile` 및 `Remove-AzureRmCdnEndpoint`를 사용하여 프로필 및 끝점을 제거할 수 있습니다.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>다음 단계
[.NET](cdn-app-dev-net.md) 또는 [Node.js](cdn-app-dev-node.md)를 사용하여 Azure CDN을 자동화하는 방법을 알아봅니다.

CDN 기능에 대해 알아보려면 [CDN 개요](cdn-overview.md)를 참조하세요.

