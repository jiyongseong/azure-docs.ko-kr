---
title: Azure Active Directory에서 온-프레미스 조건부 액세스 설정 | Microsoft Docs
description: Windows Server 2012 R2에서 AD FS(Active Directory Federation Service)를 사용하여 온-프레미스 응용 프로그램에 대한 조건부 액세스를 사용하도록 설정하는 단계별 가이드입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 0ce4497a8bebf9078363509c1f962728ab4189f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정
사용자가 Azure AD(Azure Active Directory) 장치 등록 서비스에 자신의 개인 장치를 작업 공간에 연결하도록 요구하면 해당 장치가 조직에 알려진 것으로 표시될 수 있습니다. 다음은 Windows Server 2012 R2에서 AD FS(Active Directory Federation Services)를 사용하여 온-프레미스 응용 프로그램에 대한 조건부 액세스를 사용하도록 설정하는 단계별 가이드입니다.

> [!NOTE]
> Azure Active Directory 장치 등록 서비스 조건부 액세스 정책에 등록된 장치를 사용하는 경우 Office 365 라이선스 또는 Azure AD Premium 라이선스가 필요합니다. 여기에는 온-프레미스 리소스에서 AD FS에 의해 적용되는 정책이 포함됩니다.
> 
> 온-프레미스 리소스의 조건부 액세스 시나리오에 대한 자세한 내용은 [회사 응용 프로그램에서 SSO 및 원활한 2단계 인증을 위해 모든 장치에서 작업 공간 연결](https://technet.microsoft.com/library/dn280945.aspx)을 참조하세요.

이러한 기능은 Azure Active Directory Premium 라이선스를 구입한 고객에게 제공됩니다.

## <a name="supported-devices"></a>지원되는 장치
* Windows 7 도메인 가입 장치
* Windows 8.1 개인 및 도메인 가입 장치
* Safari 브라우저의 경우 iOS 6 이상
* Android 4.0 이상, 삼성 GS3 이상 휴대폰, 삼성 갤럭시 노트 2 이상 태블릿

## <a name="scenario-prerequisites"></a>시나리오 필수 조건
* Office 365 또는 Azure Active Directory Premium 구독
* Azure Active Directory 테넌트
* Windows Server Active Directory(Windows Server 2008 이상)
* Windows Server 2012 R2에서 업데이트된 스키마
* Azure Active Directory Premium에 대한 라이선스
* Azure AD에 대해 SSO로 구성된 Windows Server 2012 R2 페더레이션 서비스
* Windows Server 2012 R2 웹 응용 프로그램 프록시 
* Azure AD Connect(Microsoft Azure Active Directory Connect)[(Azure AD Connect 다운로드)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* 확인된 도메인

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제
* 장치 기반 조건부 액세스 정책을 사용하려면 장치 개체를 Azure Active Directory에서 Active Directory로 쓰기 저장해야 합니다. 장치 개체를 Active Directory에 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다.
* iOS 7 장치는 항상 클라이언트 인증서 인증 중에 사용자에게 인증서를 선택하라는 메시지를 표시합니다.
* iOS 8.3 이전의 iOS 8 버전 중 일부는 작동하지 않습니다.

## <a name="scenario-assumptions"></a>시나리오의 가정
이 시나리오에서는 Azure AD 테넌트와 온-프레미스 Active Directory로 구성된 하이브리드 환경이 있다고 가정합니다. 이러한 테넌트는 Azure AD Connect, 확인된 도메인 및 SSO용 AD FS를 사용하여 연결되어야 합니다. 요구 사항에 따라 다음 검사 목록을 사용하여 환경을 구성할 수 있습니다.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>검사 목록: 조건부 액세스 시나리오를 위한 필수 조건
Azure AD 테넌트를 온-프레미스 Active Directory 인스턴스와 연결합니다.

## <a name="configure-azure-active-directory-device-registration-service"></a>Azure Active Directory 장치 등록 서비스 구성
이 가이드를 사용하여 조직에 맞게 Azure Active Directory 장치 등록 서비스를 배포하고 구성합니다.

여기서는 Windows Server Active Directory를 구성하고 Microsoft Azure Active Directory를 구독했다고 가정합니다. 앞에서 설명한 필수 구성 요소를 참조하세요.

Azure Active Directory 테넌트에 Azure Active Directory 장치 등록 서비스를 배포하려면 다음 검사 목록의 작업을 순서대로 완료합니다. 참조 링크가 개념 항목으로 이동하는 경우 나중에 이 검사 목록으로 돌아가서 나머지 작업을 계속 진행할 수 있습니다. 일부 작업에는 단계가 성공적으로 완료되었는지 확인할 수 있는 시나리오 유효성 검사 단계가 포함되어 있습니다.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>1부: Azure Active Directory 장치 등록 사용
검사 목록의 단계에 따라 Azure Active Directory 장치 등록 서비스를 사용하도록 설정하고 구성합니다.

| Task | 참고 자료 | 
| --- | --- |
| Azure Active Directory 테넌트에서 장치 등록을 사용하도록 설정하여 장치에서 작업 공간에 연결할 수 있게 합니다. 기본적으로 Multi-Factor Authentication은 서비스에서 사용되지 않습니다. 그러나 장치를 등록할 때는 Multi-Factor Authentication을 사용하는 것이 좋습니다. Active Directory 등록 서비스에서 Multi-Factor Authentication을 사용하도록 설정하기 전에 먼저 AD FS가 Multi-Factor Authentication 공급자에 대해 구성되어 있는지 확인합니다. |[Azure Active Directory 장치 등록 사용](active-directory-device-registration-get-started.md)| 
|장치에서 잘 알려진 DNS 레코드를 찾아 Azure Active Directory 장치 등록 서비스를 검색합니다. 장치에서 Azure Active Directory 장치 등록 서비스를 검색할 수 있도록 회사 DNS를 구성합니다. |[Azure Active Directory 장치 등록 검색 구성](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>2부: Windows Server 2012 R2 Active Directory Federation Services를 배포 및 구성하고 Azure AD와의 페더레이션 관계를 설정합니다.

| Task | 참고 자료 |
| --- | --- |
| Windows Server 2012 R2 스키마 확장을 사용하여 Active Directory Domain Services를 배포합니다. 도메인 컨트롤러는 Windows Server 2012 R2로 업그레이드할 필요가 없습니다. 스키마 업그레이드만 수행하면 됩니다. |[Active Directory Domain Services 스키마 업그레이드](#upgrade-your-active-directory-domain-services-schema) |
| 장치에서 잘 알려진 DNS 레코드를 찾아 Azure Active Directory 장치 등록 서비스를 검색합니다. 장치에서 Azure Active Directory 장치 등록 서비스를 검색할 수 있도록 회사 DNS를 구성합니다. |[Active Directory 지원 장치 준비](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>3부: Azure AD에서 장치 쓰기 저장 사용
| Task | 참고 자료 |
| --- | --- |
| "Azure AD Connect에서 장치 쓰기 저장 사용"의 2부를 완료합니다. 완료한 후에 이 가이드로 돌아옵니다. |[Azure AD Connect에서 장치 쓰기 저장 사용](./connect/active-directory-aadconnect-feature-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[선택 사항] 4부: Multi-Factor Authentication 사용
Multi-Factor Authentication을 위한 여러 옵션 중 하나를 구성하는 것이 좋습니다. Multi-Factor Authentication을 요구하려면 [사용자를 위한 Multi-Factor Authentication 보안 솔루션 선택](authentication/concept-mfa-whichversion.md)을 참조하세요. 여기에는 각 솔루션에 대한 설명, 선택한 솔루션을 구성하는 데 도움이 되는 링크가 포함되어 있습니다.

## <a name="part-5-verification"></a>5부: 확인
이제 배포가 완료되었으므로 몇 가지 시나리오를 체험할 수 있습니다. 다음 링크를 사용하여 서비스를 시험해 보고 기능을 익혀 보세요.

| Task | 참고 자료 |
| --- | --- |
| Azure Active Directory 장치 등록을 사용하여 일부 장치를 작업 공간에 연결합니다. iOS, Windows 및 Android 장치를 연결할 수 있습니다. |[Azure Active Directory 장치 등록 서비스를 사용하여 작업 공간에 장치 연결](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| 관리자 포털을 사용하여 등록된 장치를 보고 사용하거나 사용하지 않도록 설정할 수 있습니다. 이 작업에서는 관리자 포털을 사용하여 등록된 장치 일부를 확인합니다. |[Azure Active Directory 장치 등록 서비스 개요](active-directory-device-registration-get-started.md) |
| Azure Active Directory에서 Windows Server Active Directory로 장치 개체가 다시 기록되었는지 확인합니다. |[등록된 장치가 Active Directory에 쓰기 저장되는지 확인](#verify-registered-devices-are-written-back-to-active-directory) |
| 이제 사용자가 장치를 등록할 수 있으므로 등록된 장치만 허용하도록 AD FS에서 응용 프로그램 액세스 정책을 만들 수 있습니다. 이 작업에서는 응용 프로그램 액세스 규칙과 사용자 지정 액세스 거부 메시지를 만듭니다. |[응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>온-프레미스 Active Directory와 Azure Active Directory 통합

**다음을 참조하세요.**

- 개념 정보를 검토하려면 [Azure Active Directory에 온-프레미스 디렉터리 통합](./connect/active-directory-aadconnect.md)을 참조하세요.

- 설치 지침은 [Azure AD Connect의 사용자 지정 설치](./connect/active-directory-aadconnect-get-started-custom.md)를 참조하세요.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Active Directory Domain Services 스키마 업그레이드
> [!NOTE]
> Active Directory 스키마를 업그레이드한 후에는 프로세스를 취소할 수 없습니다. 먼저 테스트 환경에서 업그레이드를 수행하는 것이 좋습니다.
> 

1. 엔터프라이즈 관리자 및 스키마 관리자 권한이 모두 있는 계정으로 도메인 컨트롤러에 로그인합니다.
2. **[media]\support\adprep** 디렉터리 및 하위 디렉터리를 Active Directory 도메인 컨트롤러 중 하나(여기서 **[media]** 는 Windows Server 2012 R2 설치 미디어의 경로임)에 복사합니다.
4. 명령 프롬프트에서 **adprep** 디렉터리로 이동하여 **adprep.exe /forestprep**을 실행합니다. 화면의 지시에 따라 스키마 업그레이드를 완료합니다.

## <a name="prepare-your-active-directory-to-support-devices"></a>장치를 지원하도록 Active Directory 준비
> [!NOTE]
> 장치를 지원하도록 Active Directory 포리스트를 준비하기 위해 실행해야 하는 일회성 작업입니다. 이 절차를 완료하려면 엔터프라이즈 관리자 권한으로 로그인해야 하며 Active Directory 포리스트에 Windows Server 2012 R2 스키마가 있어야 합니다.
> 


### <a name="prepare-your-active-directory-forest"></a>Active Directory 포리스트 준비
1. 페더레이션 서버에서 Windows PowerShell 명령 창을 열고 **Initialize-ADDeviceRegistration**을 입력합니다. 
2. **ServiceAccountName**에 대한 메시지가 표시되면 AD FS에 대한 서비스 계정으로 선택한 서비스 계정의 이름을 입력합니다. gMSA 계정인 경우 **domain\accountname$** 형식으로 계정을 입력합니다. 도메인 계정의 경우 **domain\accountname** 형식을 사용합니다.

### <a name="enable-device-authentication-in-ad-fs"></a>AD FS에서 장치 인증 사용
1. 페더레이션 서버에서 AD FS 관리 콘솔을 열고 **AD FS** > **인증 정책**으로 차례로 이동합니다.
2. **동작** 창에서 **전역 기본 인증 편집**을 선택합니다.
3. **장치 인증 사용**을 선택한 다음 **확인**을 선택합니다.
4. 기본적으로 AD FS는 Active Directory에서 사용하지 않은 장치를 정기적으로 제거합니다. Azure에서 장치를 관리할 수 있도록 Azure Active Directory 장치 등록 서비스를 사용하는 경우 이 작업을 비활성화합니다.

### <a name="disable-unused-device-cleanup"></a>사용하지 않은 장치 정리 사용 안 함
페더레이션 서버에서 Windows PowerShell 명령 창을 연 다음 **Set-AdfsDeviceRegistration -MaximumInactiveDays 0**을 입력합니다.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>장치 쓰기 저장을 위해 Azure AD Connect 준비
'1부 Azure AD Connect 준비'를 완료합니다.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Azure Active Directory 장치 등록을 사용하여 작업 공간에 장치 연결

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory 장치 등록을 사용하여 iOS 장치 연결
Azure Active Directory 장치 등록은 iOS 장치에 대한 공중파 프로필 등록 프로세스를 사용합니다. 이 프로세스는 사용자가 Safari를 사용하여 프로필 등록 URL에 연결할 때 시작됩니다. URL 형식은 다음과 같습니다.

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

이 경우 `yourdomainname`은 Azure Active Directory를 사용하여 구성한 도메인 이름입니다. 예를 들어 도메인 이름이 contoso.com인 경우 URL은 다음과 같습니다.

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

다양한 방법으로 이 URL을 사용자에게 알릴 수 있습니다. 예를 들어 AD FS의 사용자 지정 응용 프로그램 액세스 거부 메시지에 이 URL을 게시하는 것이 좋습니다. 이 정보는 다음에 나오는 [응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기](#create-an-application-access-policy-and-custom-access-denied-message) 섹션에서 설명됩니다.

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory 장치 등록을 사용하여 Windows 8.1 장치 연결
1. Windows 8.1 장치에서 **PC 설정** > **네트워크** > **작업 공간**을 차례로 선택합니다.
2. 사용자 이름을 UPN 형식(예: **dan@contoso.com**)으로 입력합니다.
3. **연결**을 선택합니다.
4. 메시지가 표시되면 자격 증명으로 로그인합니다. 이제 장치가 연결되었습니다.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory 장치 등록을 사용하여 Windows 7 장치 연결
Windows 7 도메인 가입 장치를 등록하려면 [장치 등록 소프트웨어 패키지](https://www.microsoft.com/download/details.aspx?id=53554)를 배포해야 합니다.

패키지를 사용하는 방법에 대한 지침은 [비 Windows 10 컴퓨터용 Windows Installer 패키지](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers)를 참조하십시오.

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>등록된 장치가 Active Directory에 쓰기 저장되었는지 확인
LDP.exe 또는 ADSI 편집을 사용하여 장치 개체가 Active Directory에 쓰기 저장되었는지 확인할 수 있습니다. 둘 다 Active Directory 관리자 도구에서 사용할 수 있습니다.

기본적으로 Azure Active Directory에서 쓰기 저장된 장치 개체는 AD FS 팜과 동일한 도메인에 배치됩니다.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기
다음 시나리오를 고려합니다. AD FS에서 응용 프로그램 신뢰 당사자 트러스트를 만들고 등록된 장치만 허용하는 발급 권한 부여 규칙을 구성합니다. 이제 등록된 장치만 응용 프로그램에 액세스할 수 있습니다. 

사용자가 응용 프로그램에 쉽게 액세스할 수 있게 하려면 장치를 연결하는 방법에 대한 지침이 포함된 사용자 지정 액세스 거부 메시지를 구성합니다. 이제 사용자는 응용 프로그램에 액세스할 수 있도록 해당 장치를 원활하게 등록할 수 있습니다.

다음 단계에서는 이 시나리오를 구현하는 방법을 보여 줍니다.

> [!NOTE]
> 이 섹션에서는 AD FS에서 응용 프로그램에 대한 신뢰 당사자 트러스트를 이미 구성했다고 가정합니다.
> 

1. AD FS MMC 도구를 연 다음 **AD FS** > **트러스트 관계** > **신뢰 당사자 트러스트**를 차례로 선택합니다.
2. 새로운 이 액세스 규칙이 적용되는 응용 프로그램을 찾습니다. 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **클레임 규칙 편집**을 선택합니다.
3. **발급 권한 부여 규칙** 탭을 선택한 다음 **규칙 추가**를 선택합니다.
4. **클레임 규칙** 템플릿 드롭다운 목록에서 **들어오는 클레임을 기준으로 사용자 허용 또는 거부**를 선택합니다. 그런 후 **다음**을 선택합니다.
5. **클레임 규칙 이름** 필드에서 **등록된 장치에서 액세스 허용**을 입력합니다.
6. **들어오는 클레임 유형** 드롭다운 목록에서 **등록된 사용자**를 선택합니다.
7. **들어오는 클레임 값** 필드에 **true**를 입력합니다.
8. **이 들어오는 클레임의 사용자에 대한 액세스 허용** 라디오 단추를 선택합니다.
9. **마침**을 선택한 다음 **적용**을 선택합니다.
10. 허용 범위가 만든 규칙보다 더 넓은 규칙을 모두 제거합니다. 예를 들어 **모든 사용자에게 액세스 허용** 기본 규칙을 제거합니다.

이제 응용 프로그램이 사용자가 작업 공간에 등록 및 연결한 장치에서 들어오는 경우에만 액세스를 허용하도록 구성되었습니다. 자세한 고급 액세스 정책은 [추가 Multi-Factor Authentication을 통해 중요한 응용 프로그램에 대한 위험 관리](https://technet.microsoft.com/library/dn280949.aspx)를 참조하세요.

다음으로 응용 프로그램에 대한 사용자 지정 오류 메시지를 구성합니다. 오류 메시지를 통해 사용자는 장치를 작업 공간에 연결해야 응용 프로그램에 액세스할 수 있음을 알게 됩니다. 사용자 지정 HTML 및 PowerShell을 사용하여 사용자 지정 응용 프로그램 액세스 거부 메시지를 만들 수 있습니다.

페더레이션 서버에서 PowerShell 명령 창을 열고 다음 명령을 입력합니다. 명령 부분을 시스템과 관련된 항목으로 바꿉니다.

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
이 응용 프로그램에 액세스하려면 먼저 장치를 등록해야 합니다.

**iOS 장치를 사용하는 경우 다음 링크를 선택하여 장치를 연결합니다**.

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

이 iOS 장치를 작업 공간에 연결합니다.

Windows 8.1 장치를 사용하는 경우 **PC 설정**> **네트워크** > **작업 공간**을 차례로 선택하여 장치를 연결할 수 있습니다.

앞의 명령에서 **relying party trust name**(신뢰 당사자 트러스트 이름)은 AD FS에 있는 응용 프로그램의 신뢰 당사자 트러스트 개체 이름입니다.
그리고 **yourdomain.com**은 Azure Active Directory로 구성한 도메인 이름(예: contoso.com)입니다.
**Set-AdfsRelyingPartyWebContent** cmdlet에 전달하는 HTML 콘텐츠에서 줄 바꿈을 모두 제거해야 합니다(있는 경우).

이제 사용자가 Azure Active Directory 장치 등록 서비스에 등록되지 않은 장치에서 응용 프로그램에 액세스하면 오류가 표시됩니다.

