---
title: Azure 지점 및 사이트 간 연결 문제 해결 | Microsoft Docs
description: 지점 및 사이트 간 연결 문제를 해결하는 방법을 알아봅니다.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 0db2291b53c4fe7d2d0894a4c266ed60f78219de
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>문제 해결: Azure 지점 및 사이트 간 연결 문제

이 문서에서는 발생할 수 있는 일반적인 지점 및 사이트 간 연결 문제를 나열합니다. 또한 이러한 문제의 가능한 원인과 해결 방법을 설명합니다.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN 클라이언트 오류: 인증서를 찾을 수 없습니다.

### <a name="symptom"></a>증상

VPN 클라이언트를 사용하여 Azure 가상 네트워크에 연결하려고 할 때 다음과 같은 오류 메시지가 나타납니다.

**이 확장할 수 있는 인증 프로토콜에 사용할 수 있는 인증서를 찾을 수 없습니다. (오류 798)**

### <a name="cause"></a>원인

클라이언트 인증서가 **인증서 - Current User\Personal\Certificates**에서 누락된 경우 이 문제가 발생합니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 다음 단계를 수행합니다.

1. 인증서 관리자에서: **시작**을 클릭하고 **컴퓨터 인증서 관리**를 입력한 다음, 검색 결과에서 **컴퓨터 인증서 관리**를 선택합니다.

2. 다음 인증서가 올바른 위치에 있는지 확인합니다.

    | 인증서 | 위치 |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

3. Users\<UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>로 이동하고 인증서(*.cer file)를 사용자 및 컴퓨터의 스토어에 수동으로 설치합니다.

클라이언트 인증서를 설치하는 방법에 대한 자세한 내용은 [지점 및 사이트 간 연결에 대한 인증서를 생성 및 내보내기](vpn-gateway-certificates-point-to-site.md)를 참조하세요.

> [!NOTE]
> 클라이언트 인증서를 가져올 때 **강력한 개인 키 보호 사용** 옵션을 선택하지 않습니다.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN 클라이언트 오류: 예기치 않거나 형식이 잘못된 메시지를 수신했습니다.

### <a name="symptom"></a>증상

VPN 클라이언트를 사용하여 Azure 가상 네트워크에 연결하려고 할 때 다음과 같은 오류 메시지가 나타납니다.

**예기치 않거나 형식이 잘못된 메시지를 수신했습니다. (오류 0x80090326)**

### <a name="cause"></a>원인

이 문제는 다음 조건 중 하나가 true인 경우에 발생합니다.

- 게이트웨이 서브넷의 기본 경로를 사용한 UDR(사용자 정의 경로)이 잘못 설정되었습니다.
- 루트 인증서 공개 키가 Azure VPN 게이트웨이에 업로드되지 않았습니다. 
- 키가 손상되거나 만료되었습니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 다음 단계를 수행합니다.

1. 게이트웨이 서브넷에서 UDR를 제거합니다. UDR이 모든 트래픽을 제대로 전달하는지 확인합니다.
2. Azure Portal에서 루트 인증서의 상태를 검사하여 인증서가 해지되었는지 여부를 확인합니다. 해지되지 않은 경우 루트 인증서를 삭제하고 다시 업로드합니다. 자세한 내용은 [인증서 만들기](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)를 참조하세요.

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN 클라이언트 오류: 인증서 체인이 처리되었지만 종료되었습니다. 

### <a name="symptom"></a>증상 

VPN 클라이언트를 사용하여 Azure 가상 네트워크에 연결하려고 할 때 다음과 같은 오류 메시지가 나타납니다.

**인증서 체인이 처리되었지만 트러스트 공급자가 신뢰하지 않는 루트 인증서에서 종료되었습니다.**

### <a name="solution"></a>해결 방법

1. 다음 인증서가 올바른 위치에 있는지 확인합니다.

    | 인증서 | 위치 |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. 인증서가 이미 있으면 해당 인증서를 삭제하고 다시 설치하려고 합니다. **azuregateway-*GUID*.cloudapp.net** 인증서는 Azure Portal에서 다운로드한 VPN 클라이언트 구성 패키지에 있습니다. 패키지에서 파일을 추출하려면 파일 실행자를 사용할 수 있습니다.

