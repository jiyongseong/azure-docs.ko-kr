---
title: Azure IoT Hub Device Provisioning 서비스를 사용하여 X.509 CA 인증서에 대해 소유 증명을 수행하는 방법 | Microsoft Docs
description: DPS 서비스를 사용하여 X.509 CA 인증서를 확인하는 방법
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: eb37ce7e61796494be0a9282afdc620b0ca5886a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Device Provisioning 서비스를 사용하여 X.509 CA 인증서에 대해 소유 증명을 수행하는 방법

확인된 X.509 CA(인증 기관) 인증서는 프로비전 서비스에 업로드되고 등록되며 서비스를 사용하여 소유 증명을 수행한 CA 인증서입니다. 

소유 증명에는 다음과 같은 단계가 포함됩니다.
1. X.509 CA 인증서에 대한 프로비전 서비스에서 생성된 고유한 확인 코드를 가져옵니다. Azure Portal에서 이 작업을 수행할 수 있습니다.
2. 확인 코드를 제목으로 사용하여 X.509 확인 인증서를 만들고 X.509 CA 인증서와 연결된 개인 키를 사용하여 인증서에 서명합니다.
3. 서비스에 서명된 확인 인증서를 업로드합니다. 서비스는 확인된 CA 인증서의 공개 부분을 사용하여 확인 인증서의 유효성을 검사합니다. 따라서 CA 인증서의 개인 키를 소유했는지 확인합니다.

확인된 인증서는 등록 그룹을 사용하는 경우 중요한 역할을 수행합니다. 인증서 소유권을 확인하면 인증서의 업로더가 인증서의 개인 키를 소유했는지 확인하여 추가 보안 계층을 제공합니다. 확인을 통해 트래픽을 찾아낸 악성 행위자가 중간 인증서를 추출하고, 해당 인증서를 사용하여 고유한 프로비전 서비스에서 등록 그룹을 만들고, 효과적으로 장치를 하이재킹하지 않도록 방지합니다. 인증서 체인에서 루트 또는 중간 인증서의 소유권을 증명하여 해당 등록 그룹의 일부로 등록하는 장치에 대한 리프 인증서를 생성하는 권한이 있는지 입증합니다. 이로 인해 등록 그룹에서 구성된 루트 또는 중간 인증서는 확인된 인증서여야 하거나 서비스에 인증할 때 장치가 나타내는 인증서 체인에서 확인된 인증서로 롤업되어야 합니다. 등록 그룹에 대해 자세히 알아보려면 [X.509 인증서](concepts-security.md#x509-certificates) 또는 [X.509 인증서를 사용하여 프로비전 서비스에 대한 장치 액세스 제어](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)를 참조하세요.

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>X.509 인증서의 공개 부분 등록 및 확인 코드 가져오기

CA 인증서를 프로비전 서비스에 등록하고 소유 증명하는 동안 사용할 수 있는 확인 코드를 가져오려면 다음과 같은 단계를 수행합니다. 

1. Azure Portal에서 프로비전 서비스로 이동하고 왼쪽 메뉴에서 **인증서**를 엽니다. 
2. **추가**를 클릭하여 새 인증서를 추가합니다.
3. 인증서의 표시 이름을 입력합니다. X.509 인증서의 공개 부분을 나타내는 .cer 또는 .pem 파일을 찾습니다. **업로드**를 클릭합니다.
4. 인증서가 업로드가 완료되었다는 알림을 받으면 **저장**을 클릭합니다.

    ![인증서 업로드](./media/how-to-verify-certificates/add-new-cert.png)  

   인증서가 **인증서 탐색기** 목록에 표시됩니다. 이 인증서의 **상태**는 *확인되지 않음*입니다.

5. 이전 단계에서 추가한 인증서를 클릭합니다.

6. **인증서 세부 정보**에서 **확인 코드 생성**을 클릭합니다.

7. 프로비전 서비스는 인증서 소유권의 유효성을 검사하는 데 사용할 수 있는 **확인 코드**를 만듭니다. 코드를 클립보드에 복사합니다. 

   ![인증서 확인](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>디지털로 확인 코드에 서명하여 확인 인증서 만들기

이제 서명을 생성하는 X.509 CA 인증서와 연결된 개인 키를 사용하여 *확인 코드*에 서명해야 합니다. [소유 증명](https://tools.ietf.org/html/rfc5280#section-3.1)이라고 알려져 있으며 서명된 확인 인증서의 결과입니다.

Microsoft는 서명된 확인 인증서를 만들 수 있는 도구 및 샘플을 제공합니다. 

- **Azure IoT Hub C SDK**는 확인 코드를 사용하여 CA 및 리프 인증서를 만들고 소유 증명을 수행하기 위해 PowerShell(Windows) 및 Bash(Linux) 스크립트를 제공합니다. 작업 폴더에 시스템과 관련된 [파일](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)을 다운로드하고 [CA 인증서 관리 추가 정보](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) 의 지침에 따라 CA 인증서에서 소유 증명을 수행할 수 있습니다. 
- **Azure IoT Hub C# SDK**에는 [그룹 인증서 확인 샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample)이 포함됩니다. 이 기능은 소유 증명을 수행하는 데 사용할 수 있습니다.
- IoT Hub 설명서의 [CA 서명 X.509 인증서를 관리하는 PowerShell 스크립트](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates)의 단계를 수행할 수 있습니다. 특히, [X.509 CA 인증서의 소유권 증명](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates#signverificationcode)이라는 섹션에 언급된 스크립트를 수행합니다.
 
> [!IMPORTANT]
> 소유 증명을 수행하는 것 외에도 이전에 언급된 PowerShell 및 Bash 스크립트를 통해 장치를 인증하고 프로비전하는 데 사용할 수 있는 루트 인증서, 중간 인증서 및 리프 인증서를 만들 수 있습니다. 이러한 인증서는 개발 용도로만 사용되어야 합니다. 프로덕션 환경에서 사용되지 않아야 합니다. 

설명서 및 SDK에서 제공된 PowerShell 및 Bash 스크립트는 [OpenSSL](https://www.openssl.org/)을 사용합니다. 소유 증명을 수행할 수 있도록 OpenSSL 또는 기타 타사 도구를 사용할 수도 있습니다. SDK와 함께 제공되는 도구에 대한 자세한 내용은 [SDK에 제공된 도구를 사용하는 방법](how-to-use-sdk-tools.md)을 참조하세요. 


## <a name="upload-the-signed-verification-certificate"></a>서명된 확인 인증서 업로드

1. 결과로 생성된 서명을 포털에서 프로비전 서비스에 확인 인증서로 업로드합니다. Azure Portal의 **인증서 세부 정보**에서 **확인 인증서 .pem 또는 .cer 파일** 필드 옆에 있는 _파일 탐색기_ 아이콘을 사용하여 시스템에서 서명된 인증서를 업로드합니다.

2. 인증서 업로드가 완료되면 **확인**을 클릭합니다. **인증서 탐색기** 목록에서 인증서의 **상태**가 **_확인됨_** 으로 변경됩니다. 자동으로 업데이트되지 않으면 **새로 고침**을 클릭하십시오.

   ![인증서 업로드 확인](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>다음 단계

- 포털을 사용하여 등록 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 장치 등록 관리](how-to-manage-enrollments.md)를 참조하세요.
- 서비스 SDK를 사용하여 등록 그룹을 만드는 방법에 대한 자세한 내용은 [서비스 SDK에서 장치 등록 관리](how-to-manage-enrollments-sdks.md)를 참조하세요.










