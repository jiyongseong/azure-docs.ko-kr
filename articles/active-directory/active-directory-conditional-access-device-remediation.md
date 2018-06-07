---
title: Windows 장치의 Azure Portal 상의 여기에서 가져올 수 없습니다 | Microsoft Docs
description: 여기에서 가져올 수 없는 위치 및 이 대화 상자에서 실행되지 않도록 방지하기 위해 확인할 점에 대해 알아봅니다.
services: active-directory
keywords: 장치 기반 조건부 액세스, 장치 등록, 장치 등록 사용, 장치 등록 및 MDM
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 5ad9b01d3821b481fe3255c821e8674dcb26b322
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Windows 장치 상에서 '여기에서 가져올 수 없습니다'

예를 들어 조직의 SharePoint Online 인트라넷에 액세스하려 할 때 *여기에서 가져올 수 없습니다*라는 메시지가 있는 페이지가 표시될 수 있습니다. 관리자가 특정 조건 하에서는 조직의 리소스에 액세스하지 못하도록 하는 조건부 액세스 정책을 구성했기 때문에 이 페이지가 표시됩니다. 이 문제를 해결하기 위해 기술 지원팀 또는 관리자에게 문의하기 전에 사용자가 직접 시도해 볼 수 있는 몇 가지 조치가 있습니다.

**Windows** 장치를 사용하는 경우 다음을 확인해야 합니다.

- 지원되는 브라우저를 사용하고 있는가?

- 장치에서 지원되는 Windows 버전을 실행하고 있는가?

- 장치가 규격을 준수하는가?






## <a name="supported-browser"></a>지원되는 브라우저

관리자가 조건부 액세스 정책을 구성한 경우, 지원되는 브라우저를 사용하여 조직의 리소스를 액세스할 수 있습니다. Windows 장치에서는 **Internet Explorer** 및 **Microsoft Edge**만 지원됩니다.

오류 페이지의 세부 정보 섹션을 보면 리소스에 액세스할 수 없는 이유가 지원되지 않는 브라우저 때문인지 여부를 쉽게 알 수 있습니다.

![지원되지 않은 브라우저에 대한 "여기에서 가져올 수 없습니다" 메시지](./media/active-directory-conditional-access-device-remediation/02.png "시나리오")

유일하게 수정된 부분은 응용 프로그램이 장치 플랫폼에 지원하는 브라우저를 사용하는 것입니다. 지원되는 브라우저의 전체 목록은 [지원되는 브라우저](active-directory-conditional-access-supported-apps.md)를 참조하세요.  


## <a name="supported-versions-of-windows"></a>지원되는 Windows 버전

장치의 Windows 운영 체제는 다음을 충족해야 합니다. 

- 장치에서 Windows 데스크톱 운영 체제를 실행하는 경우 Windows 7 이상이어야 합니다.
- 장치에서 Windows 서버 운영 체제를 실행하는 경우 Windows Server 2008 R2 이상이어야 합니다. 


## <a name="compliant-device"></a>규정 준수 장치

관리자는 규정 준수 장치에서만 조직의 리소스에 액세스할 수 있도록 조건부 액세스 정책을 구성했을 수 있습니다. 규정을 준수하려면 장치가 온-프레미스 Active Directory에 조인하거나 Azure Active Directory에 조인해야 합니다.

오류 페이지의 세부 정보 섹션을 보면 리소스에 액세스할 수 없는 이유가 규정을 준수하지 않는 브라우저 때문인지 여부를 쉽게 알 수 있습니다.
 
![등록되지 않은 장치에 대한 "여기에서 가져올 수 없습니다" 메시지](./media/active-directory-conditional-access-device-remediation/01.png "시나리오")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>장치가 온-프레미스 Active Directory에 조인되어 있습니까?

**장치가 조직의 온-프레미스 Active Directory에 조인된 경우 다음을 수행합니다.**

