---
title: "Azure AD Connect - AD FS 관리 및 사용자 지정 | Microsoft Docs"
description: "Azure AD Connect를 사용한 AD FS 관리 및 Azure AD Connect와 PowerShell을 사용한 사용자 AD FS 로그인 환경의 사용자 지정입니다."
keywords: "AD FS, ADFS, AD FS 관리, AAD Connect, 연결, 로그인, AD FS 사용자 지정, 트러스트 복구, O365, 페더레이션, 신뢰 당사자"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: mtillman
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 49acea5c08a10ba3b60d0db5f05e30d573f5e507
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Azure AD Connect를 사용하여 Active Directory Federation Services 관리 및 사용자 지정
이 문서에서는 Azure AD(Azure Active Directory) Connect를 사용하여 AD FS(Active Directory Federation Services)를 관리 및 사용자 지정하는 방법을 설명합니다. 또한 AD FS 팜의 완벽한 구성을 위해 수행해야 할 수 있는 다른 일반적인 AD FS 작업을 포함합니다.

| 항목 | 포함된 내용 |
|:--- |:--- |
| **AD FS 관리** | |
| [트러스트 복구](#repairthetrust) |Office 365를 사용하여 페더레이션 트러스트를 복구하는 방법입니다. |
| [대체 로그인 ID를 사용하여 Azure AD와 페더레이션](#alternateid) | 대체 로그인 ID를 사용하여 페더레이션 구성  |
| [AD FS 서버 추가](#addadfsserver) |추가 AD FS 서버를 사용하여 AD FS 팜을 확장하는 방법입니다. |
| [AD FS 웹 응용 프로그램 프록시 서버 추가](#addwapserver) |추가 WAP(웹 응용 프로그램 프록시) 서버를 사용하여 AD FS 팜을 확장하는 방법입니다. |
| [페더레이션된 도메인을 추가합니다.](#addfeddomain) |페더레이션된 도메인을 추가하는 방법입니다. |
| [SSL 인증서 업데이트](active-directory-aadconnectfed-ssl-update.md)| AD FS 팜에 대한 SSL 인증서를 업데이트하는 방법입니다. |
| **AD FS 사용자 지정** | |
| [사용자 지정 회사 로고 또는 일러스트레이션 추가](#customlogo) |회사 로고와 일러스트레이션을 사용하여 AD FS 로그인 페이지를 사용자 지정하는 방법입니다. |
| [로그인 설명 추가](#addsignindescription) |로그인 페이지 설명을 추가하는 방법입니다. |
| [AD FS 클레임 규칙 수정](#modclaims) |다양한 페더레이션 시나리오에 대한 AD FS 클레임을 수정하는 방법입니다. |

## <a name="manage-ad-fs"></a>AD FS 관리
Azure AD Connect 마법사를 사용하여 최소한의 사용자의 개입만으로 Azure AD Connect에서 다양한 AD FS 관련 작업을 수행할 수 있습니다. 마법사를 실행하여 Azure AD Connect 설치를 완료한 후 다시 마법사를 실행하여 추가 작업을 수행할 수 있습니다.

## <a name="repairthetrust"></a>트러스트 복구 
Azure AD Connect를 사용하여 AD FS와 Azure AD 트러스트의 현재 상태를 확인하고 적절한 조치를 취하여 트러스트를 복구할 수 있습니다. 이러한 단계에 따라 Azure AD 및 AD FS 트러스트를 복구합니다.

1. 추가 작업 목록에서 **AAD 및 ADFS 트러스트 복구** 를 선택합니다.
   ![AAD 및 ADFS 트러스트 복구](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. **Azure AD에 연결** 페이지에서 Azure AD에 대한 전역 관리자 자격 증명을 제공하고 **다음**을 클릭합니다.
   ![Azure AD에 연결](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. **원격 액세스 자격 증명** 페이지에서 도메인 관리자에 대한 자격 증명을 입력합니다.

   ![원격 액세스 자격 증명](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    **다음**을 클릭한 후 Azure AD Connect가 인증서 상태를 확인하고 문제를 표시합니다.

    ![인증서의 상태](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    **구성 준비** 페이지는 트러스트를 복구하기 위해 수행할 작업 목록을 표시합니다.

    ![구성 준비](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. **설치** 를 클릭하여 트러스트를 복구합니다.

> [!NOTE]
> Azure AD Connect는 자체 서명된 인증서에 대해서만 복구 또는 조치를 취할 수 있습니다. Azure AD Connect에서 타사 인증서를 복구할 수 없습니다.

## <a name="alternateid"></a>AlternateID를 사용하여 Azure AD와 페더레이션 
온-프레미스 UPN(사용자 계정 이름) 및 클라우드 사용자 계정 이름을 동일하게 유지하는 것이 좋습니다. 온-프레미스 UPN이 라우팅할 수 없는 도메인(예: Contoso.local)을 사용하거나 로컬 응용 프로그램 종속성으로 인해 변경될 수 없는 경우 대체 로그인 ID를 설정하는 것이 좋습니다. 대체 로그인 ID를 사용하면 사용자가 UPN 이외의 특성(예: 메일)을 사용하여 로그인할 수 있는 로그인 환경을 구성할 수 있습니다. Azure AD Connect에서 선택할 수 있는 기본 사용자 계정 이름은 Active Directory의 userPrincipalName 특성입니다. 사용자 계정 이름에 대해 다른 특성을 선택하고 AD FS를 사용하여 페더레이션할 경우 Azure AD Connect는 대체 로그인 ID에 맞게 AD FS를 구성합니다. 사용자 계정 이름으로 선택할 수 있는 다른 특성은 아래와 같습니다.

![대체 ID 특성 선택 항목](media/active-directory-aadconnect-federation-management/attributeselection.png)

AD FS에 대한 대체 로그인 ID 구성은 크게 다음 두 단계로 구성됩니다.
1. **올바른 발급 클레임 집합 구성**: Azure AD 신뢰 당사자 트러스트의 발급 클레임 규칙이 선택한 UserPrincipalName 특성을 사용자의 대체 ID로 사용하도록 수정됩니다.
2. **AD FS 구성에서 대체 로그인 ID를 사용하도록 설정**: AD FS가 대체 ID를 사용하여 해당 포리스트에서 사용자를 조회할 수 있도록 AD FS 구성이 업데이트됩니다 이 구성은 Windows Server 2012 R2(KB2919355) 이상의 AD FS에 대해 지원됩니다. AD FS 서버가 2012 R2인 경우 Azure AD Connect는 필요한 KB가 있는지 확인합니다. KB가 검색되지 않으면 구성이 완료된 후에 아래와 같이 경고가 표시됩니다.

    ![2012R2의 KB 누락에 대한 경고](media/active-directory-aadconnect-federation-management/kbwarning.png)

    KB가 누락된 경우 구성 문제를 수정하려면 필요한 [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590)를 설치한 다음 [AAD 및 AD FS 트러스트 복구](#repairthetrust)를 사용하여 트러스트를 복구합니다.

> [!NOTE]
> alternateID 및 수동 구성 단계에 대한 자세한 내용은 [대체 로그인 ID 구성](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)을 참조하세요.

## <a name="addadfsserver"></a>AD FS 서버 추가 

> [!NOTE]
> AD FS 서버를 추가하려면 Azure AD Connect는 PFX 인증서가 필요합니다. 따라서 Azure AD Connect를 사용하여 AD FS 팜을 구성한 경우에만 이 작업을 수행할 수 있습니다.

1. **추가 페더레이션 서버 배포**를 선택하고 **다음**을 클릭합니다.

   ![추가 페더레이션 서버](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. **Azure AD에 연결** 페이지에서 Azure AD에 대한 전역 관리자 자격 증명을 입력하고 **다음**을 클릭합니다.

   ![Azure에 연결](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. 도메인 관리자 자격 증명을 제공합니다.

   ![도메인 관리자 자격 증명](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect는 Azure AD Connect를 사용하여 새 AD FS 팜을 구성하는 동안 제공한 PFX 파일의 암호를 요청합니다. **암호 입력** 을 클릭하여 PFX 파일에 대한 암호를 제공합니다.

   ![인증서 암호](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![SSL 인증서 지정](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. **AD FS 서버** 페이지에서 AD FS 팜에 추가할 서버 이름 또는 IP 주소를 입력합니다.

   ![AD FS 서버](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. **다음**을 클릭하고 마지막 **구성** 페이지로 이동합니다. Azure AD Connect가 AD FS 팜에 서버 추가를 완료한 후 연결을 확인하는 옵션이 제공됩니다.

   ![구성 준비](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![설치 완료](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>AD FS WAP 서버 추가 

> [!NOTE]
> WAP 서버를 추가하려면 Azure AD Connect는 PFX 인증서가 필요합니다. 따라서 Azure AD Connect를 사용하여 AD FS 팜을 구성한 경우에만 이 작업을 수행할 수 있습니다.

1. 사용 가능한 작업 목록에서 **웹 응용 프로그램 프록시 배포** 를 선택합니다.

   ![웹 응용 프로그램 프록시 배포](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Azure 전역 관리자 자격 증명을 제공합니다.

   ![Azure에 연결](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. **SSL 인증서 지정** 페이지에서 Azure AD Connect를 사용하여 AD FS 팜을 구성했을 때 제공한 PFX 파일에 대한 암호를 제공합니다.
   ![인증서 암호](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![SSL 인증서 지정](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. WAP 서버로 추가할 서버를 추가합니다. WAP 서버가 도메인에 가입되지 않을 수 있으므로 마법사가 추가 중인 서버에 대한 관리자 자격 증명을 요청합니다.

   ![관리 서버 자격 증명](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. **프록시 트러스트 자격 증명** 페이지에서 프록시 트러스트를 구성하고 AD FS 팜의 주 서버에 액세스하는 관리자 자격 증명을 제공합니다.

   ![프록시 트러스트 자격 증명](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. **구성 준비** 페이지에서 마법사는 수행할 작업 목록을 표시합니다.

   ![구성 준비](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. **설치** 를 클릭하여 구성을 완료합니다. 구성이 완료되면 마법사에서 서버에 대한 연결을 확인하는 옵션을 제공합니다. **확인** 을 클릭하여 연결을 확인합니다.

   ![설치 완료](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>페더레이션된 도메인 추가 

Azure AD Connect를 사용하면 Azure AD와 페더레이션될 도메인을 쉽게 추가할 수 있습니다. Azure AD Connect는 페더레이션에 대한 도메인을 추가하고 Azure AD와 페더레이션된 여러 도메인이 있는 경우 발급자를 올바르게 반영하기 위해 클레임 규칙을 수정합니다.

1. 페더레이션된 도메인을 추가하려면 작업 **추가 Azure AD 도메인 추가**를 선택합니다.

   ![추가 Azure AD 도메인](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. 마법사의 다음 페이지에서 Azure AD 전역 관리자 자격 증명을 제공합니다.

   ![Azure에 연결](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. **원격 액세스 자격 증명** 페이지에서 도메인 관리자 자격 증명을 제공합니다.

   ![원격 액세스 자격 증명](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. 다음 페이지에서 마법사는 온-프레미스 디렉터리를 페더레이션할 수 있는 Azure AD 도메인 목록을 제공합니다. 목록에서 도메인을 선택합니다.

   ![Azure AD 도메인](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    도메인을 선택하면 마법사는 마법사가 수행할 추가 작업 및 구성의 영향에 대한 적절한 정보를 제공합니다. 경우에 따라 Azure AD에서 아직 확인되지 않은 도메인을 선택하는 경우 마법사는 도메인을 확인하는 데 유용한 정보를 제공합니다. 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름 추가](../active-directory-domains-add-azure-portal.md) 를 참조하세요.

5. **다음**을 클릭합니다. **구성 준비** 페이지에 Azure AD Connect가 수행할 작업 목록이 표시됩니다. **설치** 를 클릭하여 구성을 완료합니다.

   ![구성 준비](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> 추가 페더레이션된 도메인의 사용자는 Azure AD에 로그인하기 전에 동기화되어야 합니다.

## <a name="ad-fs-customization"></a>AD FS 사용자 지정
다음 섹션에서는 AD FS 로그인 페이지를 사용자 지정할 때 수행해야 할 수 있는 일반적인 작업 중 일부에 대해 자세히 설명합니다.

## <a name="customlogo"></a>사용자 지정 회사 로고 또는 일러스트레이션 추가 
**로그인** 페이지에 표시되는 회사의 로고를 변경하려면 다음 Windows PowerShell cmdlet 및 구문을 사용합니다.

> [!NOTE]
> 로고의 권장 크기는 파일 크기가 10KB 이하인 96dpi에서 260x35입니다.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* 매개 변수는 필수입니다. AD FS와 함께 제공되는 기본 테마의 이름은 Default입니다.

## <a name="addsignindescription"></a>로그인 설명 추가 
**로그인 페이지**에 로그인 페이지 설명을 추가하려면 다음 Windows PowerShell cmdlet 및 구문을 사용합니다.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>AD FS 클레임 규칙 수정 
AD FS는 사용자 지정 클레임 규칙을 만드는 데 사용할 수 있는 다양한 클레임 언어를 지원합니다. 자세한 내용은 [클레임 규칙 언어의 역할](https://technet.microsoft.com/library/dd807118.aspx)을 참조하세요.

다음 섹션에서는 Azure AD 및 AD FS 페더레이션에 관련된 몇 가지 시나리오에 대한 사용자 지정 규칙을 작성할 수 있는 방법에 대해 자세히 설명합니다.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>특성에 나타나는 값에서 변경이 불가능한 ID 조건부
Azure AD Connect에서는 개체가 Azure AD에 동기화되는 경우 원본 앵커로 사용할 특성을 지정할 수 있습니다. 사용자 지정 특성의 값이 비어 있지 않은 경우 변경이 불가능한 ID 클레임을 발급하는 것이 좋습니다.

예를 들어 원본 앵커의 특성으로 **ms-ds-consistencyguid**를 선택하고 특성이 해당 항목에 대한 값을 갖는 경우 **ms-ds-consistencyguid**로 **ImmutableID**를 발급할 수 있습니다. 특성에 대한 값이 없는 경우 변경이 불가능한 ID로 **objectGuid**를 발급합니다. 다음 섹션에 설명된 대로 사용자 지정 클레임 규칙의 집합을 생성할 수 있습니다.

**규칙 1: 쿼리 특성**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

이 규칙에서는 Active Directory의 사용자에 대해 **ms-ds-consistencyguid** 및 **objectGuid**의 값을 쿼리합니다. AD FS 배포에서 적절한 저장소 이름으로 저장소 이름을 변경합니다. 또한 **objectGuid** 및 **ms-ds-consistencyguid**에 대해 정의된 대로 페더레이션에 대한 적절한 클레임 형식으로 클레임 형식을 변경합니다.

또한 **issue**가 아닌 **add**를 사용하여 엔터티에 대해 나가는 발급을 추가하지 않고 단지 중간 값으로 값을 사용할 수 있습니다. 변경이 불가능한 ID로 사용할 값을 설정한 후 이후 규칙에서 클레임을 발급합니다.

**규칙 2: 사용자에 대한 ms-ds-consistencyguid가 있는지 확인**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

이 규칙은 단순히 사용자에 대해 채워진 **ms-ds-consistencyguid**가 없는 경우 **useguid**로 설정된 **idflag**라는 임시 플래그를 정의합니다. 이 이면에 숨겨진 논리는 AD FS가 빈 클레임을 허용하지 않는다는 사실입니다. 따라서 규칙 1에서 http://contoso.com/ws/2016/02/identity/claims/objectguid 및 http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid 클레임을 추가하면 사용자에 대해 값이 채워진 경우에만 **msdsconsistencyguid** 클레임을 갖게 됩니다. 채워지지 않은 경우 AD FS는 빈 값을 갖는 것을 확인하고 즉시 삭제합니다. 모든 개체에는 **objectGuid**가 있으므로 규칙 1이 실행된 후 항상 클레임이 발생합니다.

**규칙 3: 있는 경우 변경이 불가능한 ID로 ms-ds-consistencyguid 발급**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

이는 암시적 **Exist** 확인입니다. 클레임에 대한 값이 있으면 변경이 불가능한 ID로 이를 발급합니다. 이전 예제는 **nameidentifier** 클레임을 사용합니다. 사용자 환경에서 변경이 불가능한 ID에 대한 적절한 클레임 유형으로 변경해야 합니다.

**규칙 4: ms-ds-consistencyGuid가 없는 경우 변경이 불가능한 ID로 objectGuid 발급**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

이 규칙에서는 단순히 임시 플래그 **idflag**를 확인합니다. 해당 값에 따라 클레임 발급 여부를 결정합니다.

> [!NOTE]
> 이러한 규칙 시퀀스는 중요합니다.

### <a name="sso-with-a-subdomain-upn"></a>하위 도메인 UPN을 사용한 SSO
[새 페더레이션된 도메인 추가](active-directory-aadconnect-federation-management.md#addfeddomain)에 설명된 대로 Azure AD Connect를 사용하여 페더레이션될 도메인을 둘 이상 추가할 수 있습니다. 페더레이션된 루트 도메인이 자식도 포함하기 때문에 발급자 ID는 하위 도메인이 아닌 루트 도메인과 일치해야 하므로 UPN(사용자 계정 이름) 클레임을 수정해야 합니다.

기본적으로 발급자 ID에 대한 클레임 규칙은 다음과 같이 설정됩니다.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![기본 발급자 ID 클레임](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

기본 규칙은 UPN 접미사를 가져다가 발급자 ID 클레임에 사용합니다. 예를 들어 John은 sub.contoso.com의 사용자이고 contoso.com은 Azure AD를 사용하여 페더레이션됩니다. John은 Azure AD에 로그인하는 동안 사용자 이름으로 john@sub.contoso.com을 입력합니다. AD FS에서 기본 발급자 ID 클레임 규칙은 다음과 같은 방식으로 처리합니다.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**클레임 값:** http://sub.contoso.com/adfs/services/trust/

발급자 클레임 값에 루트 도메인을 포함시키려면 다음과 일치하도록 클레임 규칙을 변경합니다.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>다음 단계
[사용자 로그인 옵션](active-directory-aadconnect-user-signin.md)에 대해 알아봅니다.