## <a name="file-download-error-target-uri-is-not-specified"></a>파일 다운로드 오류: 대상 URI를 지정하지 않았습니다.

### <a name="symptom"></a>증상

다음과 같은 오류 메시지가 표시됩니다.

**파일 다운로드 오류. 대상 URI가 지정되지 않았습니다.**

### <a name="cause"></a>원인 

이 문제는 잘못된 게이트웨이 형식 때문에 발생합니다. 

### <a name="solution"></a>해결 방법

VPN 게이트웨이 형식은 **VPN**이어야 하고 VPN 형식은 **경로 기반**이어야 합니다.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN 클라이언트 오류: Azure VPN 사용자 지정 스크립트가 실패했습니다. 

### <a name="symptom"></a>증상

VPN 클라이언트를 사용하여 Azure 가상 네트워크에 연결하려고 할 때 다음과 같은 오류 메시지가 나타납니다.

**(라우팅 테이블을 업데이트하는) 사용자 지정 스크립트에 실패했습니다. (오류 8007026f)**

### <a name="cause"></a>원인

이 문제는 바로 가기를 사용하여 사이트 및 지점 간 VPN 연결을 시도하는 경우에 발생할 수 있습니다.

### <a name="solution"></a>해결 방법 

바로 가기로 열지 않고 직접 VPN 패키지를 엽니다.

## <a name="cannot-install-the-vpn-client"></a>VPN 클라이언트를 설치할 수 없습니다.

### <a name="cause"></a>원인 

가상 네트워크에 대한 VPN Gateway를 신뢰하기 위해 추가 인증서가 필요합니다. 인증서는 Azure Portal에서 생성되는 VPN 클라이언트 구성 패키지에 포함됩니다.

### <a name="solution"></a>해결 방법

VPN 클라이언트 구성 패키지를 추출하고 .cer 파일을 찾습니다. 인증서를 설치하려면 다음 단계를 따르세요.

1. mmc.exe를 엽니다.
2. **인증서** 스냅인을 추가합니다.
3. 로컬 컴퓨터의 **컴퓨터** 계정을 선택합니다.
4. **신뢰할 수 있는 루트 인증 기관** 노드를 마우스 오른쪽 단추로 클릭합니다. **모든 작업** > **가져오기**를 클릭하고 VPN 클라이언트 구성 패키지에서 추출한 .cer 파일을 찾습니다.
5. 컴퓨터를 다시 시작합니다. 
6. VPN 클라이언트를 설치해봅니다.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal 오류: VPN 게이트웨이를 저장하지 못했으며 데이터가 유효하지 않습니다.

### <a name="symptom"></a>증상

Azure Portal에서 VPN Gateway에 변경 내용을 저장하려고 할 때 다음과 같은 오류 메시지가 나타납니다.

**가상 네트워크 게이트웨이 &lt;*게이트웨이 이름*&gt;을(를) 저장하지 못했습니다. &lt;*인증서 ID*&gt; 인증서에 대한 데이터가 잘못되었습니다.**

### <a name="cause"></a>원인 

업로드한 루트 인증서 공개 키가 공백과 같은 유효하지 않은 문자를 포함하는 경우 이 문제가 발생할 수 있습니다.

### <a name="solution"></a>해결 방법

인증서의 데이터에 줄 바꿈(캐리지 리턴)과 같은 유효하지 않은 문자가 없는지 확인합니다. 전체 값은 한 줄이어야 합니다. 다음 텍스트는 인증서의 샘플입니다.

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal 오류: VPN 게이트웨이를 저장하지 못했으며 리소스 이름이 유효하지 않습니다.

### <a name="symptom"></a>증상

Azure Portal에서 VPN Gateway에 변경 내용을 저장하려고 할 때 다음과 같은 오류 메시지가 나타납니다. 

