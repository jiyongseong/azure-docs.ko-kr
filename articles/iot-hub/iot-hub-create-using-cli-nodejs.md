---
title: Azure CLI(azure.js)를 사용하여 IoT Hub 만들기 | Microsoft Docs
description: 플랫폼 간 Azure CLI(azure.js)를 사용하여 Azure IoT hub를 만드는 방법
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 7e761ba66f4b28dd7cc59dc8998c23810dd2b7d1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLI를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개

Azure CLI 2.0(azure.js)을 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리할 수 있습니다. 이 문서는 Azure CLI(azure.js)를 사용하여 IoT Hub를 만드는 방법을 보여줍니다.

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

* Azure CLI(azure.js) - 이 문서에 설명된 것처럼 클래식 및 리소스 관리 배포 모델용 CLI입니다.
* [Azure CLI 2.0(az.py)](iot-hub-create-using-cli.md) - 리소스 관리 배포 모델용 차세대 CLI

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 내에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
* [Azure CLI 0.10.4][lnk-CLI-install] 이상 Azure CLI가 이미 설치된 경우 다음 명령을 사용하여 명령 프롬프트에서 현재 버전을 확인할 수 있습니다.

```azurecli
azure --version
```

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Azure Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. Azure CLI는 Azure Resource Manager 모드여야 합니다.
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Azure 계정 및 구독 설정

1. 명령 프롬프트에서 다음 명령을 입력하여 로그인합니다.

   ```azurecli
    azure login
   ```

   제안된 웹 브라우저와 코드를 사용하여 인증합니다.
1. Azure 구독이 여러 개 있는 경우 Azure에 연결하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 Azure 구독을 보고 기본적으로 사용되는 구독을 확인할 수 있습니다.

   ```azurecli
    azure account list
   ```

   나머지 명령을 실행할 구독 컨텍스트를 설정하려면 다음을 사용합니다.

   ```azurecli
    azure account set <subscription name>
   ```

1. 리소스 그룹이 없으면 **exampleResourceGroup**이라는 이름의 리소스 그룹을 만들 수 있습니다.

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> [Azure CLI를 사용하여 Azure 리소스 및 리소스 그룹 관리][lnk-CLI-arm] 문서는 Azure CLI를 사용하여 Azure 리소스를 관리하는 방법에 대해 자세히 설명합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

필수 매개 변수:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**: 리소스 그룹 이름입니다. 형식은 대/소문자를 구분하지 않는 영숫자, 밑줄 및 하이픈으로 구성되며, 길이는 1-64자입니다.
* **이름**: 만들려는 IoT Hub의 이름입니다. 형식은 대/소문자를 구분하지 않는 영숫자 및 하이픈으로 구성되며, 길이는 3-50자입니다.
* **location**: IoT Hub를 프로비전할 위치(Azure 지역/데이터 센터)입니다.
* **sku-name**: sku 이름이며, [F1, S1, S2, S3] 중 하나입니다. 각 sku에 대한 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요. 현재 기본 계층은 포털을 통해서만 사용할 수만 있습니다. 
* **units**: 프로비전된 단위의 수입니다. 단위 제한에 대한 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

만들기에 사용할 수 있는 모든 매개 변수를 보려면 명령 프롬프트에서 help 명령을 사용합니다.

```azurecli
azure iothub create -h
```

간단한 예제: **exampleResourceGroup**이라는 리소스 그룹에 **exampleIoTHubName**이라는 IoT Hub를 만들려면 다음 명령을 실행합니다.

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> 이 Azure CLI 명령은 대금이 청구되는 S1 표준 IoT Hub를 만듭니다. 다음 명령을 사용하여 IoT hub **exampleIoTHubName**을 삭제할 수 있습니다.
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>다음 단계

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure Portal을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
