---
title: Azure Active Directory에서 엔터프라이즈 상태 로밍 설정 문제 해결 | Microsoft Docs
description: 설정 및 앱 데이터 동기화에 대한 IT 관리자의 질문에 답변합니다.
services: active-directory
keywords: 엔터프라이즈 상태 로밍 설정, windows 클라우드, 엔터프라이즈 상태 로밍에 대한 질문과 대답
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: d9936da3ee1b0cfb4ee6e834a838798a35140620
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 상태 로밍 설정 문제 해결

이 항목은 엔터프라이즈 상태 로밍과 관련된 문제를 진단하고 해결하는 방법에 관한 정보와 알려진 문제 목록을 제공합니다.

## <a name="preliminary-steps-for-troubleshooting"></a>문제 해결을 위한 준비 단계 
문제 해결을 시작하기 전에 사용자 및 장치가 적절하게 구성되었고 Enterprise State Roaming의 모든 요구 사항을 충족하는지 확인합니다. 

1. 최신 업데이트된 Windows 10와 최소 버전 1511(OS 빌드 10586 이상)이 장치에 설치되어 있습니다. 
2. 장치는 Azure AD나 하이브리드 Azure AD에 조인되어 있습니다. 자세한 내용은 [Azure AD에서 장치를 제어하는 방법](device-management-introduction.md)을 참조하세요.
3. [Enterprise State Roaming을 사용하려면](active-directory-windows-enterprise-state-roaming-enable.md)에 설명된 대로 Azure AD에서 테넌트에 대해 **Enterprise State Roaming**을 활성화합니다. 모든 사용자 또는 선택한 사용자 그룹만을 로밍할 수 있습니다.
4. 이미 사용자에게는 Azure Active Directory Premium 라이선스가 할당되어야 합니다.  
25. Enterprise State Roaming 기능에 액세스하려면 장치를 다시 시작해야 하고 사용자가 다시 로그인해야 합니다.

## <a name="information-to-include-when-you-need-help"></a>도움이 필요한 경우 포함할 정보
아래 지침 관련 문제를 해결할 수 없는 경우 지원 엔지니어에 문의할 수 있습니다. 문의할 때 다음 정보를 포함합니다.

* **오류에 대한 일반적인 설명**: 사용자가 본 오류 메시지가 있습니까? 오류 메시지가 있는 경우 알아낸 예기치 않은 동작을 자세히 설명합니다. 동기화를 위해 어떤 기능을 사용할 수 있고 사용자가 동기화해야 하는 이유는 무엇입니까? 다수의 기능이 동기화되지 않거나 동기화되지 않는 기능은 분리됩니까?
* **영향을 받는 사용자** – 동기화가 한 사용자 또는 여러 사용자에 대 해 성공/실패합니까? 사용자당 몇 개의 장치가 관련됩니까? 모두 동기화되지 않거나 일부만 동기화되고 일부는 동기화되지 않습니까?
* **사용자에 대한 정보**: 장치에 로그인을 사용하는 데 사용자가 사용하는 ID는 무엇입니까? 사용자는 장치에 어떻게 로그인합니까? 동기화가 허용된 보안 그룹의 일원입니까? 
* **장치에 대한 정보** – 이 장치가 Azure AD에 조인되었거나 도메인에 조인되었습니까? 장치의 빌드는 무엇입니까? 최신 업데이트는 무엇입니까?
- **날짜/시간/표준 시간대** – 오류(표준 시간대 포함)가 나타나는 경우 정확한 날짜 및 시간은 무엇입니까?

이 정보를 포함하면 최대한 빨리 문제를 해결하는데 도움이 됩니다.

## <a name="troubleshooting-and-diagnosing-issues"></a>문제 해결 및 문제 진단
이 섹션에서는 엔터프라이즈 상태 로밍에 관련된 문제를 해결하고 진단하는 방법을 제시합니다.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>동기화 및 "설정 동기화" 설정 페이지 확인 

