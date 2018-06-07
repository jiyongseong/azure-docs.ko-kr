---
title: Azure AD userPrincipalName 채우기
description: 다음 문서에서는 UserPrincipalName 특성이 채워지는 방법을 설명합니다.
author: billmath
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 96b12fbddd4293c55e9029b194416541ca44c622
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD userPrincipalName 채우기

이 문서에서는 Azure AD(Azure Active Directory)에서 UserPrincipalName 특성이 채워지는 방법을 설명합니다.
UserPrincipalName 특성 값은 사용자 계정에 대한 Azure AD 사용자 이름입니다.

## <a name="upn-terminology"></a>UPN 용어
이 문서에서는 다음과 같은 용어가 사용됩니다.

|용어|설명|
|-----|-----|
|초기 도메인|Azure AD 테넌트의 기본 도메인(onmicrosoft.com)입니다. 예: contoso.onmicrosoft.com.|
|MOERA(Microsoft 온라인 전자 메일 라우팅 주소)|Azure AD에서는 Azure AD MailNickName 특성 및 Azure AD 초기 도메인의 MOERA를 &lt;MailNickName&gt;&#64;&lt; 초기 도메인&gt;으로 계산합니다.|
|온-프레미스 mailNickName 특성|Active Directory의 특성으로, Exchange 조직에서 사용자의 별칭을 나타냅니다.|
|온-프레미스 mail 특성|Active Directory의 특성으로, 사용자의 전자 메일 주소를 나타냅니다.|
|기본 SMTP 주소|Exchange 받는 사람 개체의 기본 전자 메일 주소입니다. 예: SMTP:user@contoso.com|
|대체 로그인 ID|UserPrincipalName 이외의 온-프레미스 특성(예: 로그인에 사용되는 mail 특성)입니다.|

