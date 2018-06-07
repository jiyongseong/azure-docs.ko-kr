---
title: 'Azure 인증서 인증용 P2S VPN 클라이언트 구성 파일 만들기 및 설치: PowerShell: Azure | Microsoft Docs'
description: P2S 인증서 인증용 Windows, Linux(strongSwan) 및 Mac OS X VPN 클라이언트 구성 파일을 만들고 설치합니다.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: 9b9528aba0be8fd46087d97bc294552db608f1c1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Azure 기본 인증서 인증 Point-to-Site 구성용 VPN 클라이언트 구성 파일 만들기 및 설치

VPN 클라이언트 구성 파일은 zip 파일에 포함되어 있습니다. 구성 파일은 Windows, Mac IKEv2 VPN 또는 Linux 클라이언트에서 Azure 기본 인증서 인증을 사용하는 지점 및 사이트 간 연결을 통해 VNet에 연결하는 데 필요한 설정을 제공합니다.

### <a name="workflow"></a>P2S 워크플로

  1. P2S 연결을 위한 Azure VPN 게이트웨이를 설정합니다.
  2. 루트 인증서 및 클라이언트 인증서를 생성합니다. 루트 인증서 공개 키를 Azure에 업로드하고, 클라이언트 장치에 클라이언트 인증서를 설치합니다. 인증서는 연결하는 사용자를 인증하는 데 사용됩니다.
  3. 선택한 인증 옵션에 대한 VPN 클라이언트 구성을 가져 오고 Windows 및 Mac 장치에서 VPN 클라이언트를 설정합니다. 이렇게 하면 지점 및 사이트 간 연결을 통해 Azure VNet에 연결할 수 있습니다.

>[!NOTE]
>클라이언트 구성 파일은 VNet에 대한 VPN 구성에 적용됩니다. VPN 프로토콜 유형 또는 인증 유형과 같은 VPN 클라이언트 구성 파일을 생성한 후에 지점 및 사이트 간 VPN 구성을 변경하면 사용자 장치에 대한 새 VPN 클라이언트 구성 파일을 생성해야 합니다.
>
>

## <a name="generate"></a>VPN 클라이언트 구성 파일 생성

시작하기 전에 연결하는 모든 사용자에 대해 유효한 인증서가 해당 사용자의 장치에 설치되어 있는지 확인합니다. 클라이언트 인증서 설치에 대한 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

PowerShell을 사용하거나 Azure Portal을 사용하여 클라이언트 구성 파일을 생성할 수 있습니다. 두 메서드 모두 동일한 zip 파일을 반환합니다. 파일의 압축을 풀어 다음 폴더를 확인합니다.

  * **WindowsAmd64** 및 **WindowsX86** - 각각 Windows 64비트 및 32비트 설치 관리자 패키지가 포함되어 있습니다. **WindowsAmd64** 설치 관리자 패키지는 Amd 외에도 지원되는 모든 64비트 Windows 클라이언트에서 사용됩니다.
  * **Generic** - 사용자 고유의 VPN 클라이언트 구성을 만드는 데 사용되는 일반 정보가 포함되어 있습니다. Generic 폴더는 게이트웨이에 IKEv2 또는 SSTP + IKEv2를 구성한 경우에 제공됩니다. SSTP만 구성되면 Generic 폴더가 없습니다.

### <a name="zipportal"></a>Azure Portal을 사용하여 파일 생성

1. Azure Portal에서 연결하려는 가상 네트워크의 가상 네트워크 게이트웨이로 이동합니다.
2. 가상 네트워크 게이트웨이 페이지에서 **지점 및 사이트 간 구성**을 클릭합니다.
3. 지점 및 사이트 간 구성 페이지의 맨 위에서 **VPN 클라이언트 다운로드**를 클릭합니다. 클라이언트 구성 패키지를 생성하는 데 몇 분이 소요됩니다.
4. 클라이언트 구성 zip 파일을 사용할 수 있으면 브라우저에 표시됩니다. 게이트웨이와 동일한 이름이 지정됩니다. 파일의 압축을 풀어 폴더를 확인합니다.

### <a name="zipps"></a>PowerShell을 사용하여 파일 생성

1. VPN 클라이언트 구성 파일을 생성하는 경우 '-AuthenticationMethod' 값은 'EapTls'입니다. 다음 명령을 사용하여 VPN 클라이언트 구성 파일을 생성합니다.

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. 브라우저에 URL을 복사하여 zip 파일을 다운로드한 다음 파일의 압축을 풀어 폴더를 봅니다.