**가상 네트워크 게이트웨이 &lt;*게이트웨이 이름*&gt;을(를) 저장하지 못했습니다. 리소스 이름 &lt;*업로드하려는 인증서 이름*&gt;이(가) 잘못되었습니다**.

### <a name="cause"></a>원인

이 문제는 인증서의 이름이 공백과 같은 유효하지 않은 문자를 포함하기 때문에 발생합니다. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure Portal 오류: VPN 패키지 파일 다운로드 오류 503

### <a name="symptom"></a>증상

VPN 클라이언트 구성 패키지를 다운로드하려고 할 때 다음과 같은 오류 메시지가 나타납니다.

**파일을 다운로드하지 못했습니다. 오류 세부 정보: 오류 503 서버가 사용 중입니다.**
 
### <a name="solution"></a>해결 방법

이 오류는 임시 네트워크 문제로 인해 발생할 수 있습니다. VPN 패키지를 몇 분 후에 다시 다운로드해봅니다.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN Gateway 업그레이드: 모든 지점 및 사이트 간 클라이언트를 연결할 수 없습니다.

### <a name="cause"></a>원인

인증서가 수명의 50%를 넘는 경우 롤오버됩니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 모든 클라이언트에 지점 및 사이트 간 패키지를 다시 배포합니다.

## <a name="too-many-vpn-clients-connected-at-once"></a>한 번에 너무 많은 VPN 클라이언트 연결

각 VPN Gateway의 최대 허용 연결 수는 128개입니다. Azure Portal에서 연결된 클라이언트의 총 수를 볼 수 있습니다.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>지점 및 사이트 간 VPN은 10.0.0.0/8의 경로를 경로 테이블에 올바르게 않게 추가했습니다.

### <a name="symptom"></a>증상

지점 및 사이트 간 클라이언트에서 VPN 연결을 사용하는 경우 VPN 클라이언트는 Azure 가상 네트워크에 대한 경로를 추가해야 합니다. IP 도우미 서비스는 VPN 클라이언트의 서브넷에 대한 경로를 추가해야 합니다. 

VPN 클라이언트 범위는 10.0.12.0/24와 같은 10.0.0.0/8의 더 작은 서브넷에 속해 있습니다. 10.0.12.0/24에 대한 경로 대신 우선 순위가 더 높은 10.0.0.0/8에 대한 경로가 추가됩니다. 

이 잘못된 경로는 정의된 특정 경로가 없는 10.50.0.0/24와 같은 10.0.0.0/8 범위 내에서 다른 서브넷에 속할 수 있는 다른 온-프레미스 네트워크와의 연결을 중단합니다. 

### <a name="cause"></a>원인

이 동작은 Windows 클라이언트용으로 설계되었습니다. 클라이언트가 PPP IPCP 프로토콜을 사용하는 경우 서버(이 경우에 VPN 게이트웨이)의 터널 인터페이스에 IP 주소를 가져옵니다. 그러나 프로토콜의 제한 사항 때문에 클라이언트에는 서브넷 마스크가 없습니다. 가져올 다른 방법이 없기 때문에 클라이언트는 터널 인터페이스 IP 주소의 클래스에 따라 서브넷 마스크를 추측하려고 합니다. 

따라서 다음과 같은 고정 매핑에 따라 경로가 추가됩니다. 

주소가 클래스 A에 속하는 경우 --> /8 적용

주소가 클래스 B에 속하는 경우 --> /16 적용

주소가 클래스 C에 속하는 경우 --> /24 적용

### <a name="solution"></a>해결 방법

다른 네트워크의 경로가 지점 및 사이트 간보다 낮은 메트릭(높은 우선 순위) 또는 가장 긴 접두사 일치로 라우팅 테이블에 삽입되도록 합니다. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN 클라이언트는 네트워크 파일 공유에 액세스할 수 없습니다.

### <a name="symptom"></a>증상

VPN 클라이언트가 Azure 가상 네트워크에 연결됩니다. 그러나 클라이언트는 네트워크 공유에 액세스할 수 없습니다.

### <a name="cause"></a>원인