1. 작업 계정(Active Directory 계정)을 사용하여 Windows에 로그인하는지 확인합니다.
2. 가상 사설망(VPN) 또는 DirectAccess를 통해 회사 네트워크에 연결합니다.
3. 일단 연결한 후에 Windows 로고 키 + L 키를 사용하여 Windows 세션을 잠급니다.
4. 작업 계정 자격 증명을 입력하여 Windows 세션의 잠금을 해제합니다.
5. 몇 분 기다렸다가 다시 응용 프로그램 또는 서비스에 액세스를 시도하세요.
6. 동일한 페이지가 표시되면 **자세한 내용** 링크를 클릭한 후에 관리자에게 세부 정보를 문의합니다.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>장치가 온-프레미스 Active Directory에 조인되어 있지 않습니까?

장치가 온-프레미스 Active Directory에 조인되지 않고 Windows 10을 실행하는 경우 두 가지 옵션이 있습니다.

* Azure AD 조인을 실행합니다.
* Windows에 회사 또는 학교 계정을 추가합니다.

이러한 옵션의 차이점에 대한 자세한 내용은 [작업 공간에서 Windows 10 장치 사용](active-directory-azureadjoin-windows10-devices.md)을 참조하세요.  
장치가

- 조직에 속해 있는 경우 Azure AD 조인을 실행해야 합니다.
- 개인 장치 또는 Windows Phone인 경우 Windows에 회사 또는 학교 계정을 추가해야 합니다. 



#### <a name="azure-ad-join-on-windows-10"></a>Windows 10에서 Azure AD 조인

장치를 Azure AD에 조인하는 단계는 장치에서 실행하는 Windows 10의 버전에 따라 다릅니다. Windows 10 운영 체제의 버전을 확인하려면 다음과 같이 **winver** 명령을 실행합니다. 

![Windows 버전](./media/active-directory-conditional-access-device-remediation/03.png )


**Windows 10 1주년 업데이트(버전 1607):**

1. **설정** 앱을 엽니다.
2. **계정** > **회사 또는 학교에 액세스**를 클릭합니다.
3. **Connect**를 클릭합니다.
4. **Azure AD에 이 장치 연결**을 클릭합니다.
5. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
6. 로그아웃한 후 작업 계정으로 로그인합니다.
7. 응용 프로그램에 다시 액세스를 시도합니다.

**Windows 10 2015년 11월 업데이트(버전 1511):**

1. **설정** 앱을 엽니다.
2. **시스템** > **정보**를 클릭합니다.
3. **Azure AD 조인**을 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. 로그아웃한 후 작업 계정(Azure AD 계정)으로 로그인합니다.
6. 응용 프로그램에 다시 액세스를 시도합니다.


#### <a name="workplace-join-on-windows-81"></a>Windows 8.1에서 작업 공간 연결

장치가 도메인에 조인되지 않고 Windows 8.1을 실행하는 경우 작업 공간 연결을 수행하고 Microsoft Intune에 등록하려면 다음을 수행합니다.

1. **PC 설정**을 엽니다.
2. **네트워크** > **작업 공간**을 클릭합니다.
3. **조인**을 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. **켜기**를 클릭합니다.
6. 응용 프로그램에 다시 액세스를 시도합니다.



#### <a name="add-your-work-or-school-account-to-windows"></a>Windows에 회사 또는 학교 계정을 추가합니다. 


**Windows 10 1주년 업데이트(버전 1607):**

1. **설정** 앱을 엽니다.
2. **계정** > **회사 또는 학교에 액세스**를 클릭합니다.
3. **Connect**를 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. 응용 프로그램에 다시 액세스를 시도합니다.


**Windows 10 2015년 11월 업데이트(버전 1511):**

1. **설정** 앱을 엽니다.
2. **계정** > **사용자 계정**을 클릭합니다.
3. **회사 또는 학교 계정 추가**를 클릭합니다.
4. 조직에 인증하고 메시지가 표시되면 다단계 인증을 제공하고 제시되는 단계를 따릅니다.
5. 응용 프로그램에 다시 액세스를 시도합니다.





## <a name="next-steps"></a>다음 단계
[Azure Active Directory 조건부 액세스](active-directory-conditional-access-azure-portal.md)

