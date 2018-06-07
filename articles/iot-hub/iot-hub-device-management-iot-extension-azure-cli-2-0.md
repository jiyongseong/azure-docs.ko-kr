---
title: Azure CLI 2.0용 IoT 확장을 사용하여 Azure IoT 장치 관리 | Microsoft Docs
description: Direct 메서드와 Twin의 desired 속성 관리 옵션을 제공하는 Azure CLI 2.0용 IoT 확장 도구를 사용하여 Azure IoT Hub 장치를 관리합니다.
services: iot-hub
documentationcenter: ''
author: chrissie926
manager: timlt
tags: ''
keywords: Azure IoT 장치 관리, Azure IoT Hub 장치 관리, 장치 관리 IoT, IoT Hub 장치 관리
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 27a8dca68000e3f4b7f33508fc6dbc3450fc80b4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Azure CLI 2.0용 IoT 확장을 사용하여 Azure IoT Hub 장치 관리

![종단 간 다이어그램](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure CLI 2.0에 대한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension)은 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)의 기능에 추가하는 새 오픈 소스 IoT 확장입니다. Azure CLI 2.0에는 Azure Resource Manager 및 관리 끝점과 상호 작용하기 위한 명령이 포함되어 있습니다. 예를 들어 Azure CLI 2.0을 사용하여 Azure VM 또는 IoT 허브를 만들 수 있습니다. CLI 확장을 사용하면 Azure 서비스는 Azure CLI를 확대하여, 사용자에게 추가적인 서비스별 기능에 대한 액세스 권한을 부여할 수 있습니다. IoT 확장은 IoT 개발자가 명령줄을 통해 모든 IoT Hub, IoT Edge, IoT Hub 장치 프로비전 서비스 기능에 액세스할 수 있도록 합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 관리 옵션          | Task                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| 직접 메서드             | 메시지 보내기 시작 또는 중지, 장치 다시 부팅 등의 장치 작업을 수행합니다.                                        |
| Twin desired 속성    | 장치를 특정 상태(예: LED를 녹색으로 설정 또는 원격 분석 전송 간격을 30 분으로 설정)로 지정합니다.         |
| Twin reported 속성   | 장치의 보고된 상태를 가져옵니다. 예를 들어 장치에서 지금 LED가 깜박이고 있다고 보고합니다.                                    |
| Twin tags                  | 클라우드에서 장치 전용 메타데이터를 저장합니다. 예를 들어 자동 판매기의 배포 위치입니다.                         |
| 장치 쌍 쿼리        | 모든 장치 쌍을 쿼리하여 사용할 수 있는 장치를 식별하는 등 임의의 조건에 해당하는 장치를 검색합니다. |

이러한 옵션을 사용하는 방법에 대한 차이점과 지침에 대한 자세한 내용은 [장치-클라우드 통신 지침](iot-hub-devguide-d2c-guidance.md) 및 [클라우드-장치 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조하세요.

장치 쌍은 장치의 상태 정보(메타데이터, 상태 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 여기에 연결하는 각 장치에 대해 하나의 장치 쌍을 유지합니다. 장치 쌍에 대한 자세한 내용은 [장치 쌍 시작](iot-hub-node-node-twin-getstarted.md)을 참조하세요.

## <a name="what-you-learn"></a>학습 내용

배포 컴퓨터에서 다양한 관리 옵션으로 Azure CLI 2.0용 IoT 확장을 사용하는 방법에 대해 알아봅니다.

## <a name="what-you-do"></a>수행할 작업

다양한 관리 옵션을 사용하여 Azure CLI 2.0 및 Azure CLI 2.0용 IoT 확장을 실행합니다.

## <a name="what-you-need"></a>필요한 항목

- 다음 요구 사항을 다루는 자습서 [장치 설정](iot-hub-raspberry-pi-kit-node-get-started.md) 완료:
  - 활성 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 클라이언트 응용 프로그램

- 이 자습서를 진행하는 동안 장치가 클라이언트 응용 프로그램을 사용해서 실행되고 있어야 합니다.

- [Python 2.7x 또는 Python 3.x](https://www.python.org/downloads/)

- Azure CLI 2.0을 설치합니다. Windows에 설치하는 간단한 방법 중 하나는 [MSI](https://aka.ms/InstallAzureCliWindows)를 다운로드하여 설치하는 것입니다. [Microsoft 문서](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)의 설치 지침에 따라 환경에 Azure CLI 2.0을 설치합니다. Azure CLI 2.0 버전이 2.0.24 이상이어야 합니다. `az –version` 명령을 사용하여 유효성을 검사합니다. 

- IoT 확장을 설치합니다. 가장 간단한 방법은 `az extension add --name azure-cli-iot-ext` 명령을 사용하는 것입니다. [IoT 확장 추가 정보](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md)에는 확장을 설치하는 여러 가지 방법이 설명되어 있습니다.


## <a name="log-in-to-your-azure-account"></a>Azure 계정에 로그인합니다.

다음 명령을 실행하여 Azure 계정에 로그인합니다.

```bash
az login
```

## <a name="direct-methods"></a>직접 메서드

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>장치 쌍 desired 속성

다음 명령을 실행하여 desired 속성의 간격(interval = 3000)을 설정합니다.

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

이 속성은 장치에서 읽을 수 있습니다.

## <a name="device-twin-reported-properties"></a>장치 쌍 reported 속성

다음 명령을 실행하여 장치의 reported 속성을 가져옵니다.

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

쌍 reported 속성 중 하나는 $metadata.$lastUpdated로서 장치 앱이 해당 reported 속성 집합을 업데이트한 마지막 시간을 나타냅니다.

## <a name="device-twin-tags"></a>장치 쌍 태그

다음 명령을 실행하여 장치의 태그와 속성을 표시합니다.

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

다음 명령을 실행하여 장치에 역할(role = temperature&humidity) 필드를 추가합니다.

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>장치 쌍 쿼리

다음 명령을 실행하여 role = 'temperature & humidity' 태그가 있는 장치를 쿼리합니다.

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

다음 명령을 실행하여 role = 'temperature & humidity' 태그가 있는 장치를 제외한 모든 장치를 쿼리합니다.

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>다음 단계

IoT 장치와 Azure IoT Hub 간에 장치-클라우드 메시지를 모니터링하고 클라우드-장치 메시지를 보내는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
