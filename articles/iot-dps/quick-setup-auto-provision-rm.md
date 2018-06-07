---
title: Azure Resource Manager 템플릿을 사용하여 Device Provisioning 설정 | Microsoft Docs
description: Azure 빠른 시작 - 템플릿을 사용하여 Azure IoT Hub Device Provisioning 서비스 설정
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
ms.openlocfilehash: 8bb27cca9e976ff8433793ef378cc6a43449d4bb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿으로 IoT Hub Device Provisioning 서비스 설정

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 사용하여 장치를 프로비전하는 데 필요한 Azure 클라우드 리소스를 프로그래밍 방식으로 설정할 수 있습니다. 이 단계에서는 IoT 허브, 새 IoT Hub Device Provisioning 서비스를 만들고 Azure Resource Manager 템플릿을 사용하여 두 서비스를 서로 연결하는 방법을 보여줍니다. 이 빠른 시작에서는 [Azure CLI 2.0](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)을 사용하여 리소스 그룹을 만들고 템플릿을 배포하는 데 필요한 프로그래밍 단계를 수행하지만, [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby 또는 다른 프로그래밍 언어를 사용하여 이러한 단계를 간편하게 수행하고 템플릿을 배포할 수 있습니다. 


## <a name="prerequisites"></a>필수 조건

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 빠른 시작에서는 Azure CLI를 로컬에서 실행해야 합니다. Azure CLI 버전 2.0 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Azure에 로그인하여 리소스 그룹 만들기

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

3. IoT 허브 및 프로비전 서비스와 같은 Azure 클라우드 리소스를 만들 때, 리소스 그룹에 해당 리소스를 생성합니다. 기존 리소스 그룹을 사용하거나 다음 [리소스 그룹을 만드는 명령][lnk-az-resource-command]을 실행합니다.
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 이전 예제에서는 미국 서부 위치에서 리소스 그룹을 만듭니다. `az account list-locations -o table` 명령을 실행하여 사용할 수 있는 위치의 목록을 볼 수 있습니다.
    >
    >

## <a name="create-a-resource-manager-template"></a>Resource Manager 템플릿 만들기

JSON 템플릿을 사용하여 프로비전 서비스 및 연결된 IoT 허브를 리소스 그룹에 만듭니다. Azure Resource Manager 템플릿을 사용하여 기존 프로비전 서비스나 IoT 허브를 변경할 수도 있습니다.

1. 텍스트 편집기를 사용하여 다음 기본 구조 콘텐츠가 포함된 **template.json**이라는 Azure Resource Manager 템플릿을 만듭니다. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. **parameters** 섹션을 다음 콘텐츠로 바꿉니다. 매개 변수 섹션에는 다른 파일에서 전달할 수 있는 매개 변수를 지정합니다. 이 섹션에는 생성할 IoT 허브 및 프로비전 서비스의 이름을 지정합니다. 또한 IoT 허브 및 프로비전 서비스의 위치도 지정합니다. 값은 IoT 허브 및 프로비전 서비스를 지원하는 Azure 영역으로 제한됩니다. Device Provisioning 서비스가 지원되는 위치 목록을 보려면 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 명령을 실행하거나 [Azure 상태](https://azure.microsoft.com/status/) 페이지로 이동하여 "Device Provisioning 서비스"를 검색합니다.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. **variables** 섹션을 다음 콘텐츠로 바꿉니다. 이 섹션에서는 IoT 허브 연결 문자열을 구성하기 위해 나중에 사용되는 값을 정의합니다. 이 값은 프로비전 서비스와 IoT 허브를 연결하는 데 필요합니다. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. IoT 허브를 만들려면 **resources** 컬렉션에 다음 줄을 추가합니다. JSON은 IoT Hub를 만드는 데 필요한 최소 속성을 지정합니다. **name** 및 **location** 속성은 매개 변수로 전달됩니다. 템플릿에서 IoT Hub에 대해 지정할 수 있는 속성에 대해 자세히 알아보려면 [Microsoft.Devices/IotHubs 템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs)를 참조하세요.

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. 프로비전 서비스를 만들려면 **resources** 컬렉션의 IoT 허브 사양 뒤에 다음 줄을 추가합니다. 프로비전 서비스의 **name** 및 **location**은 매개 변수로 전달됩니다. **iotHubs** 컬렉션에 프로비전 서비스에 연결할 IoT 허브를 지정합니다. 최소한, 연결된 IoT 허브마다 **connectionString** 및 **location** 속성을 지정해야 합니다. IoT 허브마다 **allocationWeight** 및 **applyAllocationPolicy**와 같은 속성을 설정할 수 있을 뿐만 아니라 프로비전 서비스 자체에 **allocationPolicy** 및 **authorizationPolicies**와 같은 속성을 설정할 수 있습니다. 자세히 알아보려면 [Microsoft.Devices/provisioningServices 템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices)를 참조하세요.

   **dependsOn** 속성은 Resource Manager가 프로비전 서비스를 만들기 전에 IoT 허브를 만들도록 하는 데 사용됩니다. 템플릿에 허브와 해당 키가 먼저 생성되도록 프로비전 서비스에 대한 링크를 지정하는 IoT 허브의 연결 문자열이 필요합니다. 템플릿은 **concat** 및 **listKeys**와 같은 함수를 사용하여 연결 문자열을 만듭니다. 자세히 알아보려면 [Azure Resource Manager 템플릿 함수](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)를 참조하세요.

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. 템플릿 파일을 저장합니다. 완성된 템플릿은 다음과 같은 모양입니다.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Resource Manager 매개 변수 파일 만들기

마지막 단계에서 정의한 템플릿은 매개 변수를 사용하여 IoT Hub의 이름, 프로비전 서비스의 이름 및 해당 항목이 만들어질 위치(Azure 지역)를 지정합니다. 이러한 매개 변수는 별도의 파일로 전달합니다. 이렇게 하면 여러 배포에 대해 동일한 템플릿을 다시 사용할 수 있습니다. 매개 변수 파일을 만들려면 다음 단계를 수행합니다.

1. 텍스트 편집기를 사용하여 다음 기본 구조 콘텐츠가 포함된 **parameters.json**이라는 Azure Resource Manager 매개 변수 파일을 만듭니다. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. parameter 섹션에 **iotHubName** 값을 추가합니다. 이름을 변경하는 경우 IoT 허브의 적절한 명명 규칙을 따라야 합니다. 길이는 3-50자여야 하며 대문자나 소문자 영숫자 또는 하이픈('-')만 포함할 수 있습니다. 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. parameter 섹션에 **provisioningServiceName** 값을 추가합니다. 이름을 변경하는 경우 IoT Hub Device Provisioning 서비스의 적절한 명명 규칙을 따라야 합니다. 길이는 3-64자여야 하며 대문자나 소문자 영숫자 또는 하이픈('-')만 포함할 수 있습니다.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. parameter 섹션에 **hubLocation** 값을 추가합니다. 이 값은 IoT 허브 및 프로비전 서비스의 위치를 지정합니다. 이 값은 템플릿 파일의 매개 변수 정의에 있는 **allowedValues** 컬렉션에 지정된 위치 중 하나와 일치해야 합니다. 이 컬렉션은 IoT 허브와 프로비전 서비스를 모두 지원하는 Azure 위치로 값을 제한합니다. Device Provisioning 서비스가 지원되는 위치 목록을 보려면 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 명령을 실행하거나 [Azure 상태](https://azure.microsoft.com/status/) 페이지로 이동하여 "Device Provisioning 서비스"를 검색합니다.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. 파일을 저장합니다. 


> [!IMPORTANT]
> IoT 허브와 프로비전 서비스는 모두 DNS 엔드포인트로 공개적으로 검색이 가능하기 때문에 이름을 지정할 때는 중요한 정보를 피해야 합니다.
>

## <a name="deploy-the-template"></a>템플릿 배포

다음 Azure CLI 명령을 사용하여 템플릿을 배포하고 배포를 확인합니다.

1. 템플릿을 배포하려면 다음 [명령을 실행하여 배포를 시작](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az_group_deployment_create)합니다.
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   출력에서 "Succeeded"로 설정된 **provisioningState** 속성을 찾습니다. 

   ![프로비전 출력](./media/quick-setup-auto-provision-rm/output.png) 


2. 배포를 확인하려면 다음 [명령을 실행하여 리소스를 나열하고](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az_resource_list) 출력에서 새 프로비전 서비스 및 IoT 허브를 찾습니다.

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작 또는 자습서를 사용하여 계속하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속할 계획이 아니면 Azure CLI를 사용하여 IoT 허브나 프로비전 서비스와 같은 [개별리소스를 삭제][lnk-az-resource-command]하거나 리소스 그룹과 이 그룹의 모든 리소스를 삭제할 수 있습니다.

프로비전 서비스를 삭제하려면 다음 명령을 실행합니다.

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
IoT Hub를 삭제하려면 다음 명령을 실행합니다.

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

리소스 그룹 및 모든 해당 리소스를 삭제하려면 다음 명령을 실행합니다.

```azurecli
az group delete --name {your resource group name}
```

Azure Portal, PowerShell, REST API 또는 Azure Resource Manager나 IoT Hub Device Provisioning 서비스에 게시된 지원되는 플랫폼 SDK를 사용하여 리소스 그룹 및 개별 리소스를 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Hub 및 Device Provisioning Service 인스턴스를 배포한 후 두 리소스를 연결했습니다. 시뮬레이션된 장치를 프로비전하도록 설정하는 방법에 대해 알아보려면 시뮬레이션된 장치 만들기를 위한 빠른 시작을 진행하세요.

> [!div class="nextstepaction"]
> [시뮬레이션된 장치를 만들기 위한 빠른 시작](./quick-create-simulated-device.md)


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
