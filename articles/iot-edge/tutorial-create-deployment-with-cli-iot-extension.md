---
title: Azure CLI 2.0용 IoT 확장을 사용하여 IoT Edge 장치 배포 | Microsoft Docs
description: Azure CLI 2.0용 IoT 확장을 사용하여 IoT Edge 장치 배포
services: iot-edge
keywords: ''
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: ''
ms.reviewer: kgremban
ms.openlocfilehash: 7bc0d0706385f2f3e101d06be3a2837341c331b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Azure CLI 2.0용 IoT 확장을 사용하여 IoT Edge 장치 배포

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)은 IoT Edge 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. Azure CLI 2.0은 Windows, Linux 및 MacOS에서 사용할 수 있습니다.

Azure CLI 2.0을 사용하면 Azure IoT Hub 리소스, 장치 프로비전 서비스 인스턴스 및 연결된 허브를 즉시 관리할 수 있습니다. 새로운 IoT 확장은 장치 관리 및 전체 IoT Edge 같은 기능으로 Azure CLI 2.0을 강화합니다.

이 문서에서는 Azure CLI 2.0 및 IoT 확장을 설정합니다. 그런 후 사용 가능한 CLI 명령을 사용하여 IoT Edge 장치에 모듈을 배포하는 방법을 배웁니다.

## <a name="prerequisites"></a>필수 조건

* Azure 계정. 아직 없는 경우 [무료 계정을 만들](https://azure.microsoft.com/free/?v=17.39a) 수 있습니다. 

* [Python 2.7x 또는 Python 3.x](https://www.python.org/downloads/).

* 사용자 환경의 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Azure CLI 2.0 버전이 2.0.24 이상이어야 합니다. `az –-version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다. Windows에 설치하는 간단한 방법 중 하나는 [MSI](https://aka.ms/InstallAzureCliWindows)를 다운로드하여 설치하는 것입니다.

* [Azure CLI 2.0에 대한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension):
   1. `az extension add --name azure-cli-iot-ext`을 실행합니다. 
   2. 설치 후 `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사하거나 `az extension show --name azure-cli-iot-ext` 명령을 사용하여 IoT 확장에 대한 세부 정보를 확인합니다.
   3. 확장을 제거하려면 `az extension remove --name azure-cli-iot-ext` 명령을 사용합니다.


## <a name="create-an-iot-edge-device"></a>IoT Edge 장치 만들기
이 문서에서는 IoT Edge 배포를 만드는 지침을 제공합니다. 이 예제에서는 Azure 계정에 로그인하고, Azure 리소스 그룹(Azure 솔루션에 대한 관련 리소스를 보관하는 컨테이너)을 만들고, IoT Hub를 만들고, 세 개의 IoT Edge 장치 ID를 만들고, 태그를 설정하고, 이러한 장치를 대상으로 하는 IoT Edge 배포를 만드는 방법을 보여줍니다. 

Azure 계정에 로그인 다음 login 명령을 입력하면 웹 브라우저에서 1회용 코드를 사용하여 로그인하라는 메시지가 표시됩니다. 

   ```cli
   az login
   ```

미국 동부 지역에 **IoTHubCLI**라는 새 리소스 그룹을 만듭니다. 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![리소스 그룹 만들기][2]

새로 만든 리소스 그룹에 **CLIDemoHub**라는 IoT Hub를 만듭니다.

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >각 구독에는 무료 IoT 허브가 하나 할당됩니다. CLI 명령으로 무료 허브를 만들려면 SKU 값을 `--sku F1`로 바꿉니다. 구독에 무료 허브가 이미 있는 경우 두 번째 구독을 만들려고 하면 오류 메시지가 표시됩니다. 

IoT Edge 장치 만들기:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![IoT Edge 장치 만들기][4]

## <a name="configure-the-iot-edge-device"></a>IoT Edge 장치 구성

배포 JSON 템플릿을 만들고 로컬에 txt 파일로 저장합니다. apply-configuration 명령을 실행할 때 파일 경로가 필요합니다.

배포 JSON 템플릿은 edgeAgent 및 edgeHub라는 두 가지 시스템 모듈을 항상 포함해야 합니다. 두 가지 모듈 외에도 이 파일을 사용하여 IoT Edge 장치에 추가 모듈을 배포할 수 있습니다. 다음 샘플을 사용하여 tempSensor 모듈이 하나 포함된 IoT Edge 장치를 구성합니다.

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": ""
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {},
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

IoT Edge 장치에 구성 적용:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

IoT Edge 장치의 모듈보기:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![모듈 목록 표시][6]

## <a name="next-steps"></a>다음 단계

* [IoT Edge 장치를 게이트웨이로 사용](how-to-create-transparent-gateway.md)하는 방법 알아보기

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

