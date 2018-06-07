---
title: Azure IoT Hub Device Provisioning 서비스에서 장치 등록을 해제하는 방법
description: Azure IoT Hub Device Provisioning 서비스를 통한 프로비전을 방지하기 위해 장치 등록을 해제하는 방법
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr;bryanla
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 50074eaecacf603d2bc6170183fd632b4a1ab2d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning 서비스에서 장치 등록을 해제하는 방법

IoT 솔루션과 같이 세간의 이목을 끄는 프로필 시스템에서는 적절한 장치 자격 증명 관리가 매우 중요합니다. 이러한 시스템에 대해서는 SAS(공유 액세스 서명) 토큰 또는 X.509 인증서 등, 자격 증명이 손상될 수 있는 경우 장치 액세스를 취소하는 방법을 명확하게 계획하는 것이 가장 좋습니다. 

Device Provisioning Server에 등록하면 장치가 [자동 프로비전](concepts-auto-provisioning.md)될 수 있습니다. 프로비전된 장치는 IoT Hub에 등록된 장치로, 초기 [장치 쌍](~/articles/iot-hub/iot-hub-devguide-device-twins.md) 상태를 수신하고 원격 분석 데이터 보고를 시작할 수 있습니다. 이 문서에서는 프로비전 서비스 인스턴스에서 장치 등록을 해제하여 앞으로 다시 프로비전되지 못하게 하는 방법을 설명합니다.

> [!NOTE] 
> 액세스를 취소한 장치의 다시 시도 정책을 알고 있어야 합니다. 예를 들어 무제한 다시 시도 정책이 적용된 장치는 지속적으로 프로비전 서비스 등록을 시도할 수 있습니다. 이 경우 서비스 리소스를 소비하며 성능에도 영향을 미칠 수 있습니다.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>개별 등록 항목을 사용하여 장치를 차단 목록에 등록

개별 등록은 단일 장치에 적용되며, X.509 인증서 또는 SAS 토큰을 증명 메커니즘으로 사용할 수 있습니다(실제 또는 가상 TPM에서). SAS 토큰을 증명 메커니즘으로 사용하는 장치는 개별 등록을 통해서만 프로비전할 수 있습니다. 개별 등록이 있는 장치를 차단 목록에 추가하려면 해당 등록 항목을 사용하지 않도록 설정하거나 삭제할 수 있습니다. 

해당 등록 항목을 사용하지 않도록 설정하여 장치를 일시적으로 차단 목록에 추가하려면  

