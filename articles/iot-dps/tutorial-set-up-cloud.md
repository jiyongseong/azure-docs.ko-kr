---
title: "포털에서 Azure IoT Hub Device Provisioning Service를 위한 클라우드 설정 | Microsoft Docs"
description: "Azure Portal에서 IoT Hub 자동 장치 프로비전"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 247c2155943d651c3be7791571522b652cf63483
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service를 사용하여 장치 프로비전을 위한 클라우드 리소스 구성

이 자습서에서는 IoT Hub Device Provisioning Service를 사용하여 자동 장치 프로비전을 위해 클라우드를 설정하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 IoT Hub Device Provisioning Service 만들기 및 ID 범위 가져오기
> * IoT Hub 만들기
> * Device Provisioning Service에 IoT Hub 연결
> * Device Provisioning Service에서 할당 정책 설정

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Device Provisioning Service 인스턴스 만들기 및 ID 범위 가져오기

다음 단계를 수행하여 새 Device Provisioning Service 인스턴스를 만듭니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. 검색 상자에 **장치 프로비전**을 입력합니다. 
3. **IoT Hub Device Provisioning Service**를 클릭합니다.
4. 다음 정보로 **IoT Hub Device Provisioning Service** 양식을 채웁니다.
    
   | 설정       | 제안 값 | 설명 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name** | 고유한 이름 | -- | 
   | **구독** | 사용자의 구독  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   | **리소스 그룹** | myResourceGroup | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **위치**: | 모든 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |   

   ![포털에서 DPS에 대한 기본 정보 입력](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. **만들기**를 클릭합니다.
6. *ID 범위*는 등록 ID를 식별하는 데 사용되고 등록 ID가 고유함을 보장합니다. 이 값을 얻으려면 **개요**를 클릭하여 Device Provisioning Service의 **기본 정보** 페이지를 엽니다. **ID 범위** 값을 나중에 사용할 임시 위치에 복사합니다.
7. 또한 **서비스 끝점** 값을 적어 놓거나 나중에 사용할 임시 위치에 복사합니다. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 IoT Hub 연결 문자열을 갖게 되었습니다.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>IoT Hub에 Device Provisioning Service 연결

다음 단계는 IoT Hub Device Provisioning Service가 장치를 해당 허브에 연결할 수 있도록 Device Provisioning Service와 IoT Hub를 연결하는 것입니다. 서비스는 장치를 Device Provisioning Service에 연결된 IoT Hub에만 프로비전할 수 있습니다. 다음 단계를 수행합니다.

1. **모든 리소스** 페이지에서 이전에 만든 Device Provisioning Service 인스턴스를 클릭합니다.
2. Device Provisioning Service 페이지에서 **연결된 IoT Hub**를 클릭합니다.
3. **추가**를 클릭합니다.
4. **IoT Hub에 링크 추가** 페이지에서 라디오 단추를 사용하여 연결된 IoT Hub가 현재 구독 또는 다른 구독 중 어디에 있는지를 지정합니다. 그런 다음 IoT Hub 이름을 **IoT Hub** 상자에서 선택합니다.
5. **저장**을 클릭합니다.

   ![포털에서 DPS에 연결할 허브 이름 연결](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Device Provisioning Service에서 할당 정책 설정

할당 정책은 IoT Hub에 장치를 할당하는 방법을 결정하는 IoT Hub Device Provisioning Service 설정입니다. 세 가지의 지원되는 할당 정책이 있습니다. 

1. **최소 대기 시간**: 장치가 대기 시간이 가장 낮은 허브를 기준으로 IoT Hub에 프로비전됩니다.
2. **균등 가중치 배포**(기본값): 연결된 IoT Hub들은 동일하게 해당 허브에 프로비전된 장치를 갖게 됩니다. 기본 설정입니다. 장치를 단 하나의 IoT Hub에 프로비전하려는 경우 이 설정을 유지할 수 있습니다. 
3. **등록 목록을 통한 고정 구성**: 등록 목록에서 원하는 IoT Hub의 사양을 Device Provisioning Service 수준 할당 정책보다 우선합니다.

할당 정책을 설정하려면 Device Provisioning Service 페이지에서 **할당 정책 관리**를 클릭합니다. 할당 정책이 **균등 가중치 배포**(기본값)로 설정되어 있는지 확인합니다. 변경하려면 변경 후 **저장**을 클릭합니다.

![할당 정책 관리](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 자습서는 이 자습서를 기반으로 작성됩니다. 다음 빠른 시작 또는 자습서를 사용하여 계속하려는 경우 이 자습서에서 만든 리소스를 정리하지 마십시오. 계속하지 않으려는 경우 다음 단계에 따라 이 자습서에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 IoT Hub Device Provisioning Service 인스턴스를 선택합니다. **모든 리소스** 페이지 위쪽에서 **삭제**를 클릭합니다.  
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. **모든 리소스** 페이지 위쪽에서 **삭제**를 클릭합니다.
 
## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 IoT Hub Device Provisioning Service 만들기 및 ID 범위 가져오기
> * IoT Hub 만들기
> * Device Provisioning Service에 IoT Hub 연결
> * Device Provisioning Service에서 할당 정책 설정

프로비전을 위해 장치를 설정하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [프로비전을 위한 장치 설정](tutorial-set-up-device.md)
