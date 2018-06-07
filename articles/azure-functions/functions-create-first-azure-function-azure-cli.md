---
title: "Azure CLI에서 첫 번째 함수 만들기 | Microsoft Docs"
description: "Azure CLI를 사용하여 서버를 사용하지 않는 실행을 위해 첫 번째 Azure Function을 만드는 방법을 알아봅니다."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 01/24/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 2088844693748f090a67ad56f9b5fba4514d1282
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Azure CLI를 사용하여 첫 번째 함수 만들기

이 빠른 시작 토픽에서는 Azure Functions를 사용하여 첫 번째 함수를 만드는 방법을 설명합니다. Azure CLI를 사용하여 함수를 호스트하고 [서버를 사용하지 않는](https://azure.microsoft.com/overview/serverless-computing/) 인프라인 함수 앱을 만듭니다. 함수 코드 자체는 GitHub 샘플 리포지토리에서 배포됩니다.    

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다. 

## <a name="prerequisites"></a>필수 조건 

이 샘플을 실행하기 전에 다음이 있어야 합니다.

+ 활성 [GitHub](https://github.com) 계정 
+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령을 사용하여 함수 앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. _deployment-source-url_ 매개 변수는 "Hello World" HTTP 트리거 함수를 포함하는 GitHub의 샘플 리포지토리입니다.

```azurecli-interactive
az functionapp create --deployment-source-url https://github.com/Azure-Samples/functions-quickstart  \
--resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```
_consumption-plan-location_ 매개 변수를 설정하는 것은 해당 함수 앱이 소비 호스팅 계획에서 호스팅된다는 뜻입니다. 이 계획에서는 리소스가 필요에 따라 함수를 통해 동적으로 추가되며 함수가 실행된 것에 대해서만 지불합니다. 자세한 내용은 [올바른 호스팅 계획 선택](functions-scale.md)을 참조하세요. 

함수 앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
