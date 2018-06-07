---
title: Azure Active Directory 조건부 액세스의 위치 조건 | Microsoft Docs
description: 위치 조건을 사용하여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대한 액세스를 제어하는 방법을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 842fe8c194f1c88c7dabb073e0fa7b7806d92d44
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 위치 조건 

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스 정책의 위치 조건을 사용하여 사용자의 네트워크 위치에 액세스 제어 설정을 연결할 수 있습니다.

이 문서에서는 위치 조건을 구성하는 데 필요한 정보를 제공합니다. 

## <a name="locations"></a>위치

Azure AD에서는 공용 인터넷의 어디에서든지 장치, 앱 및 서비스에 대해 Single Sign-On을 수행할 수 있습니다. 위치 조건을 사용하여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대한 액세스를 제어할 수 있습니다. 위치 조건에 대한 일반적인 사용 사례는 다음과 같습니다.

- 회사 네트워크 외부에 있을 때 서비스에 액세스하는 사용자에 대해 다단계 인증 요구  

- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자에 대한 액세스 차단 

위치는 명명된 위치 또는 다단계 인증의 신뢰할 수 있는 IP를 나타내는 네트워크 위치에 대한 레이블입니다.


## <a name="named-locations"></a>명명된 위치 

명명된 위치를 사용하여 IP 주소 범위, 국가 및 지역의 논리적 그룹을 만들 수 있습니다. 

조건부 액세스 페이지의 **관리** 섹션에서 명명된 위치에 액세스할 수 있습니다.

![위치](./media/active-directory-conditional-access-locations/02.png)

 


명명된 위치의 구성 요소는 다음과 같습니다.

![위치](./media/active-directory-conditional-access-locations/42.png)

- **이름** - 명명된 위치의 표시 이름입니다.

- **IP 범위** - CIDR 형식으로 나타낸 하나 이상의 IP 주소 범위입니다.

- **신뢰할 수 있는 위치로 표시** - 신뢰할 수 있는 위치를 나타내기 위해 명명된 위치에 대해 설정할 수 있는 플래그입니다. 일반적으로 신뢰할 수 있는 위치는 IT 부서에서 제어하는 네트워크 영역입니다. 신뢰할 수 있는 명명된 위치는 조건부 액세스뿐만 아니라, Azure ID 보호 및 Azure AD 보안 보고서에서 [거짓 긍정](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1)을 줄이는 데도 사용됩니다.

- **국가/지역** - 이 옵션을 사용하여 명명된 위치를 정의할 하나 이상의 국가를 선택할 수 있습니다. 

- **알 수 없는 영역 포함** - 일부 IP 주소는 특정 국가에 매핑되지 않습니다. 이 옵션을 사용하면 이러한 IP 주소를 명명된 위치에 포함할지를 선택할 수 있습니다. 명명된 위치를 사용하는 정책이 알 수 없는 위치에 적용될지도 선택할 수 있습니다.

구성할 수 있는 명명된 위치의 수는 Azure AD에서 관련된 개체의 크기에 따라 제한됩니다. 다음을 구성할 수 있습니다.

- 최대 1,200개 IP 범위로 명명된 하나의 위치

- 각각에 한 개의 IP 범위로 할당된 최대 90개의 명명된 위치




## <a name="trusted-ips"></a>신뢰할 수 있는 IP