## <a name="installwin"></a>설치 - Windows

클라이언트의 아키텍처와 일치하는 버전이면 각 Windows 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md#P2S)의 지점 및 사이트 간 연결 섹션을 참조하세요.

>[!NOTE]
>연결하려는 Windows 클라이언트 컴퓨터에서 관리자 권한이 있어야 합니다.
>
>

다음 단계에 따라 인증서 인증을 위한 Windows VPN 기본 클라이언트를 구성합니다.

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다. 
2. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 **자세한 정보**, **실행**을 차례로 클릭합니다.
3. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다. 
4. 연결을 시도하기 전에 먼저 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다. 인증서 생성에 대한 자세한 내용은 [인증서 생성](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)을 참조하세요. 클라이언트 인증서를 설치하는 방법은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

## <a name="installmac"></a>설치 - Mac(OS X)

Azure는 원시 Azure 인증서 인증을 위해 mobileconfig 파일을 제공하지 않습니다. Azure에 연결할 모든 Mac에서 원시 IKEv2 VPN 클라이언트를 수동으로 구성해야 합니다. **Generic** 폴더에는 이것을 구성하는 데 필요한 모든 정보가 들어 있습니다. 다운로드에 Generic 폴더가 보이지 않으면 IKEv2가 터널 유형으로 선택되지 않았을 가능성이 큽니다. IKEv2가 선택되면 zip 파일을 다시 생성하여 Generic 폴더를 검색합니다. Generic 폴더에 포함되는 파일은 다음과 같습니다.

* **VpnSettings.xml** - 서버 주소 및 터널 종류와 같은 중요한 설정이 포함되어 있습니다. 
* **VpnServerRoot.cer** - P2S 연결 설정에서 Azure VPN Gateway의 유효성을 검사하는 데 필요한 루트 인증서가 포함되어 있습니다.

다음 단계에 따라 인증서 인증을 위한 Mac VPN 기본 클라이언트를 구성합니다. Azure에 연결할 모든 Mac에서 다음 단계를 완료해야 합니다.