1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. 리소스 목록에서 장치를 차단 목록에 추가하려는 프로비전 서비스를 선택합니다.
3. 프로비전 서비스에서 **등록 관리**를 클릭한 후 **개별 등록** 탭을 선택합니다.
4. 차단 목록에 추가하려는 장치의 등록 항목을 선택합니다. 
5. 아래로 스크롤한 후 **항목 사용** 스위치에서 **사용 안 함**을 선택한 다음, **저장**을 선택합니다.  

   [![포털에서 개별 등록 항목 사용 해제](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png#lightbox)  

해당 등록 항목을 삭제하여 장치를 영구적으로 차단 목록에 추가하려면 

1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. 리소스 목록에서 장치를 차단 목록에 추가하려는 프로비전 서비스를 선택합니다.
3. 프로비전 서비스에서 **등록 관리**를 클릭한 후 **개별 등록** 탭을 선택합니다.
4. 차단 목록에 추가하려는 장치의 등록 항목 옆에 있는 확인란을 선택합니다. 
5. 창 위쪽에서 **삭제**를 선택한 다음, **예**를 선택하여 등록을 제거할 것인지 확인합니다. 

   ![포털에서 개별 등록 항목 삭제](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
절차가 완료되면 개별 등록 목록에서 항목이 제거되어야 합니다.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>등록 그룹을 사용하여 차단 목록에 X.509 중간 또는 루트 CA 인증서 추가

X.509 인증서는 일반적으로 신뢰할 수 있는 인증서 체인에 정렬됩니다. 체인의 어느 단계에서 인증서가 손상되면 트러스트가 끊어집니다. 장치 프로비전 서비스가 해당 인증서를 포함하는 모든 체인에서 장치 다운스트림을 프로비전하지 않도록 해당 인증서를 차단 목록에 추가해야 합니다. X.509 인증서 및 프로비전 서비스를 통해 사용되는 방법에 대한 자세한 내용은 [X.509 인증서](./concepts-security.md#x509-certificates)를 참조하세요. 

등록 그룹은 동일한 중간 또는 루트 CA에서 서명한 X.509 인증서의 일반적인 증명 메커니즘을 공유하는 장치에 대한 항목입니다. 등록 그룹 항목은 중간 또는 루트 CA와 연결된 X.509 인증서로 구성됩니다. 또한 해당 인증서가 인증서 체인에 있는 장치에서 공유하는 모든 구성 값(예: 쌍 상태 및 IoT 허브 연결)으로 구성됩니다. 인증서를 차단 목록에 추가하기 위해 등록 그룹을 사용하지 않도록 설정하거나 삭제할 수 있습니다.

해당 등록 그룹을 사용하지 않도록 설정하여 인증서를 일시적으로 차단 목록에 추가하려면  

1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. 리소스 목록에서 서명 인증서를 차단 목록에 추가하려는 프로비전 서비스를 선택합니다.
3. 프로비전 서비스에서 **등록 관리**를 선택한 후 **등록 그룹** 탭을 선택합니다.
4. 차단 목록에 추가하려는 인증서를 사용하는 등록 그룹을 선택합니다.
5. **항목 사용** 스위치에서 **사용 안 함**을 선택한 다음, **저장**을 선택합니다.  

   ![포털에서 등록 그룹 항목 사용 해제](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
해당 등록 그룹을 삭제하여 인증서를 영구적으로 차단 목록에 추가하려면 

1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. 리소스 목록에서 장치를 차단 목록에 추가하려는 프로비전 서비스를 선택합니다.
3. 프로비전 서비스에서 **등록 관리**를 선택한 후 **등록 그룹** 탭을 선택합니다.
4. 차단 목록에 추가하려는 인증서의 등록 그룹 옆에 있는 확인란을 선택합니다. 
5. 창 위쪽에서 **삭제**를 선택한 다음, **예**를 선택하여 등록 그룹을 제거할 것인지 확인합니다. 

   ![포털에서 등록 그룹 항목 삭제](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

절차가 완료되면 등록 그룹 목록에서 항목이 제거되어야 합니다.  

> [!NOTE]
> 인증서에 대한 등록 그룹을 삭제할 때 해당 인증서 체인에서 루트 인증서 또는 더 높은 수준의 다른 중간 인증서에 대해 사용하도록 설정된 등록 그룹이 있으면 이 인증서 체인에 해당 인증서가 있는 장치는 계속 등록할 수 있습니다.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>등록 그룹의 차단 목록에 특정 장치 추가

X.509 증명 메커니즘을 구현하는 장치는 장치의 인증서 체인과 개인 키를 사용하여 인증합니다. 장치가 장치 프로비전 서비스에 연결하여 인증할 때는 먼저 서비스가 장치의 자격 증명에 부합하는 개별 등록을 찾습니다. 그런 다음, 서비스가 등록 그룹을 검색하여 장치의 프로비전 가능 여부를 판단합니다. 서비스에서 장치에 대해 개별 등록이 사용하지 않도록 설정된 것을 확인하면 장치 연결을 차단합니다. 장치의 인증서 체인에 중간 또는 루트 CA에 대해 사용되는 등록 그룹이 있더라도 서비스에서는 연결을 차단합니다. 

등록 그룹의 개별 장치를 차단 목록에 추가하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스**를 클릭합니다.
2. 리소스 목록에서 차단 목록에 추가하려는 장치에 대한 등록 그룹이 포함된 프로비전 서비스를 선택합니다.
3. 프로비전 서비스에서 **등록 관리**를 클릭한 후 **개별 등록** 탭을 선택합니다.
4. 위쪽의 **추가** 단추를 선택합니다. 
5. **X.509**를 장치에 대한 증명 메커니즘으로 선택하고 장치 인증서를 업로드합니다. 이것은 장치에 설치된 서명된 최종 엔터티 인증서입니다. 장치가 이 항목을 사용하여 인증을 위한 인증서를 생성합니다.
6. **IoT Hub 장치 ID**에 장치의 ID를 입력합니다. 
7. **항목 사용** 스위치에서 **사용 안 함**을 선택한 다음, **저장**을 선택합니다. 

    [![사용하지 않도록 설정된 개인 등록 항목을 사용하여 포털에서 장치의그룹 등록 해제](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

등록이 성공적으로 만들어지면 장치가 **개별 등록** 탭 아래에 표시됩니다.

## <a name="next-steps"></a>다음 단계

등록 해제는 대규모 프로비전 해제 프로세스의 일부이기도 합니다. 장치 프로비전 해제에는 프로비전 서비스에서의 등록 해제와 IoT Hub에서의 등록 취소가 둘 다 포함됩니다. 전체 프로세스에 대한 자세한 내용은 [이전에 자동으로 프로비전된 장치의 프로비전을 해제하는 방법](how-to-unprovision-devices.md)을 참조하세요. 