## <a name="what-is-userprincipalname"></a>UserPrincipalName이란?
UserPrincipalName은 인터넷 표준 [RFC 822](http://www.ietf.org/rfc/rfc0822.txt)에 따른 사용자의 인터넷 스타일 로그인 이름에 해당하는 특성입니다. 

### <a name="upn-format"></a>UPN 형식
UPN은 UPN 접두사(사용자 계정 이름) 및 UPN 접미사(DNS 도메인 이름)으로 구성됩니다. "@" 기호를 사용해서 접두사를 접미사에 연결합니다. 예: "someone@example.com". UPN은 디렉터리 포리스트 내의 모든 보안 주체 개체 사이에서 고유해야 합니다. 

## <a name="upn-in-azure-ad"></a>Azure AD의 UPN 
UPN은 사용자가 로그인할 수 있도록 하기 위해 Azure AD에서 사용됩니다.  사용자가 사용할 수 있는 UPN은 도메인 확인 여부에 따라 달라집니다.  도메인이 확인되면 해당 접미사를 포함하는 사용자가 Azure AD에 로그인하도록 허용됩니다.  

이 특성은 Azure AD connect와 동기화됩니다.  설치하는 동안, 확인된 도메인과 확인되지 않은 도메인을 확인할 수 있습니다.

   ![확인되지 않은 도메인](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>대체 로그인 ID
일부 환경에서는 회사 정책 또는 온-프레미스 LOB(기간 업무) 응용 프로그램 종속성으로 인해 최종 사용자가 전자 메일 주소만 인식하고 해당 UPN은 인식하지 못할 수 있습니다.

대체 로그인 ID를 사용하면 사용자가 UPN 이외의 특성(예: 메일)을 사용하여 로그인할 수 있는 로그인 환경을 구성할 수 있습니다.

Azure AD에서 대체 로그인 ID를 사용하도록 설정하기 위해 Azure AD Connect를 사용할 때 추가 구성 단계가 필요하지 않습니다. 마법사에서 직접 대체 ID를 구성할 수 있습니다. 동기화 섹션 아래에서 사용자에 대한 Azure AD 로그인 구성을 확인하세요. **사용자 계정 이름** 드롭다운에서 대체 로그인 ID에 대한 특성을 선택합니다.

![확인되지 않은 도메인](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

자세한 내용은 [대체 로그인 ID 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 및 [Azure AD 로그인 구성](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)을 참조하세요.

## <a name="non-verified-upn-suffix"></a>확인되지 않은 UPN 접미사
온-프레미스 UserPrincipalName 특성/대체 로그인 ID 접미사가 Azure AD 테넌트에서 확인되지 않을 경우 Azure AD UserPrincipalName 특성 값은 MOERA로 설정됩니다. Azure AD에서는 Azure AD MailNickName 특성 및 Azure AD 초기 도메인의 MOERA를 &lt;MailNickName&gt;&#64;&lt; 초기 도메인&gt;으로 계산합니다.

## <a name="verified-upn-suffix"></a>확인된 UPN 접미사
온-프레미스 UserPrincipalName 특성/대체 로그인 ID 접미사가 Azure AD 테넌트에서 확인될 경우 Azure AD UserPrincipalName 특성 값은 온-프레미스 UserPrincipalName 특성/대체 로그인 ID 값과 같아집니다.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD MailNickName 특성 값 계산
Azure AD UserPrincipalName 특성 값이 MOERA로 설정될 수 있으므로 MOERA 접두사에 해당하는 Azure AD MailNickName 특성 값이 계산되는 방식을 이해하는 것이 중요합니다.

사용자 개체가 처음으로 Azure AD 테넌트와 동기화되면 Azure AD는 지정된 순서로 다음을 확인하고, MailNickName 특성 값을 기존 첫 번째 값으로 설정합니다.

- 온-프레미스 mailNickName 특성
- 기본 SMTP 주소의 접두사
- 온-프레미스 mail 특성의 접두사
- 온-프레미스 userPrincipalName 특성/대체 로그인 ID의 접두사
- 보조 SMTP 주소의 접두사

사용자 개체에 대한 업데이트가 Azure AD 테넌트와 동기화될 때 Azure AD는 온-프레미스 mailNickName 특성 값에 대한 업데이트가 있을 때만 MailNickName 특성 값을 업데이트합니다.

>[!IMPORTANT]
>Azure AD는 온-프레미스 UserPrincipalName 특성/대체 로그인 ID 값에 대한 업데이트가 Azure AD 테넌트와 동기화될 때만 UserPrincipalName 특성 값을 다시 계산합니다. 
>
>Azure AD가 UserPrincipalName 특성을 다시 계산할 때마다 MOERA도 다시 계산합니다. 

## <a name="upn-scenarios"></a>UPN 시나리오
다음은 지정된 시나리오에 따라 UPN이 계산되는 방식을 나타내는 예제 시나리오입니다.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>시나리오 1: 확인되지 않은 UPN 접미사 - 초기 동기화

온-프레미스 사용자 개체:
- mailNickName      : &lt;not set&gt;
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us3@contoso.com`

사용자 개체를 처음으로 Azure AD 테넌트와 동기화
- Azure AD MailNickName 특성을 기본 SMTP 주소 접두사로 설정합니다.
- MOERA를 &lt;MailNickName&gt;&#64;&lt; 초기 도메인&gt;으로 설정합니다.
- Azure AD UserPrincipalName 특성을 MOERA로 설정합니다.

Azure AD 테넌트 사용자 개체:
- MailNickName      : us1           
- UserPrincipalName : us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>시나리오 2: 확인되지 않은 UPN 접미사 – 온-프레미스 mailNickName 특성 설정

온-프레미스 사용자 개체:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us3@contoso.com

온-프레미스 mailNickName 특성에 대한 업데이트를 Azure AD 테넌트와 동기화
- Azure AD MailNickName 특성을 온-프레미스 mailNickName 특성으로 업데이트합니다.
- 온-프레미스 userPrincipalName 특성에 대한 업데이트가 없으므로 Azure AD UserPrincipalName 특성도 변경되지 않습니다.

Azure AD 테넌트 사용자 개체:
- MailNickName      : us4
- UserPrincipalName : us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>시나리오 3: 확인되지 않은 UPN 접미사 – 온-프레미스 userPrincipalName 특성 업데이트

온-프레미스 사용자 개체:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- mail          : us2@contoso.com
- userPrincipalName : us5@contoso.com

온-프레미스 userPrincipalName 특성에 대한 업데이트를 Azure AD 테넌트와 동기화
- 온-프레미스 userPrincipalName 특성에 대한 업데이트는 MOERA 및 Azure AD UserPrincipalName 특성의 다시 계산을 트리거합니다.
- MOERA를 &lt;MailNickName&gt;&#64;&lt; 초기 도메인&gt;으로 설정합니다.
- Azure AD UserPrincipalName 특성을 MOERA로 설정합니다.

Azure AD 테넌트 사용자 개체:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>시나리오 4: 확인되지 않은 UPN 접미사 – 기본 SMTP 주소 및 온-프레미스 mail 특성 업데이트

온-프레미스 사용자 개체:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- mail          : us7@contoso.com
- userPrincipalName : us5@contoso.com

온-프레미스 mail 특성 및 기본 SMTP 주소에 대한 업데이트를 Azure AD 테넌트와 동기화
- 사용자 개체의 초기 동기화를 업데이트한 후, 온-프레미스 mail 특성 및 기본 SMTP 주소에 대한 업데이트는 Azure AD MailNickName 또는 UserPrincipalName 특성에 영향을 주지 않습니다.

Azure AD 테넌트 사용자 개체:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>시나리오 5: 확인된 UPN 접미사 – 온-프레미스 userPrincipalName 특성 접미사 업데이트

온-프레미스 사용자 개체:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- mail          : us7@contoso.com
- serPrincipalName  : us5@verified.contoso.com

온-프레미스 userPrincipalName 특성에 대한 업데이트를 Azure AD 테넌트와 동기화
- 온-프레미스 userPrincipalName 특성에 대한 업데이트는 Azure AD UserPrincipalName 특성의 다시 계산을 트리거합니다.
- 접미사가 Azure AD 테넌트에서 확인되므로 Azure AD UserPrincipalName 특성을 온-프레미스 userPrincipalName 특성으로 설정합니다.

Azure AD 테넌트 사용자 개체:
- MailNickName      : us4     
- UserPrincipalName : us5@verified.contoso.com

## <a name="next-steps"></a>다음 단계
- [Azure Active Directory와 온-프레미스 디렉터리 통합](active-directory-aadconnect.md)
- [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
