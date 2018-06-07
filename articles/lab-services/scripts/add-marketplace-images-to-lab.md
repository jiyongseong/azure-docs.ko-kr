---
title: 'PowerShell 스크립트: 마켓플레이스 이미지를 Azure Lab Services의 사용자 지정 랩에 추가 | Microsoft Docs'
description: 이 PowerShell 스크립트는 마켓플레이스 이미지를 Azure Lab Services의 사용자 지정 랩에 추가합니다.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 64d168c132edce4ecd128b795fbfa5ab2607cb19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-custom-lab"></a>PowerShell을 사용하여 마켓플레이스 이미지를 사용자 지정 랩에 추가

이 샘플 PowerShell 스크립트는 마켓플레이스 이미지를 Azure Lab Services의 사용자 지정 랩에 추가합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>필수 조건
* **사용자 지정 랩**. 스크립트를 사용하려면 기존 사용자 지정 랩이 있어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a custom lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | 지정된 매개 변수를 기반으로 리소스를 검색합니다. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | 리소스를 가져옵니다. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | 리소스를 수정합니다. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | 리소스를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Lab Services PowerShell 스크립트 샘플은 [Azure Lab Services PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.