SMB 프로토콜은 파일 공유 액세스에 사용됩니다. 연결을 시작할 때 VPN 클라이언트는 세션 자격 증명을 추가하고 오류가 발생합니다. 연결이 설정되면 클라이언트는 Kerberos 인증에 캐시 자격 증명을 사용하도록 강제됩니다. 이 프로세스는 토큰을 가져오는 키 배포 센터(도메인 컨트롤러)에 대한 쿼리를 시작합니다. 클라이언트가 인터넷에 연결되기 때문에 도메인 컨트롤러에 도달할 수 없습니다. 따라서 클라이언트는 Kerberos에서 NTLM으로 장애 조치할 수 없습니다. 

클라이언트에 자격 증명에 대한 메시지가 표시되는 유일한 시점은 연결된 도메인에서 발급된 유효한 인증서(SAN=UPN)를 갖고 있는 경우입니다. 클라이언트도 도메인 네트워크에 물리적으로 연결되어야 합니다. 이 경우에 클라이언트는 인증서를 사용하고 도메인 컨트롤러에 도달하려고 합니다. 그런 다음 키 배포 센터는 "KDC_ERR_C_PRINCIPAL_UNKNOWN" 오류를 반환합니다. 클라이언트를 NTLM으로 장애 조치하도록 강제합니다. 

### <a name="solution"></a>해결 방법

문제를 해결하려면 다음 레지스트리 하위 키의 도메인 자격 증명 캐싱을 비활성화합니다. 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>VPN 클라이언트를 다시 설치한 후에 Windows에서 지점 및 사이트 간 VPN 연결을 찾을 수 없습니다.

### <a name="symptom"></a>증상

지점 및 사이트 간 VPN 연결을 제거한 다음 VPN 클라이언트를 다시 설치합니다. 이 경우에 VPN 연결이 성공적으로 구성되지 않았습니다. VPN 연결이 Windows의 **네트워크 연결** 설정에 표시되지 않습니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 **C:\사용자\사용자 이름\AppData\Microsoft\Network\Connections\<VirtualNetworkId>** 에서 기존 VPN 클라이언트 구성 파일을 삭제하고 VPN 클라이언트 설치 관리자를 다시 실행합니다.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>지점 및 사이트 간 VPN 클라이언트는 로컬 도메인에 있는 리소스의 FQDN을 확인할 수 없습니다.

### <a name="symptom"></a>증상

클라이언트가 지점 및 사이트 간 VPN 연결을 사용하여 Azure에 연결할 때는 로컬 도메인에 있는 리소스의 FQDN을 확인할 수 없습니다.

### <a name="cause"></a>원인

