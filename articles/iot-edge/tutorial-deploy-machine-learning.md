---
title: Azure IoT Edge를 사용하여 Azure Machine Learning 배포 | Microsoft Docs
description: Azure Machine Learning을 Edge 장치에 모듈로 배포
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6062d8193ce8cf7edaff3187f5c0f7dd9968658b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165743"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning을 IoT Edge 모듈로 배포 - 미리 보기

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 [Windows][lnk-tutorial1-win] 또는 [Linux][lnk-tutorial1-lin]에서 시뮬레이트된 장치에 Azure IoT Edge 배포 자습서에서 만든 시뮬레이트된 IoT Edge 장치에서 센서 데이터를 기준으로 장치에서 오류가 발생할 때를 예측하는 Azure Machine Learning 모듈을 배포하는 과정을 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning 모듈 만들기
> * Azure 컨테이너 레지스트리에 모듈 컨테이너 푸시
> * Azure Machine Learning 모듈을 IoT Edge 장치에 배포
> * 생성된 데이터 보기

이 자습서에서 만드는 Azure Machine Learning 모듈은 장치에서 생성된 환경 데이터를 읽고, 메시지에 비정상 레이블을 지정하기도 합니다.

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 또는 첫 번째 자습서에서 만든 Azure IoT Edge 장치
* IoT Edge 장치를 연결할 IoT Hub용 IoT Hub 연결 문자열
* Azure Machine Learning 계정입니다. 계정을 만들려면 [Azure Machine Learning 미리 보기 계정 만들기 및 Azure Machine Learning Workbench 설치](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts)의 지침을 따릅니다. 이 자습서에 사용하기 위해 워크벤치 응용 프로그램을 설치할 필요는 없습니다. 
* 컴퓨터에 있는 Azure ML용 모듈 관리입니다. 환경을 설정하고 계정을 만들려면 [모델 관리 설정](../machine-learning/desktop-workbench/deployment-setup-configuration.md)의 지침을 따릅니다.

Azure Machine Learning 모듈은 ARM 프로세서를 지원하지 않습니다.

## <a name="create-the-azure-ml-container"></a>Azure ML 컨테이너 만들기
이 섹션에서는 학습된 모델 파일을 다운로드하고 이를 Azure ML 컨테이너로 변환합니다.

Azure ML용 모듈 관리를 실행하는 컴퓨터에서, GitHub에 있는 Azure ML IoT Toolkit의 [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) 및 [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)을 다운로드하고 저장합니다. 이 파일은 Iot Edge 장치에 배포할 학습된 기계 학습 모델을 정의합니다.

학습된 모델을 사용하여 IoT Edge 장치에 배포할 수 있는 컨테이너를 만듭니다. 다음과 같은 작업에 다음 명령을 사용합니다.

   * 장치를 등록합니다.
   * 매니페스트를 만듭니다.
   * *machinelearningmodule*이라는 Docker 컨테이너 이미지를 만듭니다.
   * AKS(Azure Kubernetes Service) 클러스터에 이미지를 배포합니다.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>컨테이너 리포지토리 보기

컨테이너 이미지가 성공적으로 만들어지고 기계 학습 환경과 연결된 Azure 컨테이너 리포지토리에 저장되었는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**로 이동하여 **컨테이너 레지스트리**를 선택합니다.
2. 레지스트리를 선택합니다. 이름은 **mlcr**로 시작되어야 하며 모듈 관리를 설정하는 데 사용한 리소스 그룹, 위치 및 구독에 속합니다.
3. **액세스 키**를 선택합니다.
4. **로그인 서버**, **사용자 이름** 및 **암호**를 복사합니다.  Edge 장치에서 레지스트리에 액세스하려면 이러한 항목이 필요합니다.
5. **리포지토리** 선택
6. **machinelearningmodule** 선택
7. 이제 컨테이너의 전체 이미지 경로가 있습니다. 다음 섹션에서 사용하기 위해 이 이미지 경로를 적어둡니다. 다음과 같이 표시됩니다. **<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Edge 장치에 레지스트리 자격 증명 추가

Edge 장치를 실행 중인 컴퓨터의 Edge 런타임에 레지스트리의 자격 증명을 추가합니다. 이 명령은 컨테이너를 풀하기 위한 런타임 액세스 권한을 제공합니다.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

## <a name="run-the-solution"></a>솔루션 실행

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. **IoT Edge(미리 보기)** 로 이동하여 IoT Edge 장치를 선택합니다.
1. **모듈 설정**을 선택합니다.
1. 이전에 IoT Edge 장치에 tempSensor 모듈을 배포한 경우 자동으로 입력될 수 있습니다. 모듈 목록에 없는 경우 모듈을 추가합니다.
    1. **IoT Edge 모듈 추가**를 선택합니다.
    2. **이름** 필드에 `tempSensor`를 입력합니다.
    3. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다.
    4. **저장**을 선택합니다.
1. 만든 기계 학습 모듈을 추가합니다.
    1. **IoT Edge 모듈 추가**를 선택합니다.
    1. **이름** 필드에 `machinelearningmodule`을 입력합니다.
    1. **이미지** 필드에 이미지 주소(예: `<registry_name>.azurecr.io/machinelearningmodule:1`)를 입력합니다.
    1. **저장**을 선택합니다.
1. **모듈 추가** 단계로 돌아가서 **다음**을 선택합니다.
1. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 첫 번째 경로는 모든 Azure Machine Learning 모듈이 사용하는 끝점인 "amlInput" 끝점을 통해 온도 센서에서 Machine Learning 모듈로 메시지를 전송합니다. 두 번째 경로는 Machine Learning 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 “amlOutput”은 모든 Azure Machine Learning 모듈이 데이터를 출력하는 데 사용하는 끝점이고 ‘$upstream’은 IoT Hub를 나타냅니다.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. **다음**을 선택합니다.
1. **템플릿 검토** 단계에서 **제출**을 선택합니다.
1. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다.  **tempSensor** 모듈 및 IoT Edge 런타임 모듈과 함께 실행되는 새로운 **machinelearningmodule**이 표시됩니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

Visual Studio Code에 대해 [IoT Hub 탐색기](https://github.com/azure/iothub-explorer) 또는 Azure IoT Toolkit 확장을 사용하여 IoT Edge 장치에서 보내는 장치-클라우드 메시지를 볼 수 있습니다.

1. Visual Studio Code에서 **IoT Hub 장치**를 선택합니다.
2. **...** 를 선택하고 나서 메뉴에서 **IoT Hub 연결 문자열 설정**을 선택합니다.

   ![IoT Hub 장치 추가 메뉴](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 페이지의 맨 위에 열리는 텍스트 상자에 IoT Hub의 iothubowner 연결 문자열을 입력합니다. IoT Edge 장치가 IoT Hub 장치 목록에 표시됩니다.
4. **...** 를 다시 선택한 후 **D2C 메시지 모니터링 시작**을 선택합니다.
5. 5초마다 tempSensor에서 보낸 메시지를 관찰합니다. 메시지 본문에 machinelearningmodule이 true 또는 false 값을 제공하는 **anomaly**라는 속성이 포함되어 있습니다. **AzureMLResponse** 속성에는 모델이 성공적으로 실행된 경우 값 "OK"가 포함됩니다.

   ![메시지 본문의 Azure ML 응답](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning에서 제공하는 IoT Edge 모듈을 배포했습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다른 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 함수를 모듈로 배포](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
