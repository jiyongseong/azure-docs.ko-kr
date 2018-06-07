---
title: Node.js를 사용하여 장치에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: Node.js용 Azure IoT 장치 SDK를 사용하여 장치에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: b28a02462fe7a5a7f831102b3707fe03f84342ad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>IoT Hub를 사용하여 장치에서 클라우드로 파일 업로드

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기](iot-hub-node-node-c2d.md) 자습서의 코드를 기반으로 작성되었으며 [IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하여 [Azure Blob Storage](../storage/index.yml)에 파일을 업로드하는 방법을 보여 줍니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- 파일을 업로드하기 위한 Azure blob URI를 장치에 안전하게 제공합니다.
- IoT Hub 파일 업로드 알림을 사용하여 앱 백 엔드에서 파일 처리를 트리거합니다.

[IoT Hub 시작](iot-hub-node-node-getstarted.md) 자습서는 IoT Hub의 기본 장치-클라우드 메시지 기능을 보여줍니다. 그러나 일부 시나리오에서는 장치에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 장치-클라우드 메시지에 쉽게 매핑할 수 없습니다. 예: 

* 이미지가 포함된 대형 파일
* 동영상
* 자주 샘플링되는 진동 데이터
* 특정 형태의 전처리된 데이터

이러한 파일은 일반적으로 [Azure Data Factory](../data-factory/introduction.md) 또는 [Hadoop](../hdinsight/index.yml) 스택과 같은 도구를 사용하여 클라우드에서 배치 방식으로 처리됩니다. 장치에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

이 자습서의 끝 부분에서 다음 두 개의 Node.js 콘솔 앱을 실행합니다.

* **SimulatedDevice.js** - IoT Hub에서 제공하는 SAS URI를 사용하여 저장소에 파일을 업로드합니다.
* **ReadFileUploadNotification** - IoT Hub에서 파일 업로드 알림을 받습니다.

> [!NOTE]
> IoT Hub는 Azure IoT 장치 SDK를 통해 많은 장치 플랫폼 및 언어(C, .NET, Javascript, Python 및 Java 포함)를 지원합니다. Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 4.0.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](http://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>장치 앱에서 파일 업로드

이 섹션에서는 IoT Hub에 파일을 업로드하는 장치 앱을 만듭니다.

1. ```simulateddevice```라는 빈 폴더를 만듭니다.  ```simulateddevice``` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

1. ```simulateddevice``` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 텍스트 편집기를 사용하여 ```simulateddevice``` 폴더에 **SimulatedDevice.js** 파일을 만듭니다.

1. **SimulatedDevice.js** 파일 앞에 다음 ```require``` 문을 추가합니다.

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. ```deviceconnectionstring``` 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  ```{deviceconnectionstring}```을 _IoT Hub 만들기_ 섹션에서 만든 장치 이름으로 바꿉니다.

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > 간소화를 위해 연결 문자열이 코드에 포함되어 있습니다. 이것은 권장되는 방법이 아니며 사용 사례 및 아키텍처에 따라 이러한 비밀을 보다 안전하게 저장하는 방법을 고려할 수 있습니다.

1. 클라이언트에 연결하도록 다음 코드를 추가합니다.

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. 콜백을 만들고 **uploadToBlob** 함수를 사용하여 파일을 업로드합니다.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. **SimulatedDevice.js** 파일을 저장한 후 닫습니다.

1. 이미지 파일을 `simulateddevice` 폴더에 복사하고 파일 이름을 `myimage.png`로 바꿉니다.

## <a name="receive-a-file-upload-notification"></a>파일 업로드 알림 수신

이 섹션에서는 IoT Hub로부터 파일 업로드 알림 메시지를 수신하는 Node.js 콘솔 앱을 만듭니다.

이 섹션을 완료하려면 IoT Hub의 **iothubowner** 연결 문자열을 사용할 수 있습니다. 연결 문자열은 **공유 액세스 정책** 블레이드의 [Azure Portal](https://portal.azure.com/)에서 찾을 수 있습니다.

1. ```fileuploadnotification```라는 빈 폴더를 만듭니다.  ```fileuploadnotification``` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

1. ```fileuploadnotification``` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** SDK 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. 텍스트 편집기를 사용하여 ```fileuploadnotification``` 폴더에 **FileUploadNotification.js** 파일을 만듭니다.

1. **FileUploadNotification.js** 파일의 시작 부분에 다음 ```require``` 문을 추가합니다.

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. ```iothubconnectionstring``` 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  ```{iothubconnectionstring}```을 _IoT Hub 만들기_ 섹션에서 만든 IoT Hub에 대한 연결 문자열로 바꿉니다.

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > 간소화를 위해 연결 문자열이 코드에 포함되어 있습니다. 이것은 권장되는 방법이 아니며 사용 사례 및 아키텍처에 따라 이러한 비밀을 보다 안전하게 저장하는 방법을 고려할 수 있습니다.

1. 클라이언트에 연결하도록 다음 코드를 추가합니다.

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. 클라이언트를 열고 상태 업데이트를 수신하는 **getFileNotificationReceiver** 함수를 사용합니다.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. **FileUploadNotification.js** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

`fileuploadnotification` 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
node FileUploadNotification.js
```

`simulateddevice` 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
node SimulatedDevice.js
```

다음 스크린샷은 **SimulatedDevice** 앱의 출력을 보여 줍니다.

![simulated-device 앱의 출력](./media/iot-hub-node-node-file-upload/simulated-device.png)

다음 스크린샷은 **FileUploadNotification** 앱의 출력을 보여 줍니다.

![read-file-upload-notification 앱의 출력](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

포털을 사용하면 구성한 저장소 컨테이너에 업로드된 파일을 볼 수 있습니다.

![업로드된 파일](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 장치에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기][lnk-create-hub]
* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

<!-- Links -->
[Azure IoT 개발자 센터]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