1. Enterprise State Roaming을 허용하도록 구성되어 있는 도메인에 Windows 10 PC를 연결한 후에 회사 계정으로 로그인합니다. **설정** > **계정** > **설정 동기화**로 이동하여 동기화 및 개별 설정이 켜져 있는지 그리고 설정 페이지의 맨 위에서 사용자가 자신의 작업 계정과 동기화 중임을 나타내는지 확인합니다. **설정** > **계정** > **사용자 정보**에서 동일한 계정이 로그인 계정으로 사용되고 있는지 확인합니다. 
2. 작업 표시줄을 화면 오른쪽이나 위쪽으로 이동하는 등 원본 컴퓨터에서 일부 내용을 변경하여 여러 대의 컴퓨터에서 동기화가 작동하는지 확인합니다. 변경 사항이 5분 이내에 두 번째 컴퓨터에 적용되는지 확인합니다. 
  * 화면을 잠갔다가 해제하면(Win + L) 동기화 트리거에 도움이 될 수 있습니다.
  * Enterprise State Roaming은 컴퓨터 계정이 아닌 사용자 계정에 연결되어 있으므로 동기화가 작동하려면 두 PC에서 동일한 계정을 사용하여 로그인해야 합니다.

**잠재적인 문제**: **설정** 페이지에서 제어를 사용할 수 없는 경우 "일부 Windows 기능은 Microsoft 계정 또는 회사 계정을 사용하는 경우에만 사용할 수 있습니다."라는 메시지가 표시됩니다. 장치가 도메인에 조인되어 Azure AD에 등록되도록 설정되었지만 Azure AD에 성공적으로 인증되지 않은 경우 이러한 문제가 발생할 수 있습니다. 이는 장치 정책을 적용해야 하지만 이 응용 프로그램이 비동기적으로 작동하여 몇 시간 지연되는 것이 원인일 수 있습니다. 

### <a name="verify-the-device-registration-status"></a>장치 등록 상태 확인
엔터프라이즈 상태 로밍은 Azure AD에 등록된 장치가 필요합니다. 엔터프라이즈 상태 로밍에는 해당하지 않지만 아래 지침을 따르면 Windows 10 클라이언트 등록 여부뿐만 아니라, 지문, Azure AD 설정 URL, NGC 상태 및 기타 정보를 확인하는 데 도움이 됩니다.

1.  비관리자 권한 명령 프롬프트를 엽니다. Windows에서 이 작업을 수행하려면 실행 시작 관리자(Win + R)를 열고 "cmd"를 입력하여 엽니다.
2.  명령 프롬프트가 열리면 "*dsregcmd.exe /status*"을 입력합니다.
3.  예상된 출력에서 **AzureAdJoined** 필드 값은 "YES", **WamDefaultSet** 필드 값도 "YES"여야 하고 **WamDefaultGUID** 필드 값은 끝에 "(AzureAd)"가 있는 GUID여야 합니다.

**잠재적인 문제**: **WamDefaultSet** 및 **AzureAdJoined** 필드 값이 모두 "NO"이고 장치가 도메인에 가입되고 Azure AD에 등록되었으며 장치가 동기화되지 않습니다. 이것이 표시되는 경우 장치를 Azure AD에 연결할 때 장치는 정책을 적용하거나 장치에 대한 인증이 실패할 때까지 대기해야 합니다. 사용자는 정책이 적용될 때까지 몇 시간을 대기해야 할 수 있습니다. 기타 문제 해결 단계에는 로그아웃하고 다시 로그인함으로써 자동 등록 다시 시도 또는 작업 Scheduler에서 작업 시작이 포함될 수 있습니다. 경우에 따라 관리자 권한 명령 프롬프트 창에서 "*dsregcmd.exe /leave*"를 실행하고 다시 부팅하여 등록을 다시 시도하면 이 문제 해결에 도움이 될 수 있습니다.