지점 및 사이트 간 VPN 클라이언트는 Azure 가상 네트워크에 구성된 Azure DNS 서버를 사용합니다. Azure DNS 서버는 클라이언트에서 구성되는 로컬 DNS 서버보다 우선하므로 모든 DNS 쿼리가 Azure DNS 서버로 전송됩니다. Azure DNS 서버에 로컬 리소스에 대한 레코드를 없는 경우 쿼리가 실패합니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 Azure 가상 네트워크에서 사용되는 Azure DNS 서버가 로컬 리소스에 대한 DNS 레코드를 확인할 수 있는지 확인합니다. 이를 수행하기 위해 DNS 전달자 또는 조건부 전달자를 사용할 수 있습니다. 자세한 내용은 [자체 DNS 서버를 이용한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>지점 및 사이트 간 VPN 연결이 설정되어 있지만 여전히 Azure 리소스에 연결할 수 없습니다. 

### <a name="cause"></a>원인

이 문제는 VPN 클라이언트가 Azure VPN 게이트웨이에서 경로를 가져오지 않을 경우에 발생할 수 있습니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 [Azure VPN 게이트웨이를 다시 설정](vpn-gateway-resetgw-classic.md)합니다.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>오류: “해지 서버가 오프라인 상태이므로 해지 함수가 해지를 확인할 수 없습니다(오류 0x80092013).”

### <a name="causes"></a>원인
이 오류 메시지는 클라이언트가 http://crl3.digicert.com/ssca-sha2-g1.crl 및 http://crl4.digicert.com/ssca-sha2-g1.cr에 액세스할 수 없는 경우 발생합니다.  해지 검사를 수행하려면 이러한 두 사이트에 액세스해야 합니다.  이 문제는 일반적으로 프록시 서버가 구성된 클라이언트에서 발생합니다. 일부 환경에서 요청이 프록시 서버를 거치지 않을 경우 Edge 방화벽에서 거부됩니다.

### <a name="solution"></a>해결 방법

프록시 서버 설정을 확인하여 클라이언트가 http://crl3.digicert.com/ssca-sha2-g1.crl 및 http://crl4.digicert.com/ssca-sha2-g1.cr에 액세스할 수 있는지 확인합니다.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN 클라이언트 오류: RAS/VPN 서버에 구성된 정책 때문에 연결되지 않았습니다. (오류 812)

### <a name="cause"></a>원인

이 오류는 VPN 클라이언트 인증에 사용한 RADIUS 서버의 설정이 잘못되었거나 Azure Gateway가 RADIUS 서버에 연결할 수 없는 경우에 발생합니다.

### <a name="solution"></a>해결 방법

RADIUS 서버가 올바르게 구성되어 있는지 확인합니다. 자세한 내용은 [Azure Multi-Factor Authentication 서버와 RADIUS 인증 통합](../active-directory/authentication/howto-mfaserver-dir-radius.md)을 참조하세요.

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>VPN Gateway에서 루트 인증서를 다운로드할 때 "오류 405" 표시

### <a name="cause"></a>원인

루트 인증서가 설치되지 않았습니다. 루트 인증서는 클라이언트의 **신뢰할 수 있는 인증서** 저장소에 설치되어 있습니다.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN 클라이언트 오류: 시도한 VPN 터널이 실패하여 원격 연결이 설정되지 않았습니다. (오류 800) 

### <a name="cause"></a>원인

NIC 드라이버가 오래되었습니다.

### <a name="solution"></a>해결 방법

NIC 드라이버를 업데이트합니다.

1. **시작**을 클릭하고 **장치 관리자**를 입력한 다음, 결과 목록에서 선택합니다. 관리자 암호를 입력하거나 확인하라는 메시지가 표시되면 암호를 입력하거나 확인을 제공합니다.
2. **네트워크 어댑터** 범주에서 업데이트하려는 NIC를 찾습니다.  
3. 장치 이름을 두 번 클릭하고 **드라이버 업데이트**를 선택한 다음, **업데이트된 드라이버 소프트웨어 자동 검색**을 선택합니다.
4. Windows에서 새 드라이버를 찾지 못하는 경우 장치 제조업체의 웹 사이트에서 드라이버를 찾은 다음 해당 지침을 따를 수 있습니다.
5. 컴퓨터를 다시 시작하고 연결을 다시 시도합니다.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>오류: ‘파일 다운로드 오류. 대상 URI를 지정하지 않았습니다.’

### <a name="cause"></a>원인

이 오류는 잘못된 게이트웨이 형식을 구성한 경우에 발생합니다.

### <a name="solution"></a>해결 방법

Azure VPN 게이트웨이 형식은 VPN이어야 하고 VPN 형식은 **경로 기반**이어야 합니다.

## <a name="vpn-package-installer-doesnt-complete"></a>VPN 패키지 설치 관리자가 완료되지 않음

### <a name="cause"></a>원인

이 문제는 이전 VPN 클라이언트 설치로 인해 발생할 수 있습니다. 

### <a name="solution"></a>해결 방법

**C:\사용자\사용자 이름\AppData\Microsoft\Network\Connections\<VirtualNetworkId>** 에서 기존 VPN 클라이언트 구성 파일을 삭제하고 VPN 클라이언트 설치 관리자를 다시 실행합니다. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN 클라이언트가 일정 시간 후에 최대 절전 모드 또는 절전 모드로 전환됨

### <a name="solution"></a>해결 방법

VPN 클라이언트를 실행 중인 컴퓨터의 절전 모드 및 최대 절전 모드 설정을 확인합니다.
