---
title: 원격 모니터링 솔루션에서 장치 시뮬레이션 - Azure | Microsoft Docs
description: 이 자습서는 원격 모니터링 솔루션 가속기에서 장치 시뮬레이터를 사용하는 방법을 보여줍니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c10d983ea6b864d21f4589a3cbfdd5def39ac753
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="create-a-new-simulated-device"></a>시뮬레이션된 새 장치 만들기

이 자습서는 원격 모니터링 솔루션 가속기에서 장치 시뮬레이터 마이크로 서비스를 사용자 지정하는 방법을 보여 줍니다. 장치 시뮬레이터의 기능을 표시하기 위해 이 자습서에서는 Contoso IoT 응용 프로그램에서 두 가지 시나리오를 사용합니다.

다음 비디오는 장치 시뮬레이터 마이크로 서비스를 사용자 지정하기 위한 옵션의 개요를 제공합니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

첫 번째 시나리오에서 Contoso는 새 스마트 전구 장치를 테스트하려고 합니다. 테스트를 수행하려면 다음 특징을 가진 시뮬레이트된 새 장치를 만듭니다.

*속성*

| Name                     | 값                      |
| ------------------------ | --------------------------- |
| 색                    | 흰색, 빨강, 파랑            |
| 밝기               | 0~100                    |
| 남은 예상 수명 | 10,000시간에서 카운트다운 |

*원격 분석*

다음 표는 전구에서 데이터 스트림으로 클라우드에 보고하는 데이터를 보여 줍니다.

| Name   | 값      |
| ------ | ----------- |
| 상태 | "켜짐", "꺼짐" |
| 온도 | 화씨 도 |
| 온라인 | true, false |

> [!NOTE]
> **온라인** 원격 분석 값은 모든 시뮬레이트된 형식에 필수입니다.

*메서드*

다음 표는 새 장치에서 지원하는 작업을 보여 줍니다.

| Name        |
| ----------- |
| 켜짐   |
| 꺼짐  |

*초기 상태*

다음 표는 장치의 초기 상태를 보여 줍니다.

| Name                     | 값 |
| ------------------------ | -------|
| 초기 색상            | 흰색  |
| 초기 밝기       | 75     |
| 초기 남은 수명   | 10000 |
| 초기 원격 분석 상태 | "켜짐"   |
| 초기 원격 분석 온도 | 200   |

두 번째 시나리오에서 Contoso의 기존 **냉각기** 장치에 새 원격 분석 유형을 추가합니다.

이 자습서는 원격 모니터링 솔루션 가속기에서 장치 시뮬레이터를 사용하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 새 장치 유형 만들기
> * 사용자 지정 장치 동작 시뮬레이트
> * 대시보드에 새 장치 유형 추가
> * 기존의 장치 유형에서 사용자 지정 원격 분석 보내기

다음 비디오에서는 원격 모니터링 솔루션에 시뮬레이션 및 실제 장치를 연결하는 연습을 보여 줍니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음이 필요합니다.

* Azure 구독에서 원격 모니터링 솔루션의 배포된 인스턴스. 원격 모니터링 솔루션을 아직 배포하지 않은 경우 [원격 모니터링 솔루션 가속기 배포](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) 자습서를 완료해야 합니다.

* Visual Studio 2017. Visual Studio 2017이 설치되지 않은 경우 체험판 [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) Edition을 다운로드할 수 있습니다.

* [Visual Studio 2017용 클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) Visual Studio 확장.