[다단계 인증 서비스 설정](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)에서 조직의 로컬 인트라넷을 나타내는 IP 주소 범위를 구성할 수도 있습니다. 이 기능을 사용하면 최대 50개의 IP 주소 범위를 구성할 수 있습니다. IP 주소 범위는 CIDR 형식입니다. 자세한 내용은 [신뢰할 수 있는 IP](authentication/howto-mfa-mfasettings.md#trusted-ips)를 참조하세요.  

신뢰할 수 있는 IP를 구성한 경우 위치 조건에 대한 위치 목록에서 **MFA에서 신뢰할 수 있는 IP**로 표시됩니다.   

### <a name="skipping-multi-factor-authentication"></a>다단계 인증 건너뛰기

다단계 인증 서비스 설정 페이지에서 **인트라넷의 페더레이션 사용자로부터 발생한 요청인 경우 다단계 인증 건너뛰기**를 선택하여 회사 인트라넷 사용자를 식별할 수 있습니다. 이 설정은 AD FS에서 발급한 내부 회사 네트워크 클레임이 신뢰되며, 사용자를 회사 네트워크에 있는 것으로 식별하는 데 사용됨을 나타냅니다. 자세한 내용은 [조건부 액세스를 사용하여 신뢰할 수 있는 IP 기능을 사용하도록 설정](authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)을 참조하세요.

명명된 위치를 포함하는 이 옵션을 선택하면, **MFA에서 신뢰할 수 있는 IP**가 해당 옵션이 선택된 모든 정책에 적용됩니다.

세션 수명이 긴 모바일 및 데스크톱 응용 프로그램의 경우 조건부 액세스가 주기적으로 다시 계산됩니다. 기본값은 1시간에 한 번입니다. 내부 회사 네트워크 클레임이 초기 인증 시에만 발급되면 Azure AD에 신뢰할 수 있는 IP 범위 목록이 없을 수 있습니다. 이 경우 사용자가 여전히 회사 네트워크에 있는지를 확인하는 것이 더 어렵습니다.

1. 사용자의 IP 주소가 신뢰할 수 있는 IP 범위 중 하나에 있는지 여부를 확인합니다.

2. 사용자 IP 주소의 처음 3개 8진수가 초기 인증 IP 주소의 처음 3개 8진수와 일치하는지 여부를 확인합니다. 이때 내부 회사 네트워크 클레임이 처음에 발급되었고 사용자 위치가 유효한지 검사되었으므로 IP 주소는 초기 인증과 비교됩니다.

두 단계가 모두 실패하는 경우 사용자는 더 이상 신뢰할 수 있는 IP에 있다고 간주되지 않습니다.



## <a name="location-condition-configuration"></a>위치 조건 구성

위치 조건을 구성할 때 다음을 구분하기 위해 옵션도 제공됩니다.

- 임의의 위치 
- 신뢰할 수 있는 모든 위치
- 선택한 위치

![위치](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>임의의 위치

기본적으로 **모든 위치**를 선택하면 정책이 모든 IP 주소(인터넷의 모든 주소)에 적용됩니다. 이 설정은 명명된 위치로 구성한 IP 주소로 제한되지 않습니다. **모든 위치**를 선택해도 정책에서 특정 위치를 제외할 수 있습니다. 예를 들어, 신뢰할 수 있는 위치를 제외한 모든 위치에 정책을 적용하여, 회사 네트워크를 제외한 모든 위치로 범위를 설정할 수 있습니다.

### <a name="all-trusted-locations"></a>신뢰할 수 있는 모든 위치

이 옵션은 다음에 적용됩니다.

- 신뢰할 수 있는 위치로 표시된 모든 위치
- **MFA에서 신뢰할 수 있는 IP**(구성된 경우)


### <a name="selected-locations"></a>선택한 위치

이 옵션을 사용하여 하나 이상의 명명된 위치를 선택할 수 있습니다. 이 설정이 적용된 정책의 경우, 선택한 위치에서만 연결해야 합니다. **선택**을 클릭하면 명명된 네트워크 목록을 표시하는 명명된 네트워크 선택 컨트롤이 열립니다. 이 목록은 네트워크 위치가 신뢰할 수 있는 위치로 표시된 경우에도 표시됩니다. 다단계 인증 서비스 설정 페이지에서 구성할 수 있는 IP 설정을 포함하기 위해 **MFA에서 신뢰할 수 있는 IP**라는 명명된 위치가 사용됩니다.

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="when-is-a-location-evaluated"></a>위치가 평가되는 시기

조건부 액세스 정책은 다음 경우에 평가됩니다. 

- 사용자가 웹앱, 모바일 또는 데스크톱 응용 프로그램에 처음 로그인합니다. 

- 최신 인증을 사용하는 모바일 또는 데스크톱 응용 프로그램에서 새로 고침 토큰을 사용하여 새 액세스 토큰을 확보합니다. 기본적으로 한 시간에 한 번 수행됩니다. 

즉, 최신 인증을 사용하는 모바일 및 데스크톱 응용 프로그램의 네트워크 위치가 변경되면 1시간 이내에 위치 변경이 감지됩니다. 최신 인증을 사용하지 않는 모바일 및 데스크톱 응용 프로그램의 경우 토큰 요청마다 정책이 적용됩니다. 요청 빈도는 응용 프로그램에 따라 다를 수 있습니다. 마찬가지로 웹 응용 프로그램의 경우 정책은 처음 로그인할 때 적용되며 웹 응용 프로그램 세션 수명 기간 동안 유효합니다. 응용 프로그램 간에 세션 수명이 달라서 정책 평가 사이의 시간도 달라질 수 있습니다. 응용 프로그램이 새 로그인 토큰을 요청할 때마다 정책이 적용됩니다.


기본적으로 Azure AD는 시간 단위로 토큰을 발급합니다. 회사 네트워크 외부로 이동한 후 1시간 이내에 최신 인증을 사용하는 응용 프로그램에 대해 정책이 적용됩니다.


### <a name="user-ip-address"></a>사용자 IP 주소

정책 평가에 사용되는 IP 주소는 사용자의 공용 IP 주소입니다. 개인 네트워크에 있는 장치의 경우, 이 IP는 인트라넷에 있는 사용자 장치의 클라이언트 IP가 아니고, 공용 인터넷에 연결하기 위해 네트워크에서 사용하는 주소입니다. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>명명된 위치의 대량 업로드 및 다운로드

명명된 위치를 만들거나 업데이트하는 경우 대량 업데이트를 위해 IP 주소가 포함된 CSV 파일을 업로드 또는 다운로드할 수 있습니다. 업로드는 목록의 IP 범위를 파일의 IP 범위와 바꿉니다. 파일의 각 행에는 하나의 IP 주소 범위가 CIDR 형식으로 포함되어 있습니다. 


### <a name="cloud-proxies-and-vpns"></a>클라우드 프록시 및 VPN 

클라우드 호스티드 프록시 또는 VPN 솔루션을 사용하면 정책을 평가하는 동안 Azure AD가 사용하는 IP 주소는 프록시의 IP 주소입니다. 사용자를 포함하는 XFF(X-Forwarded-For) 헤더는 신뢰할 수 있는 원본에서 가져왔다는 확인이 없어서 IP 주소를 속이는 방법을 제공할 수 있으므로 사용되지 않습니다. 

클라우드 프록시가 있는 경우 도메인 가입 장치를 요구하는 데 사용되는 정책이나 AD FS의 내부 회사 네트워크 클레임이 사용될 수 있습니다.



### <a name="api-support-and-powershell"></a>API 지원 및 PowerShell 

API 및 PowerShell은 명명된 위치 또는 조건부 액세스 정책에 대해 아직 지원되지 않습니다.





## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 