1. **VpnServerRoot** 루트 인증서를 Mac으로 가져옵니다. 이렇게 하려면 해당 파일을 Mac으로 복사하고 두 번 클릭하면 됩니다.  
**추가**를 클릭하여 가져옵니다.

  ![인증서 추가](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >인증서를 두 번 클릭해도 **추가** 대화 상자가 표시되지 않을 수 있지만 인증서는 올바른 저장소에 설치되어 있습니다. 인증서 범주 아래의 로그인 키 집합에서 인증서를 확인할 수 있습니다.
  
2. P2S 설정을 구성할 때 Azure로 업로드한 루트 인증서에 의해 발행된 클라이언트 인증서가 설치되어 있는지 확인합니다. 이 인증서는 이전 단계에서 설치한 VPNServerRoot와 다른 인증서입니다. 클라이언트 인증서는 인증에 사용되므로 반드시 필요합니다. 인증서 생성에 대한 자세한 내용은 [인증서 생성](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)을 참조하세요. 클라이언트 인증서를 설치하는 방법은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.
3. **네트워크 기본 설정** 아래에서 **네트워크** 대화 상자를 열고 **'+'**를 클릭하여 Azure VNet에 대한 P2S 연결용 새 VPN 클라이언트 연결 프로필을 만듭니다.

  **인터페이스** 값은 'VPN'이고, **VPN 유형** 값은 'IKEv2'입니다. **서비스 이름** 필드에 프로필 이름을 지정한 다음 **만들기**를 클릭하여 VPN 클라이언트 연결 프로필을 만듭니다.

  ![네트워크](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. **Generic** 폴더의 **VpnSettings.xml** 파일에서 **VpnServer** 태그 값을 복사합니다. 이 값을 프로필의 **서버 주소** 및 **원격 ID** 필드에 붙여넣습니다.

  ![서버 정보](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. **인증 설정**을 클릭하고 **인증서**를 선택합니다. 

  ![인증 설정](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. **선택...**을 클릭합니다. 인증에 사용하려는 클라이언트 인증서를 선택합니다. 이 인증서는 2단계에서 설치한 인증서입니다.

  ![인증서](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **ID 선택**은 선택할 수 있는 인증서의 목록을 표시합니다. 적절한 인증서를 선택한 다음 **계속**을 클릭합니다.

  ![ID](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. **로컬 ID** 필드에서 인증서 이름(6단계에서 사용한 이름)을 지정합니다. 이 예에서는 "ikev2Client.com"입니다. 그런 다음 **적용** 단추를 클릭하여 변경 내용을 저장합니다.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. **네트워크** 대화 상자에서 **적용**을 클릭하여 모든 변경 내용을 저장합니다. 그런 다음 **연결**을 클릭하여 Azure VNet에 대한 P2S 연결을 시작합니다.

## <a name="installlinux"></a>설치 - Linux(strongSwan)

### <a name="extract-the-key-and-certificate"></a>키와 인증서를 추출합니다.

strongSwan의 경우 클라이언트 인증서(.pfx 파일)에서 키와 인증서를 추출하고 개별 .pem 파일에 저장해야 합니다.
다음 단계를 따르세요.

1. [OpenSSL](https://www.openssl.org/source/)에서 OpenSSL을 다운로드 및 설치합니다.
2. 명령줄 창을 열고 OpenSSL을 설치한 디렉터리로 변경합니다(예: 'c:\OpenSLL-Win64\bin\').
3. 다음 명령을 실행하여 개인 키를 추출하고 클라이언트 인증서에서 'privatekey.pem'이라는 새 파일에 저장합니다.

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  이제 다음 명령을 실행하여 공용 인증서를 추출하고 새 파일에 저장합니다.

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>설치

다음 지침은 Ubuntu 17.0.4에서 strongSwan 5.5.1을 통해 작성되었습니다. Ubuntu 16.0.10은 strongSwan GUI를 지원하지 않습니다. Ubuntu 16.0.10을 사용하려는 경우 명령줄을 사용해야 합니다. 아래 예제는 Linux 및 strongSwan 버전에 따라 표시되는 화면과 일치하지 않을 수 있습니다.

1. 이 예의 명령을 실행하여 **터미널**을 열고 **strongSwan**과 네트워크 관리자를 설치합니다. *libcharon-extra-plugins* 관련 오류가 나타나면 'strongswan-plugin-eap-mschapv2'로 바꿉니다.

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. **네트워크 관리자** 아이콘(위쪽 화살표/아래쪽 화살표)을 선택한 다음, **연결 편집**을 선택합니다.

  ![연결 편집](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. **추가** 단추를 클릭하여 새 연결을 만듭니다.

  ![연결 추가](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. 드롭다운 메뉴에서 **IPsec/IKEv2(strongswan)**를 선택한 다음, **만들기**를 클릭합니다. 이 단계에서 연결의 이름을 바꿀 수 있습니다.

  ![연결 형식 선택](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. 다운로드한 클라이언트 구성 파일에 포함된 **일반** 폴더에서 **VpnSettings.xml** 파일을 엽니다. **VpnServer**라는 태그를 찾아 'azuregateway'로 시작하고 '.cloudapp.net'으로 끝나는 이름을 복사합니다.

  ![이름 복사](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. **게이트웨이** 섹션에서 새 VPN 연결의 **주소** 필드에 이 이름을 붙여넣습니다. 다음으로, **인증서** 필드 끝에 있는 폴더 아이콘을 클릭하고 **Generic** 폴더로 이동하여 **VpnServerRoot** 파일을 선택합니다.
7. 연결의 **클라이언트** 섹션에서 **인증**에 대해 **인증서/개인 키**를 선택합니다. **인증서** 및 **개인 키**에 대해 이전에 만든 인증서 및 개인 키를 선택합니다. **옵션**에서 **내부 IP 주소 요청**을 선택합니다. 그런 다음 **추가**를 클릭합니다.

  ![내부 IP 주소 요청](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. **네트워크 관리자** 아이콘(위쪽 화살표/아래쪽 화살표)을 클릭하고 **VPN 연결**을 마우스로 가리킵니다. 만든 VPN 연결이 표시됩니다. 클릭하여 연결을 시작합니다.

## <a name="next-steps"></a>다음 단계

P2S 구성을 완료하려면 [P2S 구성 완료](vpn-gateway-howto-point-to-site-rm-ps.md) 문서로 돌아갑니다.

P2S 문제 해결 정보의 경우 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) 및 [Mac OS X VPN 클라이언트에서 VPN 연결 문제 해결](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)을 참조하세요.