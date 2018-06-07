---
title: Azure IoT Hub X.509 CA 보안 개요 | Microsoft Docs
description: 개요 - X.509 인증 기관을 사용하여 IoT Hub에서 장치를 인증하는 방법입니다.
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 11f340ebe1719c680cce5f6ad34150d3717c57d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="device-authentication-using-x509-ca-certificates"></a>X.509 CA 인증서를 사용하여 장치 인증

이 문서에서는 IoT Hub를 연결하는 장치를 인증하는 데 X.509 CA(인증 기관) 인증서를 사용하는 방법을 설명합니다.  이 문서에서는 다음에 대해 알아봅니다.

* X.509 CA 인증서를 가져오는 방법
* IoT Hub에 X.509 CA 인증서를 등록하는 방법
* X.509 CA 인증서를 사용하여 장치를 서명하는 방법
* X.509 CA로 서명된 장치를 인증하는 방법

## <a name="overview"></a>개요

X.509 CA 기능에서는 CA(인증 기관)를 사용하여 IoT Hub에 대한 장치 인증을 허용합니다. 이 경우 초기 장치 등록 프로세스와 장치 제조 동안의 공급망 물류 처리가 간편해집니다. 장치 인증에 [X.509 CA 인증서를 사용할 때 얻을 수 있는 가치에 대해서는 이 시나리오 문서를 참조하세요](iot-hub-x509ca-concept.md).  이 시나리오의 문서는 이어지는 단계의 필요성을 잘 설명하므로 먼저 읽는 것이 좋습니다.

## <a name="prerequisite"></a>필수 요소

X.509 CA 기능을 사용하려면 IoT Hub 계정이 필요합니다.  IoT Hub 인스턴스가 아직 없는 경우 [IoT Hub 인스턴스를 만드는 방법을 알아봅니다](iot-hub-csharp-csharp-getstarted.md).

## <a name="how-to-get-an-x509-ca-certificate"></a>X.509 CA 인증서를 가져오는 방법

X.509 CA 인증서는 각 장치에 대한 인증서 체인 맨 위에 있습니다.  원하는 사용 방식에 따라 구입하거나 만들 수 있습니다.

프로덕션 환경에서는 공용 루트 인증 기관에서 X.509 CA 인증서를 구입하는 것이 좋습니다. CA 인증서를 구입하면 장치의 합법성을 보증하는 신뢰할 수 있는 제3자의 역할을 하는 루트 CA의 이점을 얻을 수 있습니다. 이 옵션은 장치를 개방형 IoT 네트워크의 일부로 사용하여 타사 제품 또는 서비스와 상호 작용하도록 하려는 경우에만 고려하세요.

시험적으로 사용하거나 폐쇄형 IoT 네트워크에서 사용하려는 경우에는 자체 서명된 X.509 CA를 만들 수도 있습니다.

X.509 CA 인증서를 획득하는 방법에 관계없이, 항상 해당 개인 키를 비밀리에 유지하고 보호하도록 하세요.  이러한 노력은 X.509 CA 인증에서 신뢰를 구축하는 데 필요합니다. 

