---
title: 템플릿을 사용하여 Azure IoT Hub 만들기(PowerShell) | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 PowerShell로 IoT Hub를 만드는 방법입니다.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0e5f95d98f772b226e162f601939bc94bf8fb78b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager 템플릿을 사용하여 IoT Hub 만들기(PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure 리소스 관리자를 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 PowerShell로 IoT Hub를 만드는 방법을 보여 줍니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델, 즉 [Azure Resource Manager 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md) 모델이 있습니다. 이 문서에서는 Azure Resource Manager 배포 모델 사용에 대해 설명합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 내에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
* [Azure PowerShell 1.0][lnk-powershell-install] 이상.

> [!TIP]
> [Azure Resource Manager로 Azure PowerShell 사용][lnk-powershell-arm] 문서에 PowerShell 및 Azure Resource Manager 템플릿을 사용하여 Azure 리소스를 만드는 방법에 대한 자세한 내용이 설명되어 있습니다.

## <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결

PowerShell 명령 프롬프트에서 다음 명령을 입력하여 Azure 구독에 로그인합니다.

```powershell
Connect-AzureRmAccount
```

Azure 구독이 여러 개 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 사용할 수 있는 Azure 구독을 나열합니다.

```powershell
Get-AzureRMSubscription
```

다음 명령을 사용하여 IoT Hub를 만드는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

다음 명령을 사용하여 IoT Hub 및 현재 지원되는 API 버전을 배포할 수 있는 위치를 검색할 수 있습니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

IoT Hub가 지원되는 위치 중 하나에서 다음 명령을 사용하여 IoT Hub를 포함하는 리소스 그룹을 만듭니다. 이 예에서는 **MyIoTRG1**이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>IoT hub를 만들 템플릿 제출

JSON 템플릿을 사용하여 리소스 그룹에 IoT hub를 만듭니다. Azure Resource Manager 템플릿을 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. 텍스트 편집기에서 새로운 표준 IoT Hub를 만드는 다음 리소스 정의를 사용하여 **template.json**이라는 Azure Resource Manager 템플릿을 만듭니다. 이 예에서는 **미국 동부** 지역에 IoT Hub를 추가하고 이벤트 허브와 호환되는 끝점에 두 개의 소비자 그룹(**cg1** 및 **cg2**)을 만들고 **2016-02-03** API 버전을 사용합니다. 또한 이 템플릿에서 매개 변수로 IoT Hub 이름 **hubName**을 전달해야 합니다. IoT Hub를 지원하는 현재 위치 목록에 대해서는 [Azure 상태][lnk-status]를 참조하세요.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. 로컬 컴퓨터에 Azure Resource Manager 템플릿 파일을 저장합니다. 이 예제에서는 **c:\templates** 폴더에 저장하는 것으로 가정합니다.

3. 다음 명령을 실행하여 새 IoT Hub를 배포하고, 매개 변수로 IoT Hub 이름을 전달합니다. 이 예제에서는 IoT Hub 이름이 `abcmyiothub`입니다. IoT Hub의 이름은 전역적으로 고유해야 합니다.

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. 앞에서 만든 IoT Hub의 키가 출력에 표시됩니다.

5. 응용 프로그램이 새 IoT Hub에 추가되었는지 확인하려면 [Azure Portal][lnk-azure-portal]을 방문하여 리소스 목록을 확인합니다. 또는 **Get AzureRmResource** PowerShell cmdlet을 사용합니다.

> [!NOTE]
> 이 예제 응용 프로그램은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료되면 [Azure Portal][lnk-azure-portal] 또는 **Remove-AzureRmResource** PowerShell cmdlet을 사용하여 IoT Hub를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 사용하여 PowerShell에서 IoT Hub를 배포했으므로 구체적인 내용을 알아볼 차례입니다.

* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
* Azure Resource Manager의 기능에 대해 자세히 알아보려면 [Azure Resource Manager 개요][lnk-azure-rm-overview]를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
