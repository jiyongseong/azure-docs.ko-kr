---
title: Azure Active Directory 인증서 기반 인증 시작
description: 사용자 환경에서 인증서 기반 인증을 구성하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: db2c19bdc303f6f7773772dd7873878ceb892cc3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33882854"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Azure Active Directory에서 인증서 기반 인증 시작

인증서 기반 인증을 사용하면 Exchange Online 계정을 다음에 연결할 때 Windows, Android 또는 iOS 장치의 클라이언트 인증서를 사용하여 Azure Active Directory에서 인증을 받을 수 있습니다.

- Microsoft Outlook 및 Microsoft Word와 같은 Microsoft 모바일 응용 프로그램
- EAS(Exchange ActiveSync) 클라이언트

이 기능을 구성하면 모바일 장치의 특정 메일 및 Microsoft Office 응용 프로그램에 사용자 이름 및 암호 조합을 입력해야 합니다.

항목 내용:

- Office 365 Enterprise, Business, Education 및 미국 정보 계획의 테넌트 사용자를 위해 인증서 기반 인증을 구성하고 활용하는 과정을 설명합니다. 이 기능은 Office 365 중국, 미국 국방부 및 연방 정부 계획에서 미리 보기 상태로 제공됩니다.
- [PKI(공개 키 인프라)](https://go.microsoft.com/fwlink/?linkid=841737) 및 [AD FS](connect/active-directory-aadconnectfed-whatis.md)가 이미 구성되어 있다고 가정합니다.

## <a name="requirements"></a>요구 사항

인증서 기반 인증을 구성하려면 다음 명령문에 해당되어야 합니다.

- CBA(인증서 기반 인증)는 최신 인증(ADAL)을 사용하는 브라우저 응용 프로그램 또는 네이티브 클라이언트의 페더레이션 환경에서만 지원됩니다. 단, 페더레이션 및 관리 계정 모두에 사용할 수 있는 EXO(Exchange Online)용 EAS(Exchange Active Sync)는 예외입니다.
- Azure Active Directory에는 루트 인증 기관 및 중간 인증 기관을 구성되어야 합니다.
- 각 인증 기관에는 인터넷 연결 URL을 통해 참조될 수 있는 CRL(인증서 해지 목록)이 있어야 합니다.
- Azure Active Directory에 해당 인증 기관이 하나 이상 구성되어 있어야 합니다. [인증 기관 구성](#step-2-configure-the-certificate-authorities) 섹션에서 관련 단계를 찾을 수 있습니다.
- Exchange ActiveSync 클라이언트의 경우, 보안 주체 이름 또는 주체 대체 이름 필드의 RFC822 이름 값에 Exchange Online 사용자가 라우팅할 수 있는 전자 메일 주소가 있어야 합니다. Azure Active Directory는 디렉터리의 프록시 주소 특성에 RFC822 값을 매핑합니다.
- 클라이언트 장치는 클라이언트 인증서를 발급하는 하나 이상의 인증 기관에 액세스해야 합니다.
- 클라이언트 인증을 위한 클라이언트 인증서가 클라이언트에 발급되어야 합니다.

## <a name="step-1-select-your-device-platform"></a>1단계: 장치 플랫폼 선택

첫 번째 단계로 대상 장치 플랫폼에 대해 다음을 검토해야 합니다.

- Office 모바일 응용 프로그램 지원
- 특정 구현 요구 사항

다음 장치 플랫폼에 대한 관련 정보가 있습니다.

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>2단계: 인증 기관 구성

Azure Active Directory에서 인증 기관을 구성하려면 각 인증 기관에 대해 다음을 업로드합니다.

* 인증서의 공개 부분( *.cer* 형식)
* CRL(인증서 해지 목록)이 있는 인터넷 연결 URL

인증 기관에 대한 스키마는 다음과 같습니다.

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

구성에는 [Azure Active Directory PowerShell 버전 2](/powershell/azure/install-adv2?view=azureadps-2.0)를 사용할 수 있습니다.

1. 관리자 권한으로 Windows PowerShell을 시작합니다.
2. Azure AD 모듈 버전 [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) 이상을 설치합니다.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

첫 번째 구성 단계로 테넌트와 연결을 설정해야 합니다. 테넌트에 대한 연결이 있으면 디렉터리에 정의된 신뢰할 수 있는 인증 기관을 검토, 추가, 삭제 및 수정할 수 있습니다.

### <a name="connect"></a>연결

테넌트와 연결을 설정하려면 [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) cmdlet을 사용합니다.

    Connect-AzureAD

### <a name="retrieve"></a>장치

디렉터리에 정의된 신뢰할 수 있는 인증 기관을 검색하려면 [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet을 사용합니다.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>추가

신뢰할 수 있는 인증 기관을 만들려면 [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet을 사용하고 **crlDistributionPoint** 특성을 올바른 값으로 설정합니다.

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>제거

신뢰할 수 있는 인증 기관을 제거하려면 [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet을 사용합니다.

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>수정

신뢰할 수 있는 인증 기관을 수정하려면 [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet을 사용합니다.

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>3단계: 해지 구성

클라이언트 인증서를 해지할 수 있게 Azure Active Directory는 인증 기관 정보의 일부로 업로드된 URL에서 CRL(인증서 해지 목록)을 가져온 후 캐시합니다. CRL의 마지막 게시 타임스탬프(**개시 날짜** 속성)는 CRL이 여전히 유효한지 확인하는 데 사용됩니다. CRL은 목록의 일부인 인증서에 대한 액세스 권한을 해지하기 위해 주기적으로 참조됩니다.

추가 인스턴트 해지가 필요하면(예: 사용자가 장치를 분실한 경우) 사용자의 권한 부여 토큰이 무효화될 수 있습니다. 권한 부여 토큰을 무효화하려면 Windows PowerShell을 사용하여 이 특정 사용자에 대한 **StsRefreshTokenValidFrom** 필드를 설정합니다. 액세스 권한을 해지하려는 각 사용자에 대한 **StsRefreshTokenValidFrom** 필드를 업데이트해야 합니다.

해지가 지속되도록 하려면 **개시 날짜**를 **StsRefreshTokenValidFrom**으로 설정한 값 이후의 날짜로 설정하고 문제의 인증서가 CRL에 있는지 확인해야 합니다.

다음 단계에서는 **StsRefreshTokenValidFrom** 필드를 설정하여 권한 부여 토큰을 업데이트 및 무효화하기 위한 프로세스를 설명합니다.

**해지를 구성하려면**

1. 관리 자격 증명을 MSOL 서비스에 연결합니다.

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. 사용자에 대한 현재 StsRefreshTokensValidFrom 값을 검색합니다.

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. 사용자에 대한 새 StsRefreshTokensValidFrom 값을 현재 타임스탬프와 같게 구성합니다.

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

설정하는 날짜는 이후 날짜여야 합니다. 날짜가 이후 날짜가 아닌 경우 **StsRefreshTokensValidFrom** 속성이 설정되지 않은 것입니다. 날짜가 이후 날짜인 경우 **StsRefreshTokensValidFrom** 이 현재 시간(Set-MsolUser 명령으로 지정된 날짜 아님)으로 설정됩니다.

## <a name="step-4-test-your-configuration"></a>4단계: 구성 테스트

### <a name="testing-your-certificate"></a>인증서 테스트

첫 번째 구성 테스트로 **장치의 브라우저**를 사용하여 [Outlook Web Access](https://outlook.office365.com) 또는 [SharePoint Online](https://microsoft.sharepoint.com)에 로그인을 시도해야 합니다.

로그인에 성공했으면 다음을 의미합니다.

- 테스트 장치에 사용자 인증서가 프로비전되었습니다.
- AD FS가 올바르게 구성되었습니다.

### <a name="testing-office-mobile-applications"></a>Office 모바일 응용 프로그램 테스트

**모바일 Office 응용 프로그램에서 인증서 인증을 테스트하려면**

1. 테스트 장치에서 Office 모바일 응용 프로그램(예: OneDrive)을 설치합니다.
3. 응용 프로그램을 시작합니다.
4. 사용자 이름을 입력하고 사용하려는 사용자 인증서를 선택합니다.

정상적으로 로그인되어야 합니다.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync 클라이언트 응용 프로그램 테스트

인증서 기반 인증을 통해 EAS(Exchange ActiveSync)에 액세스하려면 클라이언트 인증서를 포함하는 EAS 프로필을 응용 프로그램에서 사용할 수 있어야 합니다.

EAS 프로필은 다음 정보를 포함해야 합니다.

- 인증에 사용할 사용자 인증서

- EAS 끝점(예: outlook.office365.com)

Intune과 같은 MDM(모바일 장치 관리)을 활용하거나 장치의 EAS 프로필에 인증서를 수동으로 배치하여 장치에서 EAS 프로필을 구성하고 배치할 수 있습니다.

### <a name="testing-eas-client-applications-on-android"></a>Android에서 EAS 클라이언트 응용 프로그램 테스트

**인증서 인증을 테스트하려면**

1. 이전 섹션의 요구 사항을 충족하는 EAS 프로필을 응용 프로그램에서 구성합니다.
2. 응용 프로그램을 열고 메일이 동기화되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[Android 장치에서 인증서 기반 인증에 대한 추가 정보입니다.](active-directory-certificate-based-authentication-android.md)

[iOS 장치에서 인증서 기반 인증에 대한 추가 정보입니다.](active-directory-certificate-based-authentication-ios.md)