* [Docker 허브](https://hub.docker.com/) 계정. 체험하기로 시작할 수 있습니다.

* 데스크톱 컴퓨터에 설치된 [Git](https://git-scm.com/downloads).

## <a name="prepare-your-development-environment"></a>개발 환경 준비

다음 작업을 완료하여 원격 모니터링 솔루션에 새 시뮬레이션된 장치를 추가하기 위한 개발 환경을 준비합니다.

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Azure에서 솔루션 가상 머신에 대한 SSH 액세스 구성

[www.azureiotsuite.com](https://www.azureiotsuite.com)에서 원격 모니터링 솔루션을 만들 때 솔루션 이름을 선택합니다. 솔루션 이름은 솔루션을 사용하는 다양한 배포된 리소스를 포함하는 Azure 리소스 그룹의 이름이 됩니다. 다음 명령은 **Contoso-01**이라는 리소스 그룹을 사용하므로, **Contoso-01**을 사용자의 리소스 그룹 이름으로 대체해야 합니다.

다음 명령은 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)의 `az` 명령을 사용합니다. 개발 컴퓨터에 Azure CLI 2.0을 설치하거나 [Azure Portal](http://portal.azure.com)에서 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 사용할 수 있습니다. Azure CLI 2.0은 Cloud Shell에 사전 설치됩니다.

1. 원격 모니터링 리소스를 포함하는 리소스 그룹의 이름이 유효한지 확인하려면 다음 명령을 실행합니다.

    ```sh
    az group list | grep "name"
    ```

    이 명령은 구독의 리소스 그룹을 모두 나열합니다. 목록에는 원격 모니터링 솔루션과 같은 이름의 리소스 그룹이 포함되어야 합니다.

1. 리소스 그룹을 이후 명령에 대한 기본 그룹으로 만들려면 **Contoso-01** 대신 사용자의 리소스 그룹 이름을 사용하는 다음 명령을 실행합니다.

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. 리소스 그룹에서 리소스를 나열하려면 다음 명령을 실행합니다.

    ```sh
    az resource list -o table
    ```

    가상 머신 및 네트워크 보안 그룹의 이름을 기록해 둡니다. 이러한 값은 이후 단계에서 사용합니다.

1. 사용자의 가상 머신에서 SSH 액세스를 사용하도록 설정하려면 이전 단계에서 사용자의 네트워크 보안 그룹의 이름을 사용하는 다음 명령을 실행합니다.

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    네트워크에 대한 인바운드 규칙의 목록을 보려면 다음 명령을 실행합니다.

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. 가상 머신 암호를 알고 있는 암호로 변경하려면 다음 명령을 실행합니다. 이전에 적어 두었던 가상 머신의 이름 및 선택한 암호를 사용합니다.

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. 가상 머신의 IP 주소를 확인하려면 다음 명령을 사용하고 공용 IP 주소를 기록해 둡니다.

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. 이제 SSH를 사용하여 가상 머신에 연결할 수 있습니다. `ssh` 명령은 Cloud Shell에 사전 설치됩니다. 이전 단계의 공용 IP 주소를 사용하고 메시지가 표시되면 가상 머신에 구성된 암호를 사용합니다.

    ```sh
    ssh azureuser@public-ip-address
    ```

    이제 원격 모니터링 솔루션의 Docker 컨테이너를 실행하는 가상 머신에서 셸에 액세스할 수 있습니다. 실행 중인 컨테이너를 보려면 다음 명령을 사용합니다.

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>서비스 연결 문자열 찾기

자습서에서는 솔루션의 Cosmos DB 및 IoT Hub 서비스에 연결하는 Visual Studio 솔루션으로 작업합니다. 다음 단계는 필요한 연결 문자열 값을 찾는 방법을 보여 줍니다.

1. Cosmos DB 연결 문자열을 찾으려면 가상 머신에 연결된 SSH 세션에서 다음 명령을 실행합니다.

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    연결 문자열을 기록해 둡니다. 이 값은 자습서 뒷부분에서 사용합니다.

1. IoT Hub 연결 문자열을 찾으려면 가상 머신에 연결된 SSH 세션에서 다음 명령을 실행합니다.

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    연결 문자열을 기록해 둡니다. 이 값은 자습서 뒷부분에서 사용합니다.

> [!NOTE]
> 이러한 연결 문자열은 Azure Portal에서 찾거나 `az` 명령을 사용하여 찾을 수 있습니다.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>가상 머신의 장치 시뮬레이션 서비스 중지

장치 시뮬레이션 서비스를 수정하면 변경 내용을 테스트하도록 로컬로 실행할 수 있습니다. 장치 시뮬레이션 서비스를 로컬로 실행하기 전에 다음과 같이 가상 머신에서 실행 중인 인스턴스를 중지해야 합니다.

1. **device-simulation** 서비스의 **CONTAINER ID**를 찾으려면 가상 머신에 연결된 SSH 세션에서 다음 명령을 실행합니다.

    ```sh
    docker ps
    ```

    **device-simulation** 서비스의 컨테이너 ID를 적어 둡니다.

1. **device-simulation** 컨테이너를 중지하려면 다음 명령을 실행합니다.

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>GitHub 리포지토리 복제

이 자습서에서는 **device-simulation** 및 **storage-adapter** Visual Studio 프로젝트로 작업합니다. GitHub에서 소스 코드 리포지토리를 복제할 수 있습니다. Visual Studio가 설치되어 있는 로컬 개발 컴퓨터에서 이 단계를 수행합니다.

1. 명령 프롬프트를 열고 **device-simulation** 및 **storage-adapter** GitHub 리포지토리의 사본을 저장하려는 폴더로 이동합니다.

1. **device-simulation** 리포지토리의 .NET 버전을 복제하려면 다음 명령을 실행합니다.

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    원격 모니터링 솔루션의 장치 시뮬레이션 서비스를 사용하면 시뮬레이트된 기본 제공 장치 유형을 변경하고 시뮬레이트된 새 장치 유형을 만들 수 있습니다. 물리적 장치를 연결하기 전에 사용자 지정 장치 유형을 사용하여 원격 모니터링 솔루션의 동작을 테스트할 수 있습니다.

1. **storage-adapter** 리포지토리의 .NET 버전을 복제하려면 다음 명령을 실행합니다.

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    장치 시뮬레이션 서비스는 저장소 어댑터 서비스를 사용하여 Azure에서 Cosmos DB 서비스에 연결합니다. 원격 모니터링 솔루션은 Cosmos DB 데이터베이스에 시뮬레이션된 장치 구성 데이터를 저장합니다.

### <a name="run-the-storage-adapter-service-locally"></a>저장소 어댑터 서비스를 로컬로 실행

장치 시뮬레이션 서비스는 저장소 어댑터 서비스를 사용하여 솔루션의 Cosmos DB 데이터베이스에 연결합니다. 장치 시뮬레이션 서비스를 로컬로 실행하는 경우 저장소 어댑터 서비스도 로컬로 실행해야 합니다. 다음 단계는 Visual Studio에서 저장소 어댑터 서비스를 실행하는 방법을 보여 줍니다.

1. Visual Studio에서 **pcs-storage-adapter.sln** 솔루션 파일을 **storage-adapter** 리포지토리의 로컬 복제본에서 엽니다.

1. 솔루션 탐색기에서 **WebService** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택한 다음, **디버그**를 선택합니다.

1. **환경 변수** 섹션에서 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 변수를 이전에 적어 두었던 Cosmos DB 연결 문자열로 편집합니다. 그런 다음, 변경 사항을 저장합니다.

1. 솔루션 탐색기에서 **WebService** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음, **새 인스턴스 시작**을 선택합니다.

1. 서비스가 로컬로 시작되고 기본 브라우저에서 `http://localhost:9022/v1/status`가 열립니다. **상태** 값이 “확인: 활성 및 양호”인지 확인합니다.

1. 이 자습서를 완료할 때까지 저장소 어댑터 서비스가 로컬로 실행되도록 합니다.

이제 모든 항목이 준비되었으며 원격 모니터링 솔루션에 새 시뮬레이션된 장치 유형 추가를 시작할 수 있습니다.

## <a name="create-a-simulated-device-type"></a>시뮬레이트된 장치 유형 만들기

장치 시뮬레이션 서비스에서 새 장치 유형을 만드는 가장 쉬운 방법은 기존 유형을 복사하고 수정하는 것입니다. 다음 단계에서는 기본 제공 **냉각기** 장치를 복사하여 새 **전구** 장치를 만드는 방법을 보여 줍니다.

1. Visual Studio에서 **device-simulation.sln** 솔루션 파일을 **device-simulation** 리포지토리의 로컬 복제본에서 엽니다.

1. 솔루션 탐색기에서 **SimulationAgent** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택한 다음, **디버그**를 선택합니다.

1. **환경 변수** 섹션에서 **PCS\_IOTHUB\_CONNSTRING** 변수를 이전에 적어 두었던 IoT Hub 연결 문자열로 편집합니다. 그런 다음, 변경 사항을 저장합니다.

1. 솔루션 탐색기에서 **WebService** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택한 다음, **디버그**를 선택합니다.

1. **환경 변수** 섹션에서 **PCS\_IOTHUB\_CONNSTRING** 변수를 이전에 적어 두었던 IoT Hub 연결 문자열로 편집합니다. 그런 다음, 변경 사항을 저장합니다.

1. 솔루션 탐색기에서 **device-simulation** 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다. **한 개의 시작 프로젝트**를 선택하고 **WebService**를 선택합니다. 그런 후 **OK**를 클릭합니다.

1. 각 장치 유형에는 **Services/data/devicemodels** 폴더에 JSON 모델 파일 및 관련된 스크립트가 있습니다. 솔루션 탐색기에서 **냉각기** 파일을 복사하여 다음 표에 나와 있는 것처럼 **전구** 파일을 만듭니다.

    | 원본                      | 대상                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>새 장치 유형의 특성 정의

**lightbulb-01.json** 파일은 생성하는 원격 분석 및 지원하는 메서드와 같은 유형의 특성을 정의합니다. 다음 단계는 **lightbulb-01.json** 파일을 업데이트하여 **Lightbulb** 장치를 정의합니다.

1. **lightbulb-01.json** 파일에서 다음 코드 조각과 같이 장치 메타데이터를 업데이트합니다.

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. **lightbulb-01.json** 파일에서 다음 코드 조각과 같이 시뮬레이션 정의를 업데이트합니다.

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Interval": "00:00:20",
      "Scripts": [
        {
          "Type": "javascript",
          "Path": "lightbulb-01-state.js"
        }
      ]
    },
    ```

1. **lightbulb-01.json** 파일에서 다음 코드 조각과 같이 장치 유형 속성을 업데이트합니다.

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. **lightbulb-01.json** 파일에서 다음 코드 조각과 같이 장치 유형 원격 분석을 업데이트합니다.

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. **lightbulb-01.json** 파일에서 다음 코드 조각과 같이 장치 유형 메서드를 업데이트합니다.

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. **lightbulb-01.json** 파일을 저장합니다.

### <a name="simulate-custom-device-behavior"></a>사용자 지정 장치 동작 시뮬레이트

**scripts/lightbulb-01-state.js** 파일은 **Lightbulb** 유형의 시뮬레이션 동작을 정의합니다. 다음 단계는 **scripts/lightbulb-01-state.js** 파일을 업데이트하여 **Lightbulb** 장치의 동작을 정의합니다.

1. 다음 코드 조각과 같이 **scripts/lightbulb-01-state.js** 파일에서 상태 정의를 편집합니다.

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. 다음 정의를 사용하여 **vary** 함수 뒤에 **flip** 함수를 추가합니다.

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. 다음 코드 조각과 같이 **main** 함수를 편집하여 동작을 구현합니다.

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. **scripts/lightbulb-01-state.js** 파일을 저장합니다.

**scripts/SwitchOn-method.js** 파일은 **Lightbulb** 장치에서 **Switch On** 메서드를 구현합니다. 다음 단계는 **scripts/SwitchOn-method.js** 파일을 업데이트합니다.

1. 다음 코드 조각과 같이 **scripts/SwitchOn-method.js** 파일에서 상태 정의를 편집합니다.

    ```js
    var state = {
       status: "on"
    };
    ```

1. 전구를 켜려면 다음과 같이 **main** 함수를 편집합니다.

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. **scripts/SwitchOn-method.js** 파일을 저장합니다.

1. **scripts/SwitchOff-method.js**라는 **scripts/SwitchOn-method.js** 파일의 복사본을 만듭니다.

1. 전구를 끄려면 다음과 같이 **scripts/SwitchOff-method.js** 파일의 **main** 함수를 편집합니다.

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. **scripts/SwitchOff-method.js** 파일을 저장합니다.

1. 솔루션 탐색기에서 4개의 새 파일을 각각 차례로 선택합니다. 각 파일의 **속성** 창에서**출력 디렉터리로 복사**가 **변경된 내용만 복사**로 설정되었는지 확인합니다.

### <a name="configure-the-device-simulation-service"></a>장치 시뮬레이션 서비스 구성

테스트 중 솔루션에 연결하는 시뮬레이션된 장치 수를 제한하려면 단일 냉각기와 단일 전구 장치를 실행하도록 서비스를 구성합니다. 구성 데이터는 솔루션의 리소스 그룹에서 Cosmos DB 인스턴스에 저장됩니다. 구성 데이터를 편집하려면 Visual Studio에서 **클라우드 탐색기** 보기를 사용합니다.

1. Visual Studio에서 **클라우드 탐색기** 보기를 열려면 **보기**, **클라우드 탐색기**를 차례로 선택합니다.

1. 시뮬레이션 구성 문서에서 찾으려면 **리소스 검색**에서 **simualtions.1**을 입력합니다.

1. 편집하기 위해 열려면 **simulations.1** 문서를 두 번 클릭합니다.

1. **Data**에 대한 값에서 다음 코드 조각과 같은 **DeviceModels** 배열을 찾습니다.

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. 단일 냉각기와 단일 전구 시뮬레이션된 장치를 정의하려면 **DeviceModels** 배열을 다음 코드로 바꿉니다.

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    **simulations.1** 문서에 변경 내용을 저장합니다.

> [!NOTE]
> 또한 Azure Portal에서 Cosmos DB 데이터 탐색기를 사용하여 **simulations.1** 문서를 편집할 수 있습니다.

### <a name="test-the-lightbulb-device-type-locally"></a>전구 장치 유형 로컬로 테스트

이제 장치 시뮬레이션 프로젝트를 로컬로 실행하여 새 시뮬레이션된 전구 형식을 테스트할 준비가 되었습니다.

1. 솔루션 탐색기에서 **WebService**를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음, **새 인스턴스 시작**을 선택합니다.

1. 두 개의 시뮬레이션된 장치가 사용자의 IoT Hub에 연결되어 있는지 확인하려면 브라우저에서 Azure Portal을 엽니다.

1. 원격 모니터링 솔루션을 포함하는 리소스 그룹에서 IoT Hub로 이동합니다.

1. **모니터링** 섹션에서 **메트릭**을 선택합니다. 그런 다음, **연결된 장치**의 수가 2인지 확인합니다.

    ![연결된 장치 수](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. 브라우저에서 원격 모니터링 솔루션에 대한 **대시보드**로 이동합니다. 원격 분석 패널의 **대시보드**에서 **온도**를 선택합니다. 모든 시뮬레이션된 장치에 대한 온도가 차트에 표시됩니다.

    ![온도 원격 분석](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

이제 전구 장치 시뮬레이션이 로컬로 실행됩니다. 다음 단계는 Azure에서 원격 모니터링 마이크로 서비스를 실행하는 가상 머신에 업데이트된 시뮬레이터 코드를 배포하는 것입니다.

계속하기 전에 Visual Studio에서 장치 시뮬레이션 및 저장소 어댑터 프로젝트의 디버깅을 모두 중지할 수 있습니다.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>업데이트된 시뮬레이터를 클라우드에 배포

원격 모니터링 솔루션의 마이크로 서비스는 Docker 컨테이너에서 실행됩니다. 컨테이너는 Azure에서 솔루션의 가상 머신에 호스팅됩니다. 이 섹션에서는 다음을 수행합니다.

* 새 장치 시뮬레이션 Docker 이미지를 만듭니다.
* Docker 허브 리포지토리에 이미지를 업로드합니다.
* 솔루션의 가상 머신으로 이미지를 가져옵니다.

다음 단계에서는 Docker 허브 계정에 **lightbulb**라는 리포지토리가 있다고 가정합니다.

1. Visual Studio의 **device-simulation** 프로젝트에서 **solution\scripts\docker\build.cmd** 파일을 엽니다.

1. **DOCKER_IMAGE** 환경 변수를 설정하는 줄을 사용자의 Docker 허브 리포지토리 이름으로 편집합니다.

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    변경 내용을 저장합니다.

1. Visual Studio의 **device-simulation** 프로젝트에서 **solution\scripts\docker\publish.cmd** 파일을 엽니다.

1. **DOCKER_IMAGE** 환경 변수를 설정하는 줄을 사용자의 Docker 허브 리포지토리 이름으로 편집합니다.

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    변경 내용을 저장합니다.

1. 관리자 권한으로 명령 프롬프트를 엽니다. 그런 다음, **device-simulation** GitHub 리포지토리의 복제본에 있는 **scripts\docker** 폴더로 이동합니다.

1. Docker 이미지를 빌드하려면 다음 명령을 실행합니다.

    ```cmd
    build.cmd
    ```

1. Docker 허브 계정에 로그인하려면 다음 명령을 실행합니다.

    ```cmd
    docker login
    ```

1. Docker 허브 계정에 새 이미지를 업로드하려면 다음 명령을 실행합니다.

    ```cmd
    publish.cmd
    ```

1. 업로드를 확인하려면 [https://hub.docker.com/](https://hub.docker.com/)으로 이동합니다. **lightbulb** 리포지토리를 찾아 **세부 정보**를 선택합니다. 그런 다음, **태그**를 선택합니다.

    ![Docker 허브](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    스크립트는 **테스팅** 태그를 이미지에 추가합니다.

1. SSH를 사용하여 Azure에서 솔루션의 가상 머신에 연결합니다. 그런 다음, **앱** 폴더로 이동하여 **docker-compose.yaml** 파일을 편집합니다.

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Docker 이미지를 사용하도록 장치 시뮬레이션 서비스에 대한 항목을 편집합니다.

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    변경 내용을 저장합니다.

1. 새 설정을 사용하여 모든 서비스를 다시 시작하려면 다음 명령을 실행합니다.

    ```sh
    sudo ./start.sh
    ```

1. 새 장치 시뮬레이션 컨테이너에서 로그 파일을 확인하려면 다음 명령을 실행하여 컨테이너 ID를 찾습니다.

    ```sh
    docker ps
    ```

    그런 다음, 컨테이너 ID를 사용하여 다음 명령을 실행합니다.

    ```sh
    docker logs {container ID}
    ```

이제 원격 모니터링 솔루션에 장치 시뮬레이션 서비스의 업데이트된 버전을 배포하는 단계를 완료했습니다.

브라우저에서 원격 모니터링 솔루션에 대한 **대시보드**로 이동합니다. 원격 분석 패널의 **대시보드**에서 **온도**를 선택합니다. 두 개의 시뮬레이션된 장치에 대한 온도가 차트에 표시됩니다.

![온도 원격 분석](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

**장치** 페이지에서 새 유형의 인스턴스를 프로비전할 수 있습니다.

![사용 가능한 시뮬레이션의 목록 보기](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

시뮬레이트된 장치에서 원격 분석을 볼 수 있습니다.

![전구 원격 분석 보기](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

장치에서 **SwitchOn** 및 **SwitchOff** 메서드를 호출할 수 있습니다.

![전구 메서드 호출](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>새 원격 분석 유형 추가

이 섹션에서는 시뮬레이트된 기존 장치 유형을 수정하여 새 원격 분석 유형을 지원하는 방법을 설명합니다.

### <a name="locate-the-chiller-device-type-files"></a>냉각기 장치 유형 파일 찾기

다음 단계에서는 기본 제공 **냉각기** 장치를 정의하는 파일을 찾는 방법을 보여 줍니다.

1. 아직 그렇게 하지 않은 경우 다음 명령을 사용하여 로컬 컴퓨터에 **장치-시뮬레이션** GitHub 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 각 장치 유형에는 `data/devicemodels` 폴더에 JSON 모델 파일 및 관련된 스크립트가 있습니다. 시뮬레이트된 **냉각기** 장치 유형을 정의하는 파일은 다음과 같습니다.

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>새 원격 분석 유형 지정

다음 단계에서는 **냉각기** 장치 유형에 새 **내부 온도** 유형을 추가하는 방법을 보여 줍니다.

1. **chiller-01.json** 파일을 엽니다.

1. 다음과 같이 **SchemaVersion** 값을 업데이트합니다.

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. **InitialState** 섹션에서 다음 두 정의를 추가합니다.

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. **Telemetry** 배열에서 다음 정의를 추가합니다.

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. **chiller-01.json** 파일을 저장합니다.

1. **scripts/chiller-01-state.js** 파일을 엽니다.

1. **state** 변수에 다음 필드를 추가합니다.

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. **main** 함수에 다음 줄을 추가합니다.

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. **scripts/chiller-01-state.js** 파일을 저장합니다.

### <a name="test-the-chiller-device-type"></a>냉각기 장치 유형 테스트

업데이트된 **냉각기** 장치 유형을 테스트하기 위해 먼저 **device-simulation** 서비스의 로컬 복사본을 실행하여 장치 유형이 예상대로 동작하는지 테스트합니다. 업데이트된 장치 유형을 로컬로 테스트 및 디버그하면 컨테이너를 다시 빌드하고 **장치-시뮬레이션** 서비스를 Azure로 다시 배포할 수 있습니다.

**장치-시뮬레이션** 서비스를 로컬로 실행하는 경우 원격 분석 솔루션에 원격 분석을 보냅니다. **장치** 페이지에서 업데이트된 유형의 인스턴스를 프로비전할 수 있습니다.

변경 내용을 로컬로 테스트 및 디버그하려면 이전 섹션인 [전구 장치 유형을 로컬로 테스트](#test-the-lightbulb-device-type-locally)를 참조하세요.

Azure에서 솔루션의 가상 머신에 업데이트된 장치 시뮬레이션 서비스를 배포하려면 이전 섹션인 [업데이트된 시뮬레이터를 클라우드에 배포](#deploy-the-updated-simulator-to-the-cloud)를 참조하세요.

**장치** 페이지에서 업데이트된 유형의 인스턴스를 프로비전할 수 있습니다.

![업데이트된 냉각기 추가](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

시뮬레이트된 장치에서 **내부 온도** 원격 분석을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서는 다음 방법을 보여 줬습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 새 장치 유형 만들기
> * 사용자 지정 장치 동작 시뮬레이트
> * 대시보드에 새 장치 유형 추가
> * 기존의 장치 유형에서 사용자 지정 원격 분석 보내기

장치 시뮬레이션 서비스를 사용자 지정하는 방법을 배웠습니다. 제안된 다음 단계는 [물리적 장치를 원격 모니터링 솔루션에 연결](iot-accelerators-connecting-devices-node.md)하는 방법을 알아보기 위한 것입니다.

원격 모니터링 솔루션에 대한 자세한 개발자 정보는 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
