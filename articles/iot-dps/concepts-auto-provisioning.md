---
title: IoT Hub Device Provisioning Service - 자동 프로비전 개념
description: 이 문서에서는 IoT Device Provisioning Service, IoT Hub 및 클라이언트 SDK를 사용하여 장치 자동 프로비전 단계에 대한 개념적 개요를 제공합니다.
services: iot-dps
keywords: ''
author: BryanLa
ms.author: bryanla
ms.date: 03/27/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: e743f40a1f8ff71fe93f14217b410df348d9903d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="auto-provisioning-concepts"></a>자동 프로비전 개념

[개요](about-iot-dps.md)에 설명된 대로, Device Provisioning Service는 사용자 개입 없이, IoT Hub에 대한 Just-In-Time 장치 프로비전을 허용하는 도우미 서비스입니다. 성공적으로 프로비전한 후 장치는 지정된 IoT Hub에 직접 연결됩니다. 이 프로세스를 자동 프로비전이라고 하며, 장치에 대해 기본 등록 및 초기 구성 환경을 제공합니다.

## <a name="overview"></a>개요

Azure IoT 자동 프로비전은 다음 세 단계로 구분할 수 있습니다.

1. **서비스 구성** - Azure IoT Hub 및 IoT Hub Device Provisioning Service 인스턴스를 한 번 구성하고, 설정한 후 사이에 연결을 만듭니다.

   > [!NOTE]
   > IoT 솔루션 크기에 관계 없이, 수백만 개의 장치를 지원하려는 경우에도 **일회성 구성**이 수행됩니다.

