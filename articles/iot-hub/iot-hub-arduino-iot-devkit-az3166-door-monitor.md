---
title: SendGrid 서비스 및 Azure Functions를 사용하여 문을 여는 경우 이메일 수신 | Microsoft Docs
description: 자기 센서를 모니터링하여 문이 열릴 때를 감지하고 Azure Functions를 사용하여 이메일 알림을 보냅니다.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 4eb13a588f0ffd1377caf3ce9bac4886f052a22f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="door-monitor"></a>문 모니터링          

MXChip IoT DevKit에는 기본 제공 자기 센서가 포함되어 있습니다. 이 프로젝트에서 주변의 강력한 자기 필드 유무를 검색할 있습니다. 이 경우에는 작은 영구 자석입니다.

## <a name="what-you-learn"></a>학습 내용

이 프로젝트에서는 다음에 대해 알아봅니다.
- MXChip IoT DevKit의 자기 센서를 사용하여 주변 자석의 움직임을 감지하는 방법
- SendGrid 서비스를 사용하여 이메일 주소로 알림을 보내는 방법

> [!NOTE]
> 이 프로젝트를 실제 사용하려면:
> - 문의 가장자리에 자석을 탑재합니다.
> - 자석 가까이에 있는 문설주에 DevKit를 탑재합니다. 문을 열거나 닫으면 센서가 트리거됩니다. 그 결과로 이벤트의 이메일 알림을 수신하게 됩니다.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드]({{"/docs/get-started/" | absolute_url }})를 완료하여:

* DevKit을 Wi-Fi에 연결
* 개발 환경 준비

활성 Azure 구독. 구독이 없으면 다음과 같은 방법 중 하나를 통해 등록할 수 있습니다.

