---
title: Azure IoT Hub Device Provisioning Service를 사용하여 장치 프로비전 | Microsoft Docs
description: Azure IoT Hub Device Provisioning Service를 사용하여 단일 IoT Hub에 장치를 프로비전
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/12/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9f151a8fbcdc20124467a1db290f6a05f574e4fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service를 사용하여 IoT Hub에 장치를 프로비전

이전 자습서에서 Device Provisioning Service에 연결하기 위해 장치를 설정하는 방법을 배웠습니다. 이 자습서에서는 이 서비스를 사용하여 자동 프로비전 및 **_등록 목록_** 을 사용하는 단일 IoT Hub에 장치를 프로비전하는 방법을 배웁니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 장치 등록
> * 장치 시작
> * 장치가 등록되어 있는지 확인

## <a name="prerequisites"></a>필수 조건

계속 진행하려면 [Azure IoT Hub Device Provisioning Service를 사용하여 프로비전하도록 장치를 설정](./tutorial-set-up-device.md) 자습서의 설명에 따라 장치를 구성해야 합니다.

자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](concepts-auto-provisioning.md)을 검토하세요.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>장치 등록

이 단계에서는 Device Provisioning Service에 장치의 고유 보안 아티팩트를 추가하는 것이 포함됩니다. 이러한 보안 아티팩트는 다음과 같은 장치의 [증명 메커니즘](concepts-device.md#attestation-mechanism)을 기반으로 합니다.

- TPM 기반 장치의 경우 다음과 같은 항목이 필요합니다.
    - TPM 칩 제조업체에서 얻은 각 TPM 칩 또는 시뮬레이션에 고유한 *인증 키*.  자세한 정보는 [TPM 인증 키 이해](https://technet.microsoft.com/library/cc770443.aspx)를 읽어보세요.
    - 네임스페이스/범위에서 장치를 고유하게 식별하는 데 사용되는 *등록 ID*입니다. 이 ID는 장치 ID와 같을 수도 있고 다를 수도 있습니다. ID는 모든 장치에 필수입니다. TPM 기반 장치의 경우 등록 ID는 TPM 인증 키의 SHA-256 해시와 같이 TPM 자체에서 파생될 수도 있습니다.

    [![포털에서 TPM에 대한 등록 정보](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- X.509 기반 장치의 경우 다음과 같은 항목이 필요합니다.
    - [X.509에 발급된 인증서](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) 칩 또는 시뮬레이션입니다. 형식은 *.pem* 또는 *.cer* 파일 중 하나입니다. 개별 등록은 X.509 시스템에 대한 장치별 *서명자 인증서*를, 등록 그룹의 경우 *루트 인증서*를 사용해야 합니다. 

    [![포털에 X.509 증명에 대한 개별 등록 추가](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Device Provisioning Service에 장치를 등록하는 방법은 두 가지가 있습니다.

- **등록 그룹** 특정 증명 메커니즘을 공유하는 장치의 그룹을 나타냅니다. 원하는 초기 구성을 공유하는 장치 수가 많은 경우 또는 장치가 모두 동일한 테넌트로 이동하는 경우 등록 그룹을 사용하는 것이 좋습니다.

    [![포털에 X.509 증명에 대한 그룹 등록 추가](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **개별 등록** Device Provisioning Service에 등록할 수도 있는 단일 장치에 대한 항목을 나타냅니다. 개별 등록은 증명 메커니즘으로 x509 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 고유한 초기 구성이 필요한 장치 및 증명 메커니즘으로 TPM 또는 가상 TPM을 통해 SAS 토큰만을 사용할 수 있는 장치의 경우 개별 등록을 사용하는 것이 좋습니다. 개별 등록에는 원하는 IoT Hub 장치 ID가 지정될 수 있습니다.

이제 장치의 증명 메커니즘에 따라 필요한 보안 아티팩트를 사용하여 장치를 Device Provisioning Service 인스턴스에 등록합니다. 

1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다.

2. Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭 또는 **등록 그룹** 탭 중 하나를 장치 설정에 따라 탭합니다. 위쪽에 있는 **추가** 단추를 클릭합니다. **TPM** 또는 **X.509**를 ID 증명 *메커니즘*으로 선택하고, 이전에 설명된 대로 적절한 보안 아티팩트를 입력합니다. 새 **IoT Hub 장치 ID**를 입력할 수도 있습니다. 완료되면 **저장** 단추를 클릭합니다. 

3. 장치를 성공적으로 등록하면 포털에 다음과 같은 화면이 표시됩니다.

    ![포털에서 성공적인 TPM 등록](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

등록 후 프로비전 서비스는 향후 특정한 시점에 이러한 장치가 부팅 및 연결될 때까지 대기합니다. 장치를 처음으로 부팅하는 경우 클라이언트 SDK 라이브러리는 칩과 상호 작용하여 장치에서 보안 아티팩트를 추출하고, Device Provisioning Service에 등록을 확인합니다. 

## <a name="start-the-device"></a>장치 시작

이 시점에서 다음 설정이 장치 등록을 위해 준비되었습니다.

1. 장치 또는 장치 그룹이 Device Provisioning Service에 등록되었습니다. 
2. 장치의 증명 메커니즘이 구성되었으며 Device Provisioning Service 클라이언트 SDK를 사용하여 응용 프로그램을 통해 장치에 액세스할 수 있습니다.

장치에서 Device Provisioning Service를 통해 등록을 시작하도록 클라이언트 응용 프로그램을 허용합니다.  

## <a name="verify-the-device-is-registered"></a>장치가 등록되어 있는지 확인

장치가 부팅되면 다음 작업이 이루어져야 합니다. 자세한 내용은 TPM 시뮬레이터 샘플 응용 프로그램 [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c)을 참조하세요. 

1. 장치가 Device Provisioning Service에 등록 요청을 보냅니다.
2. TPM 장치의 경우 Device Provisioning Service에서 장치가 응답하는 등록 챌린지를 다시 보냅니다. 
3. 등록에 성공하면 Device Provisioning Service는 IoT Hub URI, 장치 ID 및 암호화된 키를 장치로 다시 보냅니다. 
4. 그러면 장치의 IoT Hub 클라이언트 응용 프로그램이 사용자 허브에 연결됩니다. 
5. 허브에 성공적으로 연결되면 장치가 IoT Hub의 **Device Explorer**에 나타납니다. 

    ![포털에서 허브에 연결 성공](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 장치 등록
> * 장치 시작
> * 장치가 등록되어 있는지 확인

부하가 분산된 허브 간 여러 장치를 프로비전하는 방법에 알아보려면 다음 자습서를 진행합니다. 

> [!div class="nextstepaction"]
> [부하가 분산된 IoT Hub 간 장치 프로비전](./tutorial-provision-multiple-hubs.md)