**잠재적인 문제**: **AzureAdSettingsUrl** 필드가 비어있고 장치가 동기화되지 않습니다. 사용자가 Azure Active Directory 포털에서 엔터프라이즈 상태 로밍을 사용하기 전에 장치에 마지막으로 로그인했을 수 있습니다. 장치를 다시 시작하고 사용자에게 로그인하게 합니다. 필요한 경우 포털에서 IT 관리자에게 설정 및 엔터프라이즈 앱 데이터 동기화가 허용되는 사용자를 비활성화했다가 다시 활성화하도록 합니다. 다시 활성화되면 장치를 다시 시작하고 사용자에게 로그인하게 합니다. 그래도 문제가 해결되지 않으면 장치 인증서의 경우 **AzureAdSettingsUrl**이 비어 있을 수 있습니다. 이 경우 관리자 권한 명령 프롬프트 창에서 "*dsregcmd.exe /leave*"를 실행하고 다시 부팅하여 등록을 다시 시도하면 이 문제 해결에 도움이 될 수 있습니다.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>엔터프라이즈 상태 로밍 및 Multi-Factor Authentication 
특정 조건에서 엔터프라이즈 상태 로밍은 Azure Multi-Factor Authentication이 구성된 경우 데이터를 동기화하는 데 실패할 수 있습니다. 이러한 현상에 대한 자세한 내용은 [KB3193683](https://support.microsoft.com/kb/3193683) 지원 문서를 참조하세요. 

**잠재적인 문제**: Azure Active Directory 포털에서 Multi-Factor Authentication을 필요로 하도록 장치가 구성된 경우 암호를 사용하여 Windows 10 장치에 로그인하는 동안 설정을 동기화하는 데 실패할 수 있습니다. 이러한 형식의 Multi-Factor Authentication 구성은 Azure 관리자 계정을 보호하도록 계획되었습니다. 관리자 사용자는 Office 365와 같은 다른 Azure 서비스에 액세스하는 동안 Microsoft Passport for Work PIN을 사용하거나 Multi-Factor Authentication을 완료하여 해당 Windows 10 장치에 로그인함으로써 동기화할 수 있습니다.

**잠재적인 문제**: 관리자가 Active Directory Federation Services Multi-Factor Authentication 조건부 액세스 정책을 구성하고 장치에 대한 액세스 토큰이 만료된 경우 동기화는 실패할 수 있습니다. Office 365와 같은 다른 Azure 서비스에 액세스하는 동안 Microsoft Passport for Work PIN을 사용하거나 Multi-Factor Authentication을 완료하여 로그인 및 로그아웃해야 합니다.

### <a name="event-viewer"></a>이벤트 뷰어
고급 문제 해결의 경우 이벤트 뷰어를 사용하여 특정 오류를 찾을 수 있습니다. 이는 아래 표에 설명되어 있습니다. 이벤트는 이벤트 뷰어 > 응용 프로그램 및 서비스 로그 > **Microsoft** > **Windows** > **SettingSync** 그리고 동기화와 ID 관련 문제는 **Microsoft** > **Windows** > **Azure AD**에서 찾을 수 있습니다.


## <a name="known-issues"></a>알려진 문제

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>MDM 소프트웨어를 사용하여 앱이 로드된 장치에서는 동기화가 작동하지 않음

Windows 10 기념일 업데이트(버전 1607)를 실행하는 장치에 영향을 줍니다. SettingSync Azure 로그에 있는 이벤트 뷰어에서 80070259 오류가 있는 이벤트 ID 6013이 자주 나타납니다.

**권장 작업**  
Windows 10 v1607 클라이언트에 2016년 8월 23일 누적 업데이트가 있는지 확인합니다([KB3176934](https://support.microsoft.com/kb/3176934) OS 빌드 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer 즐겨찾기가 동기화되지 않음

Windows 10 11월 업데이트(버전 1511)를 실행하는 장치에 영향을 줍니다.

**권장 작업**  
Windows 10 v1511 클라이언트에 2016년 7월 누적 업데이트가 있는지 확인합니다([KB3172985](https://support.microsoft.com/kb/3172985) OS 빌드 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Windows 정보 보호로 보호된 데이터뿐만 아니라 테마가 동기화되지 않음 

데이터 누출을 방지하기 위해 [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip)으로 보호되는 데이터는 Windows 10 기념일 업데이트를 사용하는 장치에 대한 엔터프라이즈 상태 로밍을 통해 동기화되지 않습니다.



**권장 작업**  
없음. 이 문제는 추후에 있을 Windows에서 해결될 것입니다.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>날짜, 시간 및 지역 설정이 도메인 가입 장치에서 동기화되지 않음 
  
도메인에 가입된 장치는 날짜, 시간 및 지역 설정: 자동 시간에 대해 동기화되지 않습니다. 자동 사용을 사용하면 다른 날짜, 시간 및 지역 설정을 무시하고 이들 설정이 동기화되지 않을 수 있습니다. 

**권장 작업**  
없음. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>암호 동기화 시 UAC 프롬프트

암호를 동기화하도록 구성된 무선 NIC와 함께 Windows 10 11월 업데이트(버전 1511)를 실행하는 장치에 영향을 줍니다.

**권장 작업**  
Windows 10 v1511 클라이언트에 누적 업데이트([KB3140743](https://support.microsoft.com/kb/3140743) OS 빌드 10586.494)가 있는지 확인합니다.

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>동기화는 로그인 시 스마트 카드를 사용하는 장치에서 작동하지 않음
스마트 카드 또는 가상 스마트 카드를 사용하여 Windows 장치에 로그인하려고 하면 설정 동기화가 중지됩니다.     

**권장 작업**  
없음. 이 문제는 추후에 있을 Windows에서 해결될 것입니다.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>도메인에 가입된 장치가 회사 네트워크에서 벗어난 후 동기화되지 않음     
Azure AD에 등록된 도메인 가입 장치는 오랜 시간 장치가 현장을 벗어나 있고 도메인 인증을 완료할 수 없는 경우 동기화가 실패할 수 있습니다.

**권장 작업**  
동기화를 다시 시작할 수 있도록 장치를 회사 네트워크에 연결합니다.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD 조인 장치가 동기화되지 않고 사용자 계정 이름에 대/소문자가 혼합되어 있습니다.
 사용자의 UPN에 대/소문자가 혼합되어 있고(예: username 대신 UserName) 사용자가 Windows 10 빌드 10586에서 14393으로 업그레이드한 Azure AD 조인 장치에 있는 경우 사용자 장치는 동기화에 실패할 수 있습니다. 

**권장 작업**  
사용자가 장치의 연결을 해제하고 클라우드에 다시 연결해야 합니다. 이렇게 하려면 로컬 관리자 사용자로 로그인하고 **설정** > **시스템** > **정보**로 이동하여 장치의 연결을 해제한 후 "회사 또는 학교에서 관리 또는 연결 끊기"를 선택합니다. 아래 파일을 정리한 다음 **설정** > **시스템** > **정보**에서 "회사 또는 학교에 연결"을 선택하여 장치를 Azure AD에 다시 연결합니다. 장치를 계속 Azure Active Directory에 연결하고 흐름을 완료합니다.

정리 단계에서 다음 파일을 정리합니다.
- Settings.dat in `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account` 폴더에 있는 모든 파일

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>이벤트 ID 6065: 80070533 이 사용자는 현재 이 계정을 사용할 수 없기 때문에 로그인 할 수 없음  
사용자의 자격 증명이 만료된 경우 이벤트 뷰어의 SettingSync/Debug 로그 아래에 이 오류가 표시될 수 있습니다. 또한 테넌트가 AzureRMS를 자동으로 프로비전하지 않은 경우에도 이 오류가 발생할 수 있습니다. 

**권장 작업**  
첫 번째 경우에는 사용자가 자격 증명을 업데이트하고 새 자격 증명으로 장치에 로그인하게 합니다. AzureRMS 문제를 해결하려면 [KB3193791](https://support.microsoft.com/kb/3193791)에 나열된 단계를 수행합니다. 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>이벤트 ID 1098: 오류: 0xCAA5001C 토큰 브로커 작업 실패  
AAD/Operational 로그에 있는 이벤트 뷰어에서 이벤트 1104: AAD 클라우드 AP 플러그 인 호출이 반환된 토큰 가져오기 오류: 0xC000005F와 함께 이 오류가 나타날 수 있습니다. 이 문제는 권한 또는 소유권 특성이 없는 경우 발생합니다.  

**권장 작업**  
[KB3196528](https://support.microsoft.com/kb/3196528)에 나열된 단계를 수행합니다.  



## <a name="next-steps"></a>다음 단계

- 피드백을 제공하고 엔터프라이즈 상태 로밍을 개선하는 방법에 대해 제안하려면 [사용자 음성](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) 포럼을 사용합니다.

- 자세한 내용은 [엔터프라이즈 상태 로밍 개요](active-directory-windows-enterprise-state-roaming-overview.md)를 참조하세요. 

## <a name="related-topics"></a>관련된 항목
* [엔터프라이즈 상태 로밍 개요](active-directory-windows-enterprise-state-roaming-overview.md)
* [Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화](active-directory-windows-enterprise-state-roaming-enable.md)
* [설정 및 데이터 로밍 FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [설정 동기화에 대한 그룹 정책 및 MDM 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 로밍 설정 참조](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
