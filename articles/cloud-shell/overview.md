---
title: "Azure Cloud Shell 개요 | Microsoft Docs"
description: "Azure Cloud Shell의 개요입니다."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 72338a4c168b4d3b7c918fbb16758724f73fefc2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 개요
Azure Cloud Shell은 Azure 리소스를 관리하기 위한 브라우저에서 액세스할 수 있는 대화형 셸입니다.
작업 방식에 가장 적합한 셸 환경을 유연하게 선택할 수 있습니다.
Linux 사용자는 Bash 환경을 선택할 수 있으며, Windows 사용자는 PowerShell을 선택할 수 있습니다.

이 단추를 사용하여 shell.azure.com에서 시도하세요.

[![](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)

Cloud Shell 아이콘을 사용하여 Azure Portal에서 시도하세요.

![포털 시작](media/overview/portal-launch-icon.png)

## <a name="features"></a>기능
### <a name="browser-based-shell-experience"></a>브라우저 기반 셸 환경
Cloud Shell은 Azure 관리 작업을 사용하여 빌드된 브라우저 기반 명령줄 환경에 액세스할 수 있습니다.
Cloud Shell을 활용하여 클라우드가 제공할 수 있는 방식으로 로컬 컴퓨터에서 제한되지 않고 작업할 수 있습니다.

### <a name="choice-of-preferred-shell-experience"></a>기본 설정된 셸 환경 선택
Linux 사용자는 Cloud Shell의 Bash를 사용할 수 있는 반면 Windows 사용자는 쉘 드롭다운에서 Cloud Shell의 PowerShell(미리 보기)을 선택할 수 있습니다.

![Cloud Shell의 Bash](media/overview/overview-bash-pic.png)

![Azure Cloud Shell의 PowerShell(미리 보기)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>인증 및 구성된 Azure 워크스테이션
Cloud Shell은 Microsoft에서 관리되므로 널리 사용되는 명령줄 도구 및 언어 지원을 제공합니다. 또한 Cloud Shell은 Azure CLI 2.0 또는 Azure PowerShell cmdlet을 통해 리소스에 즉시 액세스하도록 자동으로 안전하게 인증합니다.

[Bash 환경](features.md#tools) 및 [PowerShell(미리 보기) 환경](features-powershell.md#tools)에 대한 전체 도구 목록을 확인합니다.

### <a name="multiple-access-points"></a>여러 액세스 포인트
Cloud Shell은 다음에서 사용할 수 있는 유연한 도구입니다.
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 2.0 "실행" 문서](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Azure 모바일 앱](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [VS Code Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Microsoft Azure Files 저장소 연결
Cloud Shell 컴퓨터는 임시이며 Azure Files 공유를 `clouddrive`로 탑재하도록 하여 파일을 유지해야 합니다.

Cloud Shell를 첫 번째로 시작할 때 리소스 그룹, 저장소 계정 및 Azure Files 공유를 만들라는 메시지가 표시됩니다. 이는 일회성 단계이며 모든 세션에서 자동으로 연결됩니다. Azure Cloud Shell의 Bash 및 PowerShell(미리 보기) 모두에 의해 단일 파일 공유가 매핑될 수 있으며 사용될 것입니다.

#### <a name="create-new-storage"></a>새 저장소 만들기
![](media/overview/basic-storage.png)

사용자를 대신해서 LRS(로컬 중복 저장소) 계정 및 Azure Files 공유를 만들 수 있습니다. 둘 다 사용하려 한다면 Azure Files 공유를 Bash 및 PowerShell 환경에 대해 사용합니다. 일반 저장소 비용이 적용됩니다.

세 가지 리소스가 자동으로 만들어집니다.
1. 리소스 그룹: `cloud-shell-storage-<region>`
2. Storage 계정: `cs<uniqueGuid>`
3. 파일 공유: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> 또한 Azure Cloud Shell의 Bash는 `$Home`을 유지하도록 기본 5GB 디스크 이미지를 만듭니다. SSH 키와 같이 $Home 디렉터리의 모든 파일은 탑재된 Azure 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. $Home 디렉터리 및 탑재된 Azure 파일 공유에서 파일을 저장하는 경우 모범 사례를 적용합니다.

#### <a name="use-existing-resources"></a>기존 리소스 사용
![](media/overview/advanced-storage.png)

기존 리소스를 Azure Cloud Shell에 연결하도록 고급 옵션이 제공됩니다.
저장소 설정 프롬프트에서 "고급 설정 표시"를 클릭하면 추가 옵션이 표시됩니다.

> [!Note]
> 사전 할당된 Cloud Shell 하위 지역 및 LRS/GRS/ZRS 저장소 계정에 드롭다운이 필터링됩니다.

[Azure Cloud Shell 저장소, Azure 파일 공유 업데이트 및 파일 업로드/다운로드에 대해 자세히 알아봅니다.](persisting-shell-storage.md)

## <a name="concepts"></a>개념
* Cloud Shell은 세션 별, 사용자 단위 기준으로 제공된 임시 호스트에서 실행됩니다.
* Cloud Shell은 대화형 작업 없이 20분 후에 시간이 초과됩니다.
* Cloud Shell은 Azure 파일 공유를 탑재해야 합니다.
* Cloud Shell은 Bash 및 PowerShell 모두에 동일한 Azure 파일 공유를 사용합니다.
* Cloud Shell은 사용자 계정 별로 하나의 컴퓨터를 할당합니다.
* Bash는 파일 공유에 보관된 5GB 이미지를 사용하여 $Home을 유지합니다.
* 사용 권한은 Bash의 일반적인 Linux 사용자로 설정됩니다.

[Azure Cloud Shell의 Bash](features.md)와 [Azure Cloud Shell의 PowerShell(미리 보기)](features-powershell.md)에 들어 있는 기능에 대해 자세히 알아 보세요.

## <a name="pricing"></a>가격
탑재된 Azure Files 공유의 필수 구성 요소를 포함하여 Azure Cloud Shell을 호스트하는 컴퓨터는 추가 비용 없이 사용할 수 있습니다. 일반 저장소 비용이 적용됩니다.

## <a name="next-steps"></a>다음 단계
[Cloud Shell의 Bash 빠른 시작](quickstart.md) <br>
[Cloud Shell의 PowerShell(미리 보기) 빠른 시작](quickstart-powershell.md)