이 기능 설명 전체에서 시험적으로 사용할 수 있는 [자체 서명된 CA 인증서를 만드는](iot-hub-security-x509-create-certificates.md#createcerts) 방법을 알아보세요.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>신뢰할 수 있는 인증서 체인에 장치 서명

X.509 CA 인증서의 소유자는 암호화 방식으로 중간 CA에 서명하고, 이 중간 CA는 마지막 중간 CA가 장치에 서명하여 이 프로세스를 종료할 때까지 다른 중간 CA에 서명하는 방식으로 계속 진행될 수 있습니다. 그 결과, 신뢰할 수 있는 인증서 체인으로 알려진 연속된 인증서 체인이 형성됩니다. 실제 상황에서는 장치 등록을 위한 트러스트 위임 방식으로 진행됩니다. 이러한 위임은 관리권의 가변 체인을 암호화 방식으로 설정하고 서명 키의 공유를 방지하기 때문에 유용합니다.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

여기에서는 장치에 서명할 때 완료되는 [인증서 체인 생성](iot-hub-security-x509-create-certificates.md#createcertchain) 방법을 알아봅니다.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>IoT Hub에 X.509 CA 인증서를 등록하는 방법

등록 및 연결 동안 장치를 인증에 사용되는 X.509 CA 인증서를 IoT 허브에 등록합니다.  X.509 CA 인증서 등록은 인증서 파일 업로드 및 소유 증명으로 구성되는 2단계 프로세스입니다.

업로드 프로세스에서는 인증서를 포함하는 파일이 업로드됩니다.  이 파일에 개인 키는 절대 포함하지 않아야 합니다.

소유 증명 단계에서는 사용자와 IoT Hub 간의 암호화 챌린지 및 응답 프로세스가 진행됩니다.  디지털 인증서 콘텐츠가 공용이어서 도청에 취약하다고 가정할 경우 IoT Hub는 사용자가 실제로 해당 CA 인증서를 소유하는지 확인하려고 할 것입니다.  이 작업은 CA 인증서의 해당 개인 키로 서명해야 하는 임의 챌린지를 생성하여 수행할 수 있습니다.  앞서 권장한 것처럼 개인 키를 기밀로 유지하고 보호한 경우 이 단계를 완료했다는 사실은 사용자만 알 수 있습니다. 개인 키를 비밀로 유지하는 것이 이 방법에서 신뢰를 유지하는 기반입니다.  챌린지에 서명한 후에 결과를 포함하는 파일을 업로드하여 이 단계를 완료합니다.

여기에서는 [CA 인증서를 등록](iot-hub-security-x509-get-started.md#registercerts)하는 방법을 알아봅니다.

## <a name="how-to-create-a-device-on-iot-hub"></a>IoT Hub에서 장치를 만드는 방법

장치 가장을 차단하기 위해 IoT Hub 허브는 예상되는 장치를 알려줄 것을 요구합니다.  이를 위해 IoT Hub 장치 레지스트리에서 장치 항목을 만듭니다.  이 프로세스는 IoT Hub DPS([장치 프로비전 서비스](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/))를 사용하면 자동화됩니다. 

여기에서는 [IoT Hub에서 수동으로 장치를 만드는](iot-hub-security-x509-get-started.md#createdevice) 방법을 알아봅니다.

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>X.509 CA 인증서로 서명된 장치 인증

X.509 CA 인증서가 등록되고 신뢰할 수 있는 인증서 체인에 장치가 서명되면 장치가 연결될 때(처음인 경우도 해당) 장치를 인증하기만 하면 됩니다.  X.509 CA에서 서명한 장치가 연결되면 유효성 검사를 위해 인증서 체인을 업로드합니다. 체인에는 모든 중간 CA 및 장치 인증서가 포함됩니다.  이 정보를 사용하여 IoT 허브는 두 단계 프로세스로 장치를 인증합니다.  IoT Hub는 암호화 방식으로 인증서 체인의 내부 일관성이 유지되는지 확인하고 장치에 소유 증명 챌린지를 발급합니다.  IoT Hub는 장치에서 성공적인 소유 증명 응답을 받으면 장치를 인증된 것으로 선언합니다.  이 선언에서는 장치의 개인 키가 보호되며 해당 장치만 이 챌린지에 성공적으로 응답할 수 있다고 가정합니다.  개인 키를 보호하기 위해 장치에 HSM(하드웨어 보안 모듈)과 같은 보안 칩을 사용하는 것이 좋습니다.

IoT Hub에 장치가 성공적으로 연결되면 인증 프로세스가 완료되며, 제대로 설정된 것입니다.

여기에서는 [이 장치 연결 단계를 완료](iot-hub-security-x509-get-started.md#authenticatedevice)하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

IoT에서 [X.509 CA 인증의 가치](iot-hub-x509ca-concept.md)에 대해 알아보기

IoT Hub [장치 프로비전 서비스](https://docs.microsoft.com/azure/iot-dps/) 시작하기
