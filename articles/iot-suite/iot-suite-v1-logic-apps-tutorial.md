---
title: "Azure IoT Suite 및 Logic Apps | Microsoft Docs"
description: "Logic Apps를 비즈니스 프로세스에 대한 Azure IoT Suite에 연결하는 방법에 대한 자습서입니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>자습서: 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 논리 앱 연결
미리 구성된 [Microsoft Azure IoT Suite][lnk-internetofthings] 원격 모니터링 솔루션은 IoT 솔루션의 예를 보여 주는 종단 간 기능 집합으로 신속하게 시작할 수 있는 훌륭한 방법입니다. 이 자습서에서는 미리 구성된 Microsoft Azure IoT Suite 원격 모니터링 솔루션에 논리 앱을 추가하는 방법을 안내합니다. 이러한 단계에서는 IoT 솔루션을 비즈니스 프로세스에 연결하여 추가로 활용할 수 있는 방법을 설명합니다.

*미리 구성된 원격 모니터링 솔루션을 프로비전하는 방법을 찾고 있는 경우 [자습서: 미리 구성된 IoT 솔루션 시작][lnk-getstarted]을 참조하세요.*

이 자습서를 시작하기 전에 먼저 다음을 수행해야 합니다.

* Azure 구독에서 미리 구성된 원격 모니터링 솔루션을 프로비전합니다.
* 비즈니스 프로세스를 트리거하는 전자 메일을 보낼 수 있도록 SendGrid 계정을 만듭니다. [SendGrid](https://sendgrid.com/) 에서 **무료 평가판**을 클릭하여 무료 평가판 계정을 등록할 수 있습니다. 무료 평가판 계정을 등록한 후에는 SendGrid에서 메일 전송 권한을 부여하는 [API 키](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) 를 만들어야 합니다. 이 API 키는 자습서의 뒷부분에서 필요합니다.

이 자습서를 완료하려면 미리 구성된 솔루션 백 엔드에서 동작을 수정하기 위해 Visual Studio 2015 또는 Visual Studio 2017이 필요합니다.

미리 구성된 원격 모니터링 솔루션을 이미 프로비전했다고 가정하고 [Azure Portal][lnk-azureportal]에서 해당 솔루션에 대한 리소스 그룹으로 이동합니다. 리소스 그룹 이름이 원격 모니터링 솔루션을 프로비전할 때 선택한 솔루션 이름과 같습니다. 리소스 그룹에서 솔루션에 대해 프로비전된 모든 Azure 리소스를 볼 수 있습니다. 다음 스크린샷은 미리 구성된 원격 모니터링 솔루션에 대한 예제 **리소스 그룹** 블레이드를 보여 줍니다.

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

시작하려면 미리 구성된 솔루션과 함께 사용할 논리 앱을 설정합니다.

## <a name="set-up-the-logic-app"></a>논리 앱 설정
1. Azure Portal의 리소스 그룹 블레이드 맨 위에서 **추가**를 클릭합니다.
2. **Logic App**을 검색하여 선택한 후 **만들기**를 클릭합니다.
3. **이름**을 입력하고 원격 모니터링 솔루션을 프로비전할 때 사용한 것과 동일한 **구독** 및 **리소스 그룹**을 사용합니다. **만들기**를 클릭합니다.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. 배포가 완료되면 논리 앱이 리소스 그룹에 리소스로 나열되는 것을 확인할 수 있습니다.
5. Logic App을 클릭하여 Logic App 블레이드로 이동하고 **빈 Logic App** 템플릿을 선택하여 **Logic Apps 디자이너**를 엽니다.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. **요청**을 선택합니다. 이 작업은 특정 JSON 형식 페이로드와 함께 들어오는 HTTP 요청이 트리거로 작동하도록 지정합니다.
7. 다음 코드를 요청 본문 JSON 스키마에 붙여 넣습니다.
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > 논리 앱을 저장한 후 HTTP POST에 대한 URL을 복사할 수 있지만 먼저 작업을 추가해야 합니다.
   > 
   > 
8. 수동 트리거 아래에서 **+ 새 단계**를 클릭합니다. 그런 다음 **작업 추가**를 클릭합니다.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. **SendGrid - 메일 보내기**를 검색하여 클릭합니다.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. 연결 이름(예: **SendGridConnection**)을 입력하고 SendGrid 계정을 설정할 때 만든 **SendGrid API 키**를 입력하고 **만들기**를 클릭합니다.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. 소유한 전자 메일 주소를 **보낸 사람** 및 **받는 사람** 필드에 추가합니다. **원격 모니터링 경고[DeviceId]**를 **제목** 필드에 추가합니다. **전자 메일 본문** 필드에 **장치[DeviceId]가 [measuredValue] 값과 함께 [measurementName]을 보고했습니다.**를 추가합니다. **이전 단계에서 데이터를 삽입할 수 있습니다** 섹션을 클릭하여 **[DeviceId]**, **[measurementName]** 및 **[measuredValue]**를 추가할 수 있습니다.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. 최상위 메뉴에서 **저장**을 클릭합니다.
13. **요청** 트리거를 클릭하고 **이 URL에 대한 Http 게시** 값을 복사합니다. 이 URL은 자습서의 뒷부분에서 필요합니다.

> [!NOTE]
> Logic Apps를 사용하면 Office 365의 작업을 비롯하여 [다양한 유형의 작업][lnk-logic-apps-actions]을 실행할 수 있습니다. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>EventProcessor 웹 작업 설정
이 섹션에서는 미리 구성된 솔루션을 만든 논리 앱에 연결합니다. 이 작업을 완료하려면 URL을 추가하여 장치 센서 값이 임계값을 초과할 때 발생하는 작업에 논리 앱을 트리거합니다.

1. git 클라이언트를 사용하여 최신 버전의 [azure-iot-remote-monitoring github 리포지토리][lnk-rmgithub]를 복제합니다. 예:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. Visual Studio에서 리포지토리의 로컬 복사본에서 **RemoteMonitoring.sln**을 엽니다.
3. **Infrastructure\\Repository** 폴더에서 **ActionRepository.cs** 파일을 엽니다.
4. 다음과 같이 **actionIds** 사전을 Logic App에서 기록한 **이 URL에 대한 Http 게시**로 업데이트합니다.
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. 변경 내용을 솔루션에 저장하고 Visual Studio를 종료합니다.

## <a name="deploy-from-the-command-line"></a>명령줄에서 배포
이 섹션에서는 Azure에서 현재 실행 중인 버전을 대체하도록 원격 모니터링 솔루션의 업데이트된 버전을 배포합니다.

1. [개발 설정][lnk-devsetup] 지침에 따라 배포 환경을 설정합니다.
2. 로컬로 배포하려면 [로컬 배포][lnk-localdeploy] 지침을 따릅니다.
3. 클라우드로 배포하고 기존 클라우드 배포를 업데이트하려면 [클라우드 배포][lnk-clouddeploy] 지침을 따릅니다. 원래 배포의 이름을 배포 이름으로 사용합니다. 예를 들어 원래 배포가 **demologicapp**였다면 다음 명령을 사용합니다.
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   빌드 스크립트를 실행하는 경우 솔루션을 프로비전했을 때 사용한 것과 동일한 Azure 계정, 구독, 지역 및 Active Directory 인스턴스를 사용해야 합니다.

## <a name="see-your-logic-app-in-action"></a>작업에서 논리 앱 참조
솔루션을 프로비전할 때 미리 구성된 원격 모니터링 솔루션에는 기본적으로 두 개의 규칙이 설정되어 있습니다. 두 규칙 모두 **SampleDevice001** 장치에 있습니다.

* 온도 > 38.00
* 습도 > 48.00

온도 규칙은 **경보 발생** 작업을 트리거하고 습도 규칙은 **SendMessage** 작업을 트리거합니다. 두 작업에 동일한 URL을 사용한다고 가정하면 **ActionRepository** 클래스인 논리 앱은 어느 한쪽의 규칙에 대해 트리거합니다. 두 규칙은 SendGrid를 사용하여 경고의 세부 정보가 포함된 **받는 사람** 주소에 전자 메일을 보냅니다.

> [!NOTE]
> 임계값에 도달할 때마다 논리 앱이 계속 트리거됩니다. 불필요한 전자 메일을 피하려면 솔루션 포털에서 규칙을 사용하지 않도록 설정하거나 [Azure Portal][lnk-azureportal]에서 논리 앱을 사용하지 않도록 설정할 수 있습니다.
> 
> 

전자 메일을 받는 것 외에도 논리 앱이 포털에서 실행될 때 다음을 확인할 수 있습니다.

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>다음 단계
지금까지 논리 앱을 사용하여 미리 구성된 솔루션을 비즈니스 프로세스에 연결했으므로 미리 구성된 솔루션을 사용자 지정하기 위한 옵션에 대해 자세히 알아보겠습니다.

* [원격 모니터링 사전 구성 솔루션으로 동적 원격 분석 사용][lnk-dynamic]
* [미리 구성된 원격 모니터링 솔루션의 장치 정보 메타데이터][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
