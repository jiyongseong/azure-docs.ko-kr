---
title: PowerShell을 사용하여 Azure Analysis Services 서버 만들기 | Microsoft Docs
description: PowerShell을 사용하여 Azure Analysis Services 서버를 만드는 방법 알아보기
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3f0d3ae6786e9f63f0e4eb025118d0d217eced64
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>PowerShell을 사용하여 Azure Analysis Services 서버 만들기

이 빠른 시작은 Azure 구독의 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)에서 Azure Analysis Services 서버를 만들기 위해 명령줄에서 PowerShell 사용에 대해 설명합니다.

이 작업을 수행하려면 Azure PowerShell 모듈 버전 4.0 이상이 필요합니다. 버전을 확인하려면 ` Get-Module -ListAvailable AzureRM`을 실행합니다. 설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

> [!NOTE]
> 서버를 만들면 새로운 유료 서비스가 생성될 수 있습니다. 자세한 내용은 [Analysis Services 가격 책정](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
이 빠른 시작을 완료하려면 다음이 필요합니다.

* **Azure 구독**: [Azure 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/)으로 이동하여 계정을 만들 수 있습니다.
* **Azure Active Directory**: 구독이 Azure Active Directory 테넌트와 연결되어 있고 해당 디렉터리에 계정이 있어야 합니다. 자세한 내용은 [인증 및 사용자 권한](analysis-services-manage-users.md)을 참조하세요.

## <a name="import-azurermanalysisservices-module"></a>AzureRm.AnalysisServices 모듈 가져오기
구독에서 서버를 만들려면 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 구성 요소 모듈을 사용합니다. PowerShell 세션으로 AzureRm.AnalysisServices 모듈을 로드 합니다.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 명령을 사용하여 Azure 구독에 로그인합니다. 화면에 나타나는 지침에 따릅니다.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 서버를 만들 때 구독에서 리소스 그룹을 지정해야 합니다. 아직 리소스 그룹이 없는 경우 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용하여 새 리소스 그룹을 만들 수 있습니다. 다음 예제에서는 미국 서부 지역의 `myResourceGroup`라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>서버 만들기

[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 명령을 사용하여 새 서버를 만듭니다. 다음 예제에서는 D1 계층, 미국 서부 지역, myResourceGroup에서 myServer라는 서버를 만들고 서버 관리자로 philipc@adventureworks.com을 지정합니다.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>리소스 정리

[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 명령을 사용하여 구독에서 서버를 제거할 수 있습니다. 이 컬렉션의 다른 빠른 시작 및 자습서로 계속하는 경우에는 서버를 제거하지 마세요. 다음 예제에서는 이전 단계에서 만든 서버를 제거합니다.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계
[PowerShell을 사용하여 Azure Analysis Services 관리](analysis-services-powershell.md)
[SSDT에서 모델 배포](analysis-services-deploy.md)
[Azure Portal에서 모델 만들기](analysis-services-create-model-portal.md)
