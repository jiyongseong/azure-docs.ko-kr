---
title: Python을 사용하여 장치에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: Python용 Azure IoT 장치 SDK를 사용하여 장치에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 7f64783f5e1c79436b671ef98f30f5e3594b94e6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>IoT Hub를 사용하여 장치에서 클라우드로 파일 업로드

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 [IoT 허브의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하여 [Azure Blob 저장소](../storage/index.yml)에 파일을 업로드하는 방법에 대해 설명합니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- 파일을 업로드하기 위해 저장소 컨테이너를 안전하게 제공합니다.
- Python 클라이언트를 사용하여 IoT 허브를 통해 파일을 업로드합니다.

[IoT Hub 시작](iot-hub-node-node-getstarted.md) 자습서는 IoT Hub의 기본 장치-클라우드 메시지 기능을 보여줍니다. 그러나 일부 시나리오에서는 장치에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 장치-클라우드 메시지에 쉽게 매핑할 수 없습니다. 장치에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

> [!NOTE]
> IoT 허브 Python SDK는 현재 **.txt** 파일과 같은 문자 기반 파일의 업로드만 지원합니다.

이 자습서의 끝 부분에서 Python 콘솔 앱을 실행합니다.

* **FileUpload.py**는 Python 장치 SDK를 사용하여 파일을 저장소로 업로드합니다.

> [!NOTE]
> IoT Hub는 Azure IoT 장치 SDK를 통해 많은 장치 플랫폼 및 언어(C, .NET, Javascript, Python 및 Java 포함)를 지원합니다. Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](http://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>장치 앱에서 파일 업로드

이 섹션에서는 IoT Hub에 파일을 업로드하는 장치 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub-device-client** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 텍스트 편집기를 사용하여 작업 폴더에 **FileUpload.py** 파일을 만듭니다.

1. **FileUpload.py** 파일의 시작 부분에서 다음 `import` 문 및 변수를 추가합니다. `deviceConnectionString`을 IoT 허브 장치의 연결 문자열로 바꿉니다.

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name on storage after upload]"
    ```

1. **upload_blob** 함수에 대한 콜백을 만듭니다.

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. 다음 코드를 추가하여 클라이언트를 연결하고 파일을 업로드합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

1. **UploadFile.py** 파일을 저장하고 닫습니다.

1. 샘플 텍스트 파일을 작업 폴더에 복사하고 이름을 `sample.txt`로 바꿉니다.

    > [!NOTE]
    > IoT 허브 Python SDK는 현재 **.txt** 파일과 같은 문자 기반 파일의 업로드만 지원합니다.


## <a name="run-the-application"></a>응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. 작업 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd/sh
    python FileUpload.py
    ```

1. 다음 스크린샷은 **FileUpload** 앱의 출력을 보여줍니다.

    ![simulated-device 앱의 출력](./media/iot-hub-python-python-file-upload/1.png)

1. 포털을 사용하면 구성한 저장소 컨테이너에 업로드된 파일을 볼 수 있습니다.

    ![업로드된 파일](./media/iot-hub-python-python-file-upload/2.png)


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
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