2. **장치 등록** - Device Provisioning Service 인스턴스가 향후 등록할 장치를 인식하도록 하는 프로세스입니다. [등록](concepts-service.md#enrollment)은 프로비전 서비스에서 단일 장치의 경우는 "개별 등록"으로, 여러 장치의 경우는 "그룹 등록"으로 장치 ID 정보를 구성하여 수행됩니다. ID는 장치가 사용하도록 디자인된 [증명 메커니즘](concepts-security.md#attestation-mechanism)을 기준으로 합니다. 이 메커니즘을 사용하여 프로비전 서비스는 등록 중에 장치의 신뢰성을 증명할 수 있습니다.

   - **TPM**: "개별 등록"으로 구성됩니다. 장치 ID는 TPM 등록 ID 및 공용 인증 키를 기준으로 합니다. TPM이 [사양]((https://trustedcomputinggroup.org/work-groups/trusted-platform-module/))이라고 간주할 경우, 서비스는 TPM 구현(하드웨어 또는 소프트웨어)에 관계없이 사양에 따라서만 증명합니다. TPM 기반 증명에 대한 자세한 내용은 [장치 프로비전: TPM으로 ID 증명](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/)을 참조하세요. 

   - **X509**: "개별 등록" 또는 "그룹 등록"으로 구성됩니다. 장치 ID는 .pem 또는 .cer 파일로 등록에 업로드되는 X.509 디지털 인증서를 기준으로 합니다.

   > [!IMPORTANT]  
   > Device Provisioning Service에 대한 필수 구성 요소는 아니지만, 장치가 HSM(하드웨어 보안 모듈)을 사용하여 키 및 X.509 인증서와 같은 중요한 장치 ID 정보를 저장하도록 하는 것이 좋습니다.

3. **장치 등록 및 구성** - 등록 소프트웨어에 의한 부팅 시 시작됩니다. 이 소프트웨어는 장치 및 증명 메커니즘에 적합한 Device Provisioning Service 클라이언트 SDK를 사용하여 빌드됩니다. 이 소프트웨어는 장치의 인증과 IoT Hub에서의 후속 등록을 위해 프로비전 서비스에 대한 연결을 설정합니다. 등록이 성공적으로 수행되면 장치에는 IoT Hub 고유 장치 ID 및 연결 정보가 제공되므로, 초기 구성을 끌어오고 원격 분석 프로세스를 시작할 수 있습니다. 프로덕션 환경에서 이 단계는 이전 두 단계가 진행되고 몇 주 또는 몇 달 후에 발생할 수 있습니다.

## <a name="roles-and-operations"></a>역할 및 작업

이전 섹션에서 설명된 단계는 제조 시간, 배송, 세관 프로세스 등의 프로덕션 현실 때문에 몇 주 또는 몇 개월이 걸릴 수 있습니다. 또한 관련된 다양한 엔터티를 고려한다면, 여러 역할에 걸쳐 작업이 진행될 수도 있습니다. 이 섹션에서는 각 단계와 관련된 다양한 역할 및 작업을 면밀히 검토하고 시퀀스 다이어그램에서의 흐름을 보여 줍니다. 

자동 프로비전의 경우도 증명 메커니즘의 설정과 관련해서 장치 제조업체에 요구 조건이 발생합니다. 제조 작업도 자동 프로비전 단계의 타이밍과는 별도로 진행될 수 있으며, 특히 자동 프로비전이 이미 설정된 후에 새 장치가 조달된 경우에 이러한 현상이 두드러집니다.

왼쪽의 목차에는 실무 환경을 통해 자동 프로비전을 설명하기 위해 일련의 빠른 시작 과정이 제공됩니다. 학습 프로세스를 용이하게 하고 간소화하기 위해 소프트웨어를 사용해서 등록을 위한 실제 장치를 시뮬레이트합니다. 일부 빠른 시작에서는 시뮬레이션 방식 때문에 존재하지 않는 역할에 대한 작업을 비롯하여 여러 역할에 대한 작업을 이행해야 합니다.

| 역할 | 작업 | 설명 | 
|------| --------- | ------------| 
| 제조업체 | ID 및 등록 URL 인코딩 | 사용되는 증명 메커니즘에 따라, 제조업체는 장치 ID 정보 및 Device Provisioning Service 등록 URL을 인코딩해야 합니다.<br><br>**빠른 시작**: 장치가 시뮬레이트되므로 제조업체 역할은 없습니다. 샘플 등록 응용 프로그램을 코딩하는 데 사용되는 이러한 정보를 얻는 방법에 대한 자세한 내용은 개발자 역할을 참조하세요. |  
| | 장치 ID 제공 | 장치 ID 정보의 작성자인 제조업체는 작업자(또는 지정된 에이전트)에게 해당 정보를 전달하거나, API를 통해 Device Provisioning Service에 직접 등록해야 합니다.<br><br>**빠른 시작**: 장치가 시뮬레이트되므로 제조업체 역할은 없습니다. Device Provisioning Service 인스턴스에서 시뮬레이트된 장치를 등록하는 데 사용되는 장치 ID를 얻는 방법에 대한 자세한 내용은 작업자 역할을 참조하세요. | 
| 연산자 | 자동 프로비전 구성 | 이 작업은 자동 프로비전의 첫 번째 단계에 해당합니다.<br><br>**빠른 시작**: 작업자 역할을 수행하여 Azure 구독에서 Device Provisioning Service 및 IoT Hub 인스턴스를 구성합니다. | 자동 프로비전 설정 |
|  | 장치 ID 등록 | 이 작업은 자동 프로비전의 두 번째 단계에 해당합니다.<br><br>**빠른 시작**: 작업자 역할을 수행하여 Device Provisioning Service 인스턴스에서 시뮬레이트된 장치를 등록합니다. 장치 ID는 빠른 시작에서 시뮬레이트된 증명 방법에 따라 결정됩니다(TPM 또는 X.509). 증명 세부 정보에 대해서는 개발자 역할을 참조하세요. | 
| Device Provisioning Service,<br>IoT 허브 | \<모든 작업\> | 물리적 장치를 사용하는 프로덕션 구현과 시뮬레이트된 장치를 통한 빠른 시작에서, 이러한 역할은 Azure 구독에서 구성하는 IoT 서비스를 통해 이행됩니다. IoT 서비스는 물리적 장치 및 시뮬레이트된 장치의 프로비전에서 크게 다르지 않으므로 역할/작업은 정확히 동일하게 작동합니다. | 
| Developer | 등록 소프트웨어 빌드/배포 | 이 작업은 자동 프로비전의 세 번째 단계에 해당합니다. 개발자는 적절한 SDK를 사용하여 등록 소프트웨어를 빌드하고 장치에 배포하는 일을 담당 합니다.<br><br>**빠른 시작**: 빌드하는 샘플 등록 응용 프로그램은 사용자가 선택한 플랫폼/언어에 대해 워크스테이션에서 실행되는 실제 장치를 시뮬레이트합니다(실제 장치에 배포되지 않음). 등록 응용 프로그램은 물리적 장치에 배포된 경우와 동일한 작업을 수행합니다. Device Provisioning Service의 등록 URL 및 “ID 범위" 외에 증명 방법(TPM 또는 X.509 인증서)을 지정합니다. 장치 ID는 사용자가 지정한 방법에 따라, 런타임에 SDK 증명 논리에 의해 결정됩니다. <ul><li>**TPM 증명** - 개발 워크스테이션에서 [TPM 시뮬레이터 응용 프로그램](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator)이 실행됩니다. 일단 실행되면, 장치 ID를 등록하는 데 사용할 TPM의 "인증 키" 및 "등록 ID"를 별도 응용 프로그램을 통해 추출합니다. 또한 SDK 증명 논리는 등록 중에 이 시뮬레이터를 사용하여 인증 및 등록 확인을 위한 서명된 SAS 토큰을 제공합니다.</li><li>**X509 증명** - 도구를 사용하 여 [인증서를 생성](how-to-use-sdk-tools.md#x509-certificate-generator)합니다. 일단 생성되면, 등록에 사용해야 하는 인증서 파일을 만듭니다. 또한 SDK 증명 논리는 등록 중에 이 인증서를 사용하여 인증 및 등록 확인을 위해 제공됩니다.</li></ul> | 
| 장치 | 부팅 및 등록 | 이 작업은 자동 프로비전의 세 번째 단계에 해당하며, 개발자가 빌드한 장치 등록 소프트웨어에 의해 이행됩니다. 자세한 내용은 개발자 역할을 참조하세요. 최초 부팅 시: <ol><li>응용 프로그램은 개발 동안 지정한 전역 URL 및 서비스 "ID 범위"를 기준으로 Device Provisioning Service 인스턴스에 연결합니다.</li><li>일단 연결되면, 장치는 등록 중에 지정된 증명 방법 및 ID에 따라 인증됩니다.</li><li>일단 인증되면, 장치는 프로비전 서비스 인스턴스에 의해 지정된 IoT Hub 인스턴스에 등록됩니다.</li><li>등록이 성공적으로 수행되면 고유한 장치 ID 및 IoT Hub 끝점이 IoT Hub와의 통신을 위해 등록 응용 프로그램으로 반환됩니다.</li><li> 여기에서 장치는 구성을 위해 초기 [장치 쌍](~/articles/iot-hub/iot-hub-devguide-device-twins.md) 상태를 풀다운하고, 원격 분석 데이터 보고 프로세스를 시작할 수 있습니다.</li></ol>**빠른 시작**: 장치가 시뮬레이트된 후에 등록 소프트웨어가 개발용 워크스테이션에서 실행됩니다.| 

다음 다이어그램에서는 장치 자동 프로비전 동안 작업의 역할 및 시퀀스를 요약해서 보여 줍니다.
<br><br>
![장치에 대한 자동 프로비전 시퀀스](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png) 

> [!NOTE]
> 필요에 따라, 제조업체가 작업자를 통해서가 아니라 Device Provisioning Service API를 사용하여 "장치 ID 등록" 작업을 수행할 수도 있습니다. 이 시퀀스에 대한 자세한 내용은 [Azure IoT를 사용한 제로 터치 장치 등록 비디오](https://myignite.microsoft.com/sessions/55087)를 참조하세요(41:00 표시부터 시작).

## <a name="next-steps"></a>다음 단계

해당 자동 프로비전 빠른 시작을 통해 작업을 진행하게 되므로 참조 위치로 이 문서에 책갈피를 지정하면 유용할 수 있습니다. 

먼저 관리 도구 기본 설정에 가장 적합하고 "서비스 구성" 단계를 진행하는 "자동 프로비전 설정" 빠른 시작을 완료합니다.

- [Azure CLI를 사용하여 자동 프로비전 설정](quick-setup-auto-provision-cli.md)
- [Azure Portal을 사용하여 자동 프로비전 설정](quick-setup-auto-provision.md)
- [Resource Manager 템플릿을 사용하여 자동 프로비전 설정](quick-setup-auto-provision-rm.md)

그런 후 장치 증명 메커니즘 및 Device Provisioning Service SDK/언어 기본 설정에 맞는 "시뮬레이트된 장치 자동 프로비전" 빠른 시작을 계속 진행합니다. 이 빠른 시작에서는 "장치 등록" 및 "장치 등록 및 구성" 단계를 진행합니다. 

|  | 시뮬레이트된 장치 증명 메커니즘 | 빠른 시작 SDK/언어 |  |
|--|--|--|--|
|  | TPM(신뢰할 수 있는 플랫폼 모듈) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509 인증서 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




