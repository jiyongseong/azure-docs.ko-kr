---
title: Azure Function 및 Cognitive Services를 사용하는 IoT DevKit 번역기 | Microsoft Docs
description: IoT DevKit에서 마이크를 사용하여 영어로 번역된 텍스트로 처리하기 위해 음성 메시지 및 Azure Cognitive Services를 수신합니다.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: 20e5a5f4fb381dedc42d698464819a6098c3579b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Azure Function 및 Cognitive Services에서 IoT DevKit AZ3166을 사용하여 언어 번역기 만들기

이 아티클에서는 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 사용하여 IoT DevKit을 언어 번역기로 사용하는 방법을 알아봅니다. 음성을 기록하고 DevKit 화면에 표시된 영어 텍스트로 변환합니다.

[MXChip IoT DevKit](https://aka.ms/iot-devkit)은 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. [Arduino용 Visual Studio Code 확장](https://aka.ms/arduino)을 사용하여 개발할 수 있습니다. 그리고 Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션의 프로토타입을 안내하기 위해 확장 중인 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)가 제공됩니다.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 완료하여:

* DevKit을 Wi-Fi에 연결
* 개발 환경 준비

활성 Azure 구독. 구독이 없으면 다음 두 가지 방법 중 하나를 통해 등록할 수 있습니다.

* [30일 평가판 Microsoft Azure 계정](https://azure.microsoft.com/free/) 활성화
* MSDN 또는 Visual Studio 구독자인 경우 [Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 신청합니다.

## <a name="step-1-open-the-project-folder"></a>1단계. 프로젝트 폴더 열기

### <a name="a-start-vs-code"></a>a. VS Code 시작

- DevKit가 PC에 연결되어 있지 않은지 확인합니다.
- VS Code 시작
- DevKit를 컴퓨터에 연결합니다.

### <a name="b-open-the-arduino-examples-folder"></a>B. Arduino 예제 폴더 열기

왼쪽의 **ARDUINO 예제 > MXCHIP AZ3166에 대한 예제 > AzureIoT**를 확장하고 **DevKitTranslator**를 선택합니다. DEVKITTRANSLATOR 프로젝트 폴더가 있는 새 VS Code 창이 열립니다.

![IoT DevKit 샘플](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

> [!NOTE]
> 명령 팔레트에서 예제를 열 수도 있습니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음 **Arduino: 예제**를 찾아서 선택합니다.

## <a name="step-2-provision-azure-services"></a>2단계. Azure 서비스 프로비전

솔루션 창에서 `Ctrl+P`(macOS: `Cmd+P`)를 입력하고 `task cloud-provision`을 입력합니다.

VS Code 터미널에서 대화형 명령줄은 필요한 모든 Azure 서비스를 프로비전하는 과정을 설명합니다.

![클라우드 프로비전 작업](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>3단계. Azure Functions 배포

`Ctrl+P`(macOS: `Cmd+P`)를 사용하여 `task cloud-deploy`를 실행하고 Azure Functions 코드를 배포합니다. 이 프로세스를 완료하는 데 보통 2~5분이 걸립니다.

![클라우드 배포 작업](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Azure Function가 성공적으로 배포된 후에 함수 앱 이름으로 azure_config.h 파일을 채웁니다. [Azure Portal](https://portal.azure.com/)로 이동하여 찾을 수 있습니다.

![Azure Function 앱 이름 찾기](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Azure Function이 제대로 작동하지 않는 경우 이 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) 섹션을 확인하여 문제를 해결합니다.

## <a name="step-4-build-and-upload-the-device-code"></a>4단계. 장치 코드 빌드 및 업로드

1. `Ctrl+P`(macOS: `Cmd+P`)를 사용하여 `task config-device-connection`을 실행합니다.

2. `task cloud-provision` 단계에서 검색된 연결 문자열을 사용할지 묻는 메시지가 터미널에 표시됩니다. **'새로 만들기...'** 를 선택하여 고유한 장치 연결 문자열을 입력할 수도 있습니다.

3. 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에 DevKit ID와 '구성'이 표시됩니다.
  ![Arduino 스케치 확인 및 업로드](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. `task config-device-connection`이 완료된 후 `F1`을 클릭하여 VS Code 명령을 로드하고 `Arduino: Upload`를 선택하면 VS Code에서 Arduino 스케치를 확인하고 업로드하기 시작합니다. ![Arduino 스케치 확인 및 업로드](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

## <a name="test-the-project"></a>프로젝트 테스트

앱 초기화 후에 DevKit 화면의 지시를 따릅니다. 기본 소스 언어는 중국어입니다.

번역에서 다른 언어를 선택하려면:

1. 단추 A를 눌러서 설치 모드로 전환합니다.
2. 단추 B를 눌러서 지원되는 모든 원본 언어를 스크롤합니다.
3. 단추 A를 눌러서 소스 언어의 선택 내용을 확인합니다.
4. 말하는 동안 단추 B를 누른 다음, 단추 B를 놓고 변환을 시작합니다.
5. 영어로 번역된 텍스트가 화면에 표시됩니다.

![스크롤하여 언어 선택](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![변환 결과](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

변환 결과 화면에서 다음을 수행할 수 있습니다.

- 단추 A와 B를 눌러서 소스 언어를 스크롤하여 선택합니다.
- 말하려면 단추 B를 누르고, 음성을 보내고 변환 텍스트를 가져오려면 놓습니다.

## <a name="how-it-works"></a>작동 방법

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Arduino 스케치는 음성을 기록한 다음, HTTP 요청을 게시하여 Azure Functions를 트리거합니다. Azure Functions는 Cognitive Service 음성 변환기 API를 호출하여 변환 작업을 수행합니다. Azure Functions가 변환 텍스트를 가져온 후에 장치에 C2D 메시지를 보냅니다. 그런 다음, 변환이 화면에 표시됩니다.

## <a name="change-device-id"></a>장치 ID 변경

Azure IoT Hub에 등록된 기본 장치 ID는 **AZ3166**입니다. 수정하려면 [여기의 지침](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)을 따릅니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 채널을 통해 문의해주세요.

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

이제 Azure Function 및 Cognitive Services를 사용하여 IoT DevKit를 번역기로 만들었습니다. 이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Visual Studio Code 작업을 사용하여 클라우드 프로비전 자동화
> * Azure IoT 장치 연결 문자열 구성
> * Azure Function 배포
> * 음성 메시지 변환 테스트

자세히 알아보려면 다른 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166을 Azure IoT 원격 모니터링 솔루션 가속기에 연결](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
