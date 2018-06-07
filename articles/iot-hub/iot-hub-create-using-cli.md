---
title: Azure CLI(az.py)를 사용하여 IoT Hub 만들기 | Microsoft Docs
description: 플랫폼 간 Azure CLI 2.0(az.py)을 사용하여 Azure IoT hub를 만드는 방법
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: c89ecc31754d355d6469d54dcd45e1cc2ef106f0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개

Azure CLI 2.0(az.py)을 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리할 수 있습니다. 이 문서는 Azure CLI 2.0(az.py)을 사용하여 IoT Hub를 만드는 방법을 보여줍니다.

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* [Azure CLI(azure.js)](iot-hub-create-using-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI
* Azure CLI 2.0(az.py) - 이 문서에 설명된 대로 리소스 관리 배포 모델용 차세대 CLI입니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 내에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
* [Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Azure 계정 로그인 및 설정

Azure 계정에 로그인하고 구독을 선택합니다.

1. 명령 프롬프트에서 [login 명령][lnk-login-command]을 실행합니다.
    
    ```azurecli
    az login
    ```

    지침에 따라 코드를 사용하여 인증하고 웹 브라우저를 통해 Azure 계정에 로그인합니다.

2. 여러 Azure 구독이 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 계정에 대한 액세스를 허용합니다. 다음 [Azure 계정을 나열하는 명령][lnk-az-account-command]을 사용합니다.
    
    ```azurecli
    az account list 
    ```

    다음 명령을 사용하여 IoT Hub를 만드는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure CLI를 사용하여 리소스 그룹을 만든 다음 IoT Hub를 추가합니다.

1. IoT Hub는 리소스 그룹에서 만들어야 합니다. 기존 리소스 그룹을 사용하거나 다음 [리소스 그룹을 만드는 명령][lnk-az-resource-command]을 실행합니다.
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 이전 예제에서는 미국 서부 위치에서 리소스 그룹을 만듭니다. `az account list-locations -o table` 명령을 실행하여 사용할 수 있는 위치의 목록을 볼 수 있습니다.
    >
    >

2. 리소스 그룹에서 IoT Hub에 대해 전역 고유 이름을 사용하여 다음 [IoT Hub를 만드는 명령][lnk-az-iot-command]을 실행합니다.
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> 이전 명령은 청구 대상인 S1 가격 책정 계층에 IoT Hub를 만듭니다. 자세한 내용은 [Azure IoT Hub 가격 책정][lnk-iot-pricing]을 참조하세요.
>

## <a name="remove-an-iot-hub"></a>IoT Hub 제거

Azure CLI를 사용하여 IoT Hub 같은 [개별 리소스 삭제][lnk-az-resource-command] 또는 IoT Hub를 포함한 리소스 그룹 및 모든 해당 리소스를 삭제할 수 있습니다.

IoT Hub를 삭제하려면 다음 명령을 실행합니다.

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

리소스 그룹 및 모든 해당 리소스를 삭제하려면 다음 명령을 실행합니다.

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>다음 단계
IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure Portal을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