* [30일 평가판 Microsoft Azure 계정](https://azure.microsoft.com/free/)을 활성화합니다.
* MSDN 또는 Visual Studio 구독자인 경우 [Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 신청합니다.

## <a name="deploy-sendgrid-service-in-azure"></a>Azure에서 SendGrid 서비스 배포

[SendGrid](https://sendgrid.com/)는 클라우드 기반 이메일 배달 플랫폼입니다. 이 서비스는 이메일 알림을 보내는 데 사용됩니다.

> [!NOTE]
> SendGrid 서비스를 이미 배포한 경우 직접 [Azure에서 IoT Hub 배포](#deploy-iot-hub-in-azure)를 진행할 수 있습니다.

### <a name="sendgrid-deployment"></a>SendGrid 배포

Azure 서비스를 프로비전하려면 **Azure에 배포** 단추를 사용합니다. 이 단추를 사용하면 Microsoft Azure에 오픈 소스 프로젝트를 빠르고 쉽게 배포할 수 있습니다.

아래에서 **Azure에 배포** 단추를 클릭합니다. 

[![Azure에 배포](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

그러면 다음 페이지가 표시됩니다.

> [!NOTE]
> 다음 페이지가 표시되지 않는 경우 먼저 Azure 계정에 로그인해야 합니다.

등록 양식을 작성합니다.

  * **리소스 그룹**: SendGrid 서비스를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. [리소스 그룹을 사용하여 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)(영문)를 참조하세요.

  * **이름**: SendGrid 서비스의 이름입니다. 이미 설치된 다른 서비스와 다른 고유한 이름을 선택합니다.

  * **암호**: 이 프로젝트에서 필요하지 않더라도 서비스에는 암호가 필요합니다.

  * **이메일**: SendGrid 서비스에서는 이 이메일 주소로 확인을 보냅니다.

  > [!NOTE]
  > **대시보드에 고정** 옵션을 선택하여 이 응용 프로그램을 나중에 쉽게 찾을 수 있습니다.
 
![SendGrid 배포](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>SendGrid API 키 생성

배포에 성공한 후에 해당 서비스를 클릭한 다음, **관리** 단추를 클릭합니다. SendGrid 페이지로 이동하면 이메일 주소를 확인해야 합니다.

![SendGrid 관리](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

SendGrid 페이지에서 **설정** > **API 키** > **API 키 만들기**를 클릭합니다. **API 키 이름**을 입력하고 **만들기 및 보기**를 클릭합니다.

![SendGrid API 처음 만들기](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![SendGrid API 두 번째 만들기](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

API 키는 한 번만 표시됩니다. 다음 단계에서 사용되므로 복사하고 안전하게 저장해야 합니다.

## <a name="deploy-iot-hub-in-azure"></a>Azure에서 IoT Hub 배포

다음 단계에서는 다른 Azure IoT 관련 서비스를 프로비전하고 이 프로젝트에 Azure Functions를 배포합니다.

아래에서 **Azure에 배포** 단추를 클릭합니다. 

[![Azure에 배포](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

그러면 다음 페이지가 표시됩니다.

> [!NOTE]
> 다음 페이지가 표시되지 않는 경우 먼저 Azure 계정에 로그인해야 합니다.

등록 양식을 작성합니다.

  * **리소스 그룹**: SendGrid 서비스를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. [리소스 그룹을 사용하여 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)(영문)를 참조하세요.

  * **IoT Hub이름**: IoT Hub의 이름. 이미 설치된 다른 서비스와 다른 고유한 이름을 선택합니다.

  * **IoT Hub SKU**: F1(구독 당 하나로 제한)은 무료입니다. [가격 책정 및 규모 계층](https://azure.microsoft.com/pricing/details/iot-hub/)에서 더 많은 가격 책정 정보를 볼 수 있습니다.

  * **이메일에서**: 이 SendGrid 서비스를 설정할 때 사용한 동일한 이메일 주소여야 합니다.

  > [!NOTE]
  > **대시보드에 고정** 옵션을 선택하여 이 응용 프로그램을 나중에 쉽게 찾을 수 있습니다.
 
![IoTHub 배포](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>코드 빌드 및 업로드

### <a name="start-vs-code"></a>VS Code 시작

- DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다.
- VS Code를 시작합니다.
- DevKit를 컴퓨터에 연결합니다.

> [!NOTE]
> VS Code를 시작할 때 Arduino IDE 또는 관련 보드 패키지를 찾을 수 없다는 오류 메시지가 표시될 수 있습니다. 이 오류를 수신한 경우 VS Code를 닫고 Arduino IDE를 다시 시작하면 VS Code는 Arduino IDE 경로를 올바르게 찾습니다.

### <a name="open-arduino-examples-folder"></a>Arduino 예제 폴더 열기

왼쪽의 **ARDUINO 예제** 섹션을 확장하고 **MXCHIP AZ3166에 대한 예제 > AzureIoT**로 이동하여 **DoorMonitor**를 선택합니다. 이 작업은 프로젝트 폴더가 있는 새 VS Code 창을 엽니다.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> 명령 팔레트에서 예제를 열 수도 있습니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음 **Arduino: 예제**를 찾아서 선택합니다.

### <a name="provision-azure-services"></a>Azure 서비스 프로비전

솔루션 창에서 클라우드 프로비전 작업을 실행합니다.
- `Ctrl+P`(macOS: `Cmd+P`)를 입력합니다.
- 제공된 텍스트 상자에 `task cloud-provision`을 입력합니다.

VS Code 터미널에서 대화형 명령줄은 필요한 Azure 서비스를 프로비전하는 과정을 안내합니다. [Azure에서 IoT Hub 배포](#deploy-iot-hub-in-azure)에서 이전에 프로비전한 메시지 표시 목록에서 동일한 항목을 모두 선택합니다.

![클라우드 프로비전](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Azure에 로그인하려고 시도할 때 페이지가 로딩 상태에서 중지되는 경우 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)를 참조하여 문제를 해결하세요. 

### <a name="build-and-upload-the-device-code"></a>장치 코드 빌드 및 업로드

#### <a name="windows"></a>Windows

1. `Ctrl+P`를 사용하여 `task device-upload`를 실행합니다.
2. 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에서는 DevKit ID 번호와 *구성*이라는 단어를 표시합니다.

이 프로시저에서는 [Azure 서비스 프로비전](#provision-azure-services) 단계에서 검색되는 연결 문자열을 설정합니다.

그러면 VS Code가 Arduino 스케치를 확인하고 DevKit에 업로드합니다.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

> [!NOTE]
> 경우에 따라 "오류: AZ3166: 알 수 없는 패키지" 오류 메시지가 표시될 수 있습니다. 보드 패키지 인덱스가 올바르게 새로 고쳐지지 않으면 이 오류가 발생합니다. 이 오류를 해결하려면 이 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)를 참조합니다.

#### <a name="macos"></a>macOS

1. DevKit을 구성 모드로 전환: 단추 A를 길게 누른 후 재설정 단추를 눌렀다가 놓습니다. 화면에는 '구성'이 표시됩니다.
2. `Cmd+P`를 사용하여 `task device-upload`를 실행합니다.

이 프로시저에서는 [Azure 서비스 프로비전](#provision-azure-services) 단계에서 검색되는 연결 문자열을 설정합니다.

그러면 VS Code가 Arduino 스케치를 확인하고 DevKit에 업로드합니다.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

> [!NOTE]
> 경우에 따라 "오류: AZ3166: 알 수 없는 패키지" 오류 메시지가 표시될 수 있습니다. 보드 패키지 인덱스가 올바르게 새로 고쳐지지 않으면 이 오류가 발생합니다. 이 오류를 해결하려면 이 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)를 참조합니다.

## <a name="test-the-project"></a>프로젝트 테스트

DevKit가 안정적인 자기 필드가 있는 경우 프로그램이 먼저 초기화됩니다.

초기화 후에 `Door closed`가 화면에 표시됩니다. 자기 필드가 상태가 변경된 경우 상태가 `Door opened`로 변경됩니다. 문 상태가 변경될 때마다 이메일 알림을 수신합니다. (이러한 이메일 메시지를 수신하는 데 최대 5분이 걸릴 수 있습니다.)

![센서에 가까운 자석: 문 닫힘](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "센서에 가까운 자석: 문 닫힘")

![센서에서 떨어져 있는 자석: 문 열림](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "센서에서 떨어져 있는 자석: 문 열림")

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 채널을 사용하여 문의해주세요.

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

Azure IoT 원격 모니터링 솔루션 가속기에 DevKit 장치를 연결하고 SendGrid 서비스를 사용하여 이메일을 보내는 방법에 대해 알아보았습니다. 권장되는 단계는 다음과 같습니다.

* [Azure IoT 원격 모니터링 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-suite/)
* [Azure IoT Central 응용 프로그램에 MXChip IoT DevKit 장치 연결](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
