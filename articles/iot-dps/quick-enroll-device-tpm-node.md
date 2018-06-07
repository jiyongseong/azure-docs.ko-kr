---
title: Node.js를 사용하여 Azure Device Provisioning Service에 TPM 장치 등록 | Microsoft Docs
description: Azure 빠른 시작 - Node.js 서비스 SDK를 사용하여 Azure IoT Hub Device Provisioning Service에 TPM 장치 등록
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 143aaca5671b1c9e2ae9c2d187aed64af15528ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Node.js 서비스 SDK를 사용하여 IoT Hub Device Provisioning Service에 TPM 장치 등록

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


다음 단계에서는 [Node.js 서비스 SDK](https://github.com/Azure/azure-iot-sdk-node) 및 샘플 Node.js 응용 프로그램을 사용하여 Azure IoT Hub Device Provisioning Service에서 TPM 장치에 대한 개별 등록을 프로그래밍 방식으로 만드는 방법을 보여 줍니다. 필요에 따라 이 개별 등록 항목을 사용하여 시뮬레이션된 TPM 장치를 프로비전 서비스에 등록할 수 있습니다. 이러한 단계는 Windows 및 Linux 컴퓨터 모두에서 작동하지만, 이 문서의 목적을 위해 Windows 개발 컴퓨터를 사용합니다.

## <a name="prerequisites"></a>필수 조건

- 계속 진행하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 
-  컴퓨터에 [Node.js v4.0 이상](https://nodejs.org)이 설치되어 있는지 확인합니다.
- 이 빠른 시작의 끝에서 시뮬레이션된 장치를 등록하려면, 장치에 대한 인증 키를 가져오는 단계까지 [시뮬레이션된 장치 만들기 및 프로비전](quick-create-simulated-device.md)의 단계를 수행합니다. 나중에 이 빠른 시작에서 사용할 수 있도록 인증 키를 적어 둡니다. **Azure Portal을 사용하여 개별 등록을 만드는 단계는 수행하지 마세요.**
 
## <a name="create-the-individual-enrollment-sample"></a>개별 등록 샘플 만들기 

 
1. 작업 폴더의 명령 창에서 다음을 실행합니다.
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. 텍스트 편집기를 사용하여 작업 폴더에 **create_individual_enrollment.js** 파일을 만듭니다. 다음 코드를 파일에 추가하고 저장합니다.

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ````

## <a name="run-the-individual-enrollment-sample"></a>개별 등록 샘플 실행
  
1. 샘플을 실행하려면 프로비전 서비스에 대한 연결 문자열이 필요합니다. 
    1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다. 
    2. **공유 액세스 정책**을 클릭한 다음, 해당 속성을 열기 위해 사용하려는 액세스 정책을 클릭합니다. **액세스 정책** 창에서 기본 키 연결 문자열을 복사하고 적어 둡니다. 

    ![포털에서 프로비전 서비스 연결 문자열 가져오기](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. 장치에 대한 인증 키도 필요합니다. [시뮬레이션된 장치 만들기 및 프로비전](quick-create-simulated-device.md) 빠른 시작을 수행하여 시뮬레이션된 TPM 장치를 만든 경우 해당 장치에 대해 만든 키를 사용합니다. 그렇지 않은 경우 샘플 개별 등록을 만들려면 SDK를 통해 제공되는 다음 인증 키를 사용할 수 있습니다.

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. TPM 장치에 대한 개별 등록을 만들려면 다음 명령을 실행합니다(명령 인수를 따옴표로 묶음).
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. 성공적으로 만들어지면 명령 창에 새 개별 등록에 대한 속성이 표시됩니다.

    ![명령 출력의 등록 속성](./media/quick-enroll-device-tpm-node/output.png) 

4. 개별 등록이 만들어졌는지 확인합니다. Azure Portal의 Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭을 선택하고, 새 등록 항목(*첫 번째*)을 클릭하여 해당 항목에 대한 인증 키와 다른 속성을 확인합니다.

    ![포털의 등록 속성](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
이제 TPM 장치에 대한 개별 등록을 만들었으므로 시뮬레이션된 장치를 등록하려면 [시뮬레이션된 장치 만들기 및 프로비전](quick-create-simulated-device.md)의 나머지 단계를 계속 수행할 수 있습니다. 해당 빠른 시작에서 Azure Portal을 사용하여 개별 등록을 만드는 단계를 건너뜁니다.

## <a name="clean-up-resources"></a>리소스 정리
Node.js 서비스 샘플을 탐색하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 Node.js 샘플 출력 창을 닫습니다.
1. 시뮬레이션된 TPM 장치를 만든 경우 TPM 시뮬레이터 창을 닫습니다.
2. Azure Portal에서 Device Provisioning Service로 이동하고, **등록 관리**를 클릭한 다음, **개별 등록** 탭을 선택합니다. 이 빠른 시작을 사용하여 만든 등록 항목에 대한 *등록 ID*를 선택하고, 블레이드 위쪽의 **삭제** 단추를 클릭합니다. 
 
## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 TPM 장치에 대한 개별 등록 항목을 프로그래밍 방식으로 만들고, 필요에 따라 컴퓨터에 시뮬레이션된 TPM 장치를 만들고, Azure IoT Hub Device Provisioning 서비스를 사용하여 IoT Hub에 이 장치를 프로비전했습니다. 장치 프로비전에 대해 자세히 알아보려면 Azure Portal에서 Device Provisioning Service 설치에 대한 자습서를 살펴보세요. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning 서비스 자습서](./tutorial-set-up-cloud.md)