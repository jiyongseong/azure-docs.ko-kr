---
title: Azure Cloud Shell의 PowerShell(미리 보기)에서 파일 유지 | Microsoft Docs
description: Azure Cloud Shell에서 파일을 유지하는 방법의 연습입니다.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154401"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud Shell의 PowerShell(미리 보기) 작동 방식
Azure Cloud Shell의 PowerShell(미리 보기)은 다음 방법을 통해 파일을 유지합니다. 
* 직접 파일 공유의 상호 작용을 위해 `$Home` 디렉터리에서 지정된 Azure 파일 공유를 `clouddrive`로 마운트합니다.

## <a name="list-clouddrive-azure-file-shares"></a>`clouddrive` Azure 파일 공유 나열
`Get-CloudDrive` 명령은 Azure Cloud Shell에서 `clouddrive`에 현재 탑재된 Azure 파일 공유 정보를 검색합니다. <br>
![Get-CloudDrive 실행](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>`clouddrive` 마운트 해제
언제든지 Cloud Shell에 마운트된 Azure 파일 공유의 마운트를 해제할 수 있습니다. Azure 파일 공유가 제거된 경우 다음 세션에서 새 Azure 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.

`Dismount-CloudDrive` 명령은 현재 저장소 계정에서 Azure 파일 공유를 탑재 해제합니다. `clouddrive`를 탑재 해제하면 현재 세션이 종료됩니다. 사용자에게 다음 세션에서 새 Azure 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.
![CloudDrive 탑재 해제 실행](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>다음 단계
[PowerShell에 대한 빠른 시작](quickstart-powershell.md) <br>
[Azure Files에 대해 알아보기](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[저장소 태그에 대해 알아보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>