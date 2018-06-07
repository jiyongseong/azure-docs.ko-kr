---
title: Azure PowerShell 스크립트 샘플 - GitHub의 연속 배포를 사용하여 웹앱 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - GitHub의 연속 배포를 사용하여 웹앱 만들기
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8185edf1176f5215065b8154e584703069d371a2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>GitHub의 연속 배포를 사용하여 웹앱 만들기

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 GitHub 리포지토리의 연속 배포를 설정합니다. 연속 배포를 사용하지 않는 GitHub 배포는 [웹앱 만들기 및 GitHub의 코드 배포](app-service-powershell-deploy-github.md)를 참조하세요.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzureRmAccount`를 실행하여 Azure에 연결합니다. 또한 다음 사항을 확인합니다.

- `az login` 명령을 사용하여 Azure와 연결했습니다.
- 응용 프로그램 코드는 사용자가 소유한 공용 또는 개인 GitHub 리포지토리에 있습니다.
- [GitHub 계정에서 액세스 토큰을 만들](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)었습니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 웹앱을 만듭니다. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | 리소스 그룹에서 리소스를 수정합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../app-service-powershell-samples.md)에서 확인할 수 있습니다.
