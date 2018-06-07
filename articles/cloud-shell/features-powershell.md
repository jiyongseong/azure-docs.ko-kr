---
title: Azure Cloud Shell의 PowerShell(미리 보기) 기능 | Microsoft Docs
description: Azure Cloud Shell의 PowerShell 기능의 개요
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: damaerte
ms.openlocfilehash: 209ca603a3826c6516444ea4f3f4fe592e3b3f2e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302610"
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Azure Cloud Shell의 PowerShell(미리 보기) 기능 및 도구

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> 또한 [Bash](features.md)에 대한 기능 및 도구를 사용할 수 있습니다.

Azure Cloud Shell의 PowerShell(미리 보기)는 `Windows Server 2016`에서 실행됩니다.

## <a name="features"></a>기능

### <a name="secure-automatic-authentication"></a>안전한 자동 인증

Azure Cloud Shell의 PowerShell(미리 보기)은 Microsoft Azure PowerShell에 대한 계정 액세스를 안전하고 자동으로 인증합니다.

### <a name="files-persistence-across-sessions"></a>여러 세션에 걸친 파일 지속성

세션 간에 파일을 유지하기 위해 Cloud Shell에서는 처음 시작할 때 Microsoft Azure 파일 공유 연결을 안내합니다.
작업이 완료되면 Azure Cloud Shell은 이후의 모든 세션에 대해 저장소(`$home\clouddrive`로 장착됨)를 자동으로 연결합니다.
Azure Cloud Shell에 대한 요청은 제각기 임시 컴퓨터를 할당하므로 `$home\clouddrive` 외부에 있는 파일 및 컴퓨터 상태는 세션 간에 유지되지 않습니다.

[Azure 파일 공유를 Cloud Shell에 연결하는 방법에 대해 자세히 알아봅니다.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Azure 드라이브(Azure:)

Azure Cloud Shell의 PowerShell(미리 보기)은 Azure 드라이브(`Azure:`)에서 시작됩니다.
Azure 드라이브는 파일 시스템 탐색과 비슷하게 Compute, 네트워크, Storage 등과 같은 Azure 리소스를 쉽게 검색 및 탐색할 수 있습니다.
이러한 리소스를 관리하기 위해 친숙한 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure)을 계속 사용할 수 있습니다.
Azure Portal에서 또는 Azure PowerShell cmdlet을 통해 직접 만들어진 Azure 리소스에 대한 변경 내용은 Azure 드라이브에 즉시 반영됩니다.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>컨텍스트 인식
- **리소스 그룹 범위 지정**: Azure 드라이브(`Azure:`)의 리소스 그룹 경로 컨텍스트 내에서, 리소스 그룹 이름은 Azure PowerShell cmdlet에 자동으로 전달됩니다.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get AzureRmCommand**: 이 cmdlet는 Azure 드라이브(`Azure:`) 아래 위치의 컨텍스트에서 적용할 수 있는 해당 명령의 목록을 반환합니다. 예를 들어, 사용자가 `Azure:\<subscription name>\StorageAccounts`에 있다면 저장소 관련 명령 만을 보여줍니다.

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Rich PowerShell 스크립트 편집

VIM을 사용하여 PowerShell 파일(`.ps1,.psm1,.psd1`)을 편집하려면, 자동으로 구문 강조 표시 및 IntelliSense 지원을 가져옵니다.
IntelliSense 지원은 [PowerShell 편집기 서비스](https://github.com/PowerShell/PowerShellEditorServices)의 로컬 인스턴스와 상호 작용하는 vim 플러그인을 통해 구현됩니다.

> [!TIP]
> 명령 이름, 매개 변수 이름 및 매개 변수값(있는 경우)에서 완성(IntelliSense)을 보려면 `TAB`을 사용합니다.

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>확장 가능 모델

[PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget)을 사용하여 [PowerShell 갤러리](https://www.powershellgallery.com)에서 사용자 지정 모듈과 스크립트를 쉽게 설치(및 업데이트)할 수 있습니다.
설치가 끝나면 모듈이 Azure Cloud Shell 세션 전반에 걸쳐 자동으로 유지됩니다.

> [!TIP]
> 사용자가 설치한 모듈은 `$Home\clouddrive\.pscloudshell\WindowsPowerShell` 폴더에 저장됩니다. 이 폴더에 대한 바로 가기 링크는 사용자의 문서 폴더(`$home\Documents\WindowsPowerShell`)에 만들어집니다.

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>게스트 VM의 관리

두 개의 기본 제공 명령 `Enter-AzureRmVM`과 `Invoke-AzureRmVMCommand`를 사용하여 Azure VM을 원격으로 관리할 수 있습니다.
이러한 명령은 PowerShell 원격 기능을 기반으로 구축되며 Azure VM에 대한 PowerShell 연결을 필요로 합니다.

## <a name="tools"></a>도구

|**범주**    |**Name**                                 |
|----------------|-----------------------------------------|
|Azure 도구     |[Azure PowerShell(6.0.1)](https://docs.microsoft.com/powershell/azure/overview)<br> [Azure CLI(2.0.32)](https://docs.microsoft.com/cli/azure)|
|텍스트 편집기    |vim<br> nano                             |
|패키지 관리자 |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|소스 제어  |git                                      |
|데이터베이스       |[SqlServer 모듈](https://www.powershellgallery.com/packages/SqlServer)<br> [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|테스트 도구      |Pester                                   |

## <a name="language-support"></a>언어 지원

|**언어**|**버전**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |8.9.4       |
|PowerShell  |5.1 및 [6.0.2](https://github.com/PowerShell/powershell/releases)       |
|파이썬      |2.7        |

## <a name="next-steps"></a>다음 단계

[Azure Cloud Shell의 PowerShell(미리 보기) 빠른 시작](quickstart-powershell.md)

[Microsoft Azure PowerShell에 관해 알아보기](https://docs.microsoft.com/powershell/azure/)
