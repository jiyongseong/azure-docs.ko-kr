---
title: Azure CLI를 사용하여 장치 프로비전 설정 | Microsoft Docs
description: Azure 빠른 시작 - Azure CLI를 사용하여 Azure IoT Hub Device Provisioning Service 설정
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 70248ea2bdd595e6206084ef59822ec0a7ca7d2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Azure CLI를 사용하여 IoT Hub Device Provisioning Service 설정

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작에서는 Azure CLI를 사용하여 IoT 허브 및 IoT Hub Device Provisioning Service를 만들고 두 서비스를 함께 연결하는 방법에 대해 자세히 설명합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!IMPORTANT]
> 이 빠른 시작에서 만드는 IoT 허브 및 프로비전 서비스는 모두 DNS 엔드포인트로 공개적으로 검색할 수 있습니다. 이러한 리소스에 사용되는 이름을 변경하려는 경우 중요한 정보가 공개되지 않도록 합니다.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *westus* 위치에 *my-sample-resource-group*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> 이 예제에서는 리소스 그룹을 미국 서부 위치에 만듭니다. `az account list-locations -o table` 명령을 실행하여 사용할 수 있는 위치의 목록을 볼 수 있습니다.
>
>

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령을 사용하여 IoT 허브를 만듭니다. 

다음 예제에서는 *westus* 위치에 *my-sample-hub*라는 IoT 허브를 만듭니다.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>프로비전 서비스 만들기

[az iot dps create](/cli/azure/iot/dps#az_iot_dps_create) 명령을 사용하여 프로비전 서비스를 만듭니다. 

다음 예제에서는 *westus* 위치에 *my-sample-dps*라는 프로비전 서비스를 만듭니다.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> 이 예제에서는 프로비전 서비스를 미국 서부 지역에 만듭니다. `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 명령을 실행하거나 [Azure 상태](https://azure.microsoft.com/status/) 페이지로 이동하여 "Device Provisioning Service"를 검색함으로써 사용 가능한 위치 목록을 볼 수 있습니다. 명령에서 위치는 단일 또는 다중 단어 형식(예: westus, West US, WEST US 등)으로 지정할 수 있습니다.  값은 대/소문자를 구분하지 않습니다. 다중 단어 형식을 사용하여 위치를 지정하는 경우 값을 따옴표로 묶습니다(예: `-- location "West US"`).
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>IoT 허브에 대한 연결 문자열 가져오기

장치 프로비전 서비스와 연결하려면 IoT 허브의 연결 문자열이 필요합니다. [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) 명령을 사용하여 연결 문자열을 가져오고, 해당 출력을 사용하여 두 리소스를 연결할 때 사용할 변수를 설정합니다. 

다음 예제에서는 *hubConnectionString* 변수를 허브의 *iothubowner* 정책에 있는 기본 키에 대한 연결 문자열 값으로 설정합니다. `--policy-name` 매개 변수를 사용하여 다른 정책을 지정할 수 있습니다. 이 명령은 Azure CLI [query](/cli/azure/query-azure-cli) 및 [output](/cli/azure/format-output-azure-cli#tsv-output-format) 옵션을 사용하여 명령 출력에서 연결 문자열을 추출합니다.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

`echo` 명령을 사용하여 연결 문자열을 확인할 수 있습니다.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> 이러한 두 명령은 Bash에서 실행되는 호스트에 유효합니다. 로컬 Windows/CMD 셸 또는 PowerShell 호스트를 사용하는 경우, 해당 환경에 대해 올바른 구문을 사용하도록 명령을 수정해야 합니다.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>IoT 허브 및 프로비전 서비스 연결

[az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create) 명령을 사용하여 IoT 허브와 프로비전 서비스를 연결합니다. 

다음 예제에서는 *westus* 위치에 있는 *my-sample-hub*라는 IoT 허브와 *my-sample-dps*라는 장치 프로비전 서비스를 연결합니다. 이전 단계의 *hubConnectionString* 변수에 저장된 *my-sample-hub*에 대한 연결 문자열을 사용합니다.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>프로비전 서비스 확인

[az iot dps show](/cli/azure/iot/dps#az_iot_dps_show) 명령을 사용하여 프로비전 서비스에 대한 세부 정보를 가져옵니다.

다음 예제에서는 *my-sample-dps*라는 프로비전 서비스에 대한 세부 정보를 가져옵니다. 연결된 IoT 허브는 *properties.iotHubs* 컬렉션에 표시됩니다.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작 또는 자습서를 사용하여 계속하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 명령을 사용하여 프로비전 서비스, IoT 허브 또는 리소스 그룹 및 모든 관련 리소스를 삭제할 수 있습니다.

프로비전 서비스를 삭제하려면 [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete) 명령을 실행합니다.

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
IoT 허브를 삭제하려면 [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete) 명령을 실행합니다.

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

리소스 그룹 및 모든 관련 리소스를 삭제하려면 [az group delete](/cli/azure/group#az_group_delete) 명령을 실행합니다.

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Hub 및 Device Provisioning Service 인스턴스를 배포한 후 두 리소스를 연결했습니다. 시뮬레이션된 장치를 프로비전하도록 설정하는 방법에 대해 알아보려면 시뮬레이션된 장치 만들기를 위한 빠른 시작을 진행하세요.

> [!div class="nextstepaction"]
> [시뮬레이션된 장치를 만들기 위한 빠른 시작](./quick-create-simulated-device.md)

