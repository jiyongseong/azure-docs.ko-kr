---
title: Azure Active Directory 조건부 액세스 설정 참조 | Microsoft Docs
description: Azure Active Directory 조건부 액세스 정책에서 지원되는 설정에 대한 개요를 확인합니다.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 341142222fb1440cf5118e6d10f5a254e73d53d9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258110"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory 조건부 액세스 설정 참조

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 리소스에 액세스하는 방법을 제어할 수 있습니다.   

이 문서에서는 조건부 액세스 정책의 다음 구성 옵션에 대한 지원 정보를 제공합니다. 

- 클라우드 응용 프로그램 할당

- 장치 플랫폼 조건 

- 클라이언트 응용 프로그램 조건

- 승인된 클라이언트 응용 프로그램 요구 사항


찾고 있는 내용이 아니면 이 문서 하단에 의견을 남겨 주세요.

## <a name="cloud-apps-assignments"></a>클라우드 앱 할당

조건부 액세스 정책을 사용하면 사용자가 [클라우드 앱](active-directory-conditional-access-conditions.md#cloud-apps)에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스 정책을 구성할 경우 하나 이상의 클라우드 앱을 선택해야 합니다. 

![정책에 대한 클라우드 앱 선택](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 응용 프로그램

Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다.

- Azure Information Protection - [자세한 정보](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online(비즈니스용 OneDrive 및 Project Online 포함)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft 팀


### <a name="other-applications"></a>다른 응용 프로그램 

Microsoft 클라우드 앱 외에도 다음과 같은 형식의 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다.

- Azure AD에 연결된 응용 프로그램

- 사전 통합되고 페더레이션된 SaaS(software as a service) 응용 프로그램

- 암호 SSO(Single Sign-On)를 사용하는 응용 프로그램

- 기간 업무 응용 프로그램

- Azure AD 응용 프로그램 프록시를 사용하는 응용 프로그램


## <a name="device-platform-condition"></a>장치 플랫폼 조건

조건부 액세스 정책에서 정책을 클라이언트의 운영 체제에 연결하는 장치 플랫폼 조건을 구성할 수 있습니다. Azure AD 조건부 액세스에서는 다음과 같은 장치 플랫폼을 지원합니다.

- Android

- iOS

- Windows Phone

- Windows

- macOS


![클라이언트 OS에 액세스 정책 연결](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>클라이언트 앱 조건 

조건부 액세스 정책에서 액세스 시도를 시작한 클라이언트 앱에 정책을 연결하도록 [클라이언트 앱](active-directory-conditional-access-conditions.md#client-apps) 조건을 구성할 수 있습니다. 다음과 같은 종류의 클라이언트 앱에서 액세스를 시도할 때 액세스를 차단하거나 부여하는 클라이언트 앱 조건을 설정합니다.

- 브라우저
- 모바일 앱 및 데스크톱 앱

![클라이언트 앱에 대한 액세스 제어](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>지원되는 브라우저 

조건부 액세스 정책에서 **브라우저**를 클라이언트 앱으로 선택할 수 있습니다.

![지원되는 브라우저의 액세스 제어](./media/active-directory-conditional-access-technical-reference/05.png)

이 설정은 모든 브라우저에서 작동합니다. 하지만 호환 장치 요구 사항과 같은 장치 정책을 충족하기 위해 다음 운영 체제와 브라우저가 지원됩니다.


| OS                     | 브라우저                            | 지원     |
| :--                    | :--                                 | :-:         |
| 윈도우 10             | Internet Explorer, Edge, Chrome     | ![확인][1] |
| Windows 8 / 8.1        | Internet Explorer, 크롬           | ![확인][1] |
| Windows 7              | Internet Explorer, 크롬           | ![확인][1] |
| iOS                    | Safari, Intune Managed Browser      | ![확인][1] |
| Android                | 크롬, Intune Managed Browser      | ![확인][1] |
| Windows Phone          | Internet Explorer, Edge             | ![확인][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![확인][1] |
| Windows Server 2016    | Chrome                              | 서비스 예정 |
| Windows Server 2012 R2 | Internet Explorer, 크롬           | ![확인][1] |
| Windows Server 2008 R2 | Internet Explorer, 크롬           | ![확인][1] |
| macOS                  | Chrome, Safari                      | ![확인][1] |



#### <a name="chrome-support"></a>Chrome 지원

**Windows 10 크리에이터 업데이트(버전 1703)** 이상에서 Chrome을 지원하려면 [이 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치합니다.

이 확장을 Chrome 브라우저에 자동으로 배포하려면 다음 레지스트리 키를 만듭니다.

|    |    |
|--- | ---|
|path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|Name | 1 |
|type | REG_SZ (String) |
|Data | ppnbnpeolgkicgegkbkbjmhlideopiji;https://clients2.google.com/service/update2/crx

**Windows 8.1 및 7**에서 Chrome을 지원하려면 다음 레지스트리 키를 만듭니다.

|    |    |
|--- | ---|
|path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Name | 1 |
|type | REG_SZ (String) |
|Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

이러한 브라우저는 장치 인증을 지원하므로 정책에 대해 장치 유효성을 검사하고 식별하는 것이 가능합니다. 브라우저가 개인 모드로 실행 중이면 장치 검사가 실패합니다. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>지원되는 모바일 응용 프로그램 및 데스크톱 클라이언트

조건부 액세스 정책에서 **모바일 앱 및 데스크톱 클라이언트**를 클라이언트 앱으로 선택할 수 있습니다.


![지원되는 모바일 앱 또는 데스크톱 클라이언트에 대한 액세스 제어](./media/active-directory-conditional-access-technical-reference/06.png)


이 설정은 다음 모바일 앱 및 데스크톱 클라이언트에서 수행하는 액세스 시도에 영향을 미칩니다. 


|클라이언트 앱|대상 서비스|플랫폼|
|---|---|---|
|Azure 원격 앱|Azure 원격 앱 서비스|Windows 10, Windows 8.1, Windows 7, iOS, Android 및 Mac OS X|
|Dynamics CRM 앱|Dynamics CRM|Windows 10, Windows 8.1, iOS 및 Android|
|메일/달력/사람 앱, Outlook 2016, Outlook 2013(최신 인증 포함)|Office 365 Exchange Online|윈도우 10|
|앱에 대한 MFA 및 위치 정책입니다. 장치 기반 정책은 지원되지 않습니다. |모든 My Apps 앱 서비스|Android 및 iOS|
|Microsoft Teams Services - Microsoft Teams 및 모든 클라이언트 앱(Windows 데스크톱, iOS, Android, WP 및 웹 클라이언트)을 지원하는 서비스를 모두 제어합니다.|Microsoft 팀|Windows 10, Windows 8.1, Windows 7, iOS, Android 및 macOS |
|Office 2016 앱, Office 2013(최신 인증 사용), OneDrive 동기화 클라이언트([참고](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 참조)|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Office 2016 앱, Universal Office 앱, Office 2013(최신 인증 사용), OneDrive 동기화 클라이언트([참고](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 참조), Office 그룹 지원은 나중에 지원될 예정입니다. SharePoint 앱 지원은 나중에 지원될 예정입니다.|Office 365 SharePoint Online|윈도우 10|
|macOS용 Office 2016(Word, Excel, PowerPoint, OneNote만 해당) 향후 제공될 예정인 비즈니스용 OneDrive 지원|Office 365 SharePoint Online|Mac OS X|
|Office 모바일 앱|Office 365 SharePoint Online|Android, iOS|
|Office Yammer 앱|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016(macOS용 Office)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Outlook 모바일 앱|Office 365 Exchange Online|Android, iOS|
|PowerBI 앱|PowerBI 서비스|Windows 10, Windows 8.1, Windows 7, Android 및 iOS|
|비즈니스용 Skype|Office 365 Exchange Online|Android, iOS |
|Visual Studio Team Services 앱|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS 및 Android|



## <a name="approved-client-app-requirement"></a>승인된 클라이언트 앱 요구 사항 

조건부 액세스 정책에서 선택한 클라우드 앱에 대한 액세스 시도가 승인된 클라이언트 앱에서 수행되도록 요구할 수 있습니다. 

![승인된 클라이언트 앱에 대한 액세스 제어](./media/active-directory-conditional-access-technical-reference/21.png)

이 설정은 다음 클라이언트 앱에 적용됩니다.


- Microsoft Intune Managed Browser
- Microsoft PowerBI
- Microsoft Invoicing
- Microsoft Launcher
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- 비즈니스용 Microsoft Skype
- Microsoft StaffHub
- Microsoft 팀
- Microsoft Visio
- Microsoft Word



**설명**

- 승인된 클라이언트 앱은 Intune 모바일 응용 프로그램 관리 기능을 지원합니다.

- **승인된 클라이언트 앱 필요** 요구 사항:

    - [장치 플랫폼 조건](#device-platforms-condition)에서는 iOS 및 Android만 지원됩니다.


## <a name="next-steps"></a>다음 단계

- 조건부 액세스에 대한 개요는 [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.
- 사용자 환경에서 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 권장 사례](active-directory-conditional-access-best-practices.md)를 참조하세요.



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


