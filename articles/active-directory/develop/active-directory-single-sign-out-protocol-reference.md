---
title: Azure Single Sign Out SAML 프로토콜 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory에서 Single Sign-Out SAML 프로토콜을 설명합니다.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 9ec99ffc64138cf1cd94e0f11077cdc5d86dbc57
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="single-sign-out-saml-protocol"></a>Single Sign-Out SAML 프로토콜
Azure AD(Azure Active Directory)에서는 SAML 2.0 웹 브라우저 Single Sign-Out 프로필을 지원합니다. Single Sign-Out이 제대로 작동하려면, 응용 프로그램 등록 중에 응용 프로그램에 대한 **LogoutURL**이 Azure AD에 명시적으로 등록되어야 합니다. Azure AD는 LogoutURL을 사용하여 로그아웃된 사용자를 리디렉션합니다.

이 다이어그램에서는 Azure AD Single Sign-Out 프로세스의 워크플로를 보여 줍니다.

![Single Sign Out 워크플로](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
클라우드 서비스는 세션이 종료되었음을 나타내는 `LogoutRequest` 메시지를 Azure AD로 보냅니다. 다음 발췌문은 샘플 `LogoutRequest` 요소를 보여 줍니다.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Azure AD로 전송된 `LogoutRequest` 요소에는 다음 특성이 필요합니다.

* `ID` : 로그아웃 요청을 식별합니다. `ID` 값은 숫자로 시작할 수 없습니다. 일반적인 방법은 **id** 를 GUID의 문자열 표현에 추가하는 것입니다.
* `Version` : 이 요소의 값을 **2.0**으로 설정합니다. 이 값은 필수입니다.
* `IssueInstant` : UTC(Coordinate Universal Time) 값과 [라운드 트립 형식("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)을 포함하는 `DateTime` 문자열입니다. Azure AD에는 이 형식의 값이 필요하지만 강제 적용하지는 않습니다.

### <a name="issuer"></a>발급자
`LogoutRequest`의 `Issuer` 요소는 Azure AD에서 클라우드 서비스의 **ServicePrincipalNames** 중 하나와 정확히 일치해야 합니다. 일반적으로 응용 프로그램 등록 중에 지정된 **앱 ID URI** 로 설정됩니다.

### <a name="nameid"></a>NameID
`NameID` 요소 값은 로그아웃한 사용자의 `NameID`와 정확히 일치해야 합니다.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD는 `LogoutRequest` 요소에 대한 응답에 `LogoutResponse`를 보냅니다. 다음 발췌문은 샘플 `LogoutResponse`를 보여 줍니다.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD는 `LogoutResponse` 요소에 `ID`, `Version` 및 `IssueInstant` 값을 설정합니다. 또한 `InResponseTo` 요소를 응답을 도출한 `LogoutRequest`의 `ID` 특성 값으로 설정합니다.

### <a name="issuer"></a>발급자
Azure AD는 이 값을 `https://login.microsoftonline.com/<TenantIdGUID>/`로 설정합니다. 여기서 <TenantIdGUID>은(는) Azure AD 테넌트의 테넌트 ID입니다.

`Issuer` 요소 값을 평가하려면 응용 프로그램 등록 중에 제공한 **앱 ID URI** 값을 사용합니다.

### <a name="status"></a>상태
Azure AD에서는 `Status` 요소의 `StatusCode` 요소를 사용하여 로그아웃의 성공 여부를 나타냅니다. 로그아웃 시도가 실패하면 `StatusCode` 요소는 사용자 지정 오류 메시지를 포함할 수도 있습니다.