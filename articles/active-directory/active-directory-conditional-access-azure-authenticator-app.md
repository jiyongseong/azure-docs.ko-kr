---
title: Android용 Azure Authenticator | Microsoft Docs
description: Microsoft Azure Authenticator 앱으로 로그인하여 회사 리소스에 액세스할 수 있습니다. Azure Authenticator 앱은 모바일 장치에 경고를 표시하여 보류 중인 2단계 인증 요청을 알립니다.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 349649e015aae7198d2c40efc3c1865cad087e8a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-authenticator-for-android"></a>Android용 Azure Authenticator
IT 관리자가 Microsoft Azure Authenticator로 로그인하여 회사 리소스에 액세스하도록 권장했을 수도 있습니다. 이 응용 프로그램은 다음 두 가지 로그인 옵션을 제공합니다.

* Multi-Factor Authentication을 사용하면 2단계 인증으로 회사 또는 학교 계정을 보호할 수 있습니다. 알고 있는 정보(예: 암호)를 사용하여 로그인하고 보유한 정보(이 앱의 보안 키)를 사용하여 계정을 추가로 보호합니다. Azure Authenticator 앱은 모바일 장치에 경고를 표시하여 보류 중인 2단계 인증 요청을 알립니다. 앱에서 요청을 보고 확인 또는 취소를 탭하기만 하면 됩니다. 또는 앱에 표시되는 암호를 입력하라는 메시지가 표시될 수 있습니다.
* 회사 계정을 사용하면 Android 휴대폰 또는 태블릿을 신뢰할 수 있는 장치로 전환하고 회사 응용 프로그램에 SSO(Single Sign-On)를 제공할 수 있습니다. IT 관리자가 회사 리소스에 액세스하려면 회사 계정을 추가하도록 요구할 수도 있습니다. SSO를 사용하면 한 번 로그인하여 회사에서 제공하는 모든 응용 프로그램에 자동으로 로그인할 수 있습니다.

## <a name="installing-the-azure-authenticator-app"></a>Azure Authenticator 앱 설치
Google Play 스토어에서 Azure Authenticator 앱을 설치할 수 있습니다.
삼성 Android 장치와 삼성 이외의 Android 장치에서 회사 계정을 추가하는 지침은 약간 다릅니다. 아래에는 둘 다에 대한 지침이 나와 있습니다.

## <a name="adding-the-work-account-from-samsung-android-device"></a>삼성 Android 장치에서 회사 계정 추가
### <a name="adding-the-work-account-through-the-app-home-screen"></a>앱 홈 화면을 통해 회사 계정 추가
다음 지침은 삼성 GS3 이상 휴대폰 또는 Note2 이상 태블릿에 적용할 수 있습니다.

1. 앱의 홈 화면에서 EULA(최종 사용자 사용권 계약)에 동의합니다.
2. 계정 활성화 화면에서 오른쪽에 있는 상황에 맞는 메뉴를 클릭하고 **회사 계정**을 선택합니다.
3. 계정 추가 화면에서 **회사 계정**을 선택합니다.
4. 장치 관리자 활성화 화면에서 **활성화**를 클릭합니다.
5. 개인 정보 취급 방침 화면에서 확인란을 선택하고 **확인**을 클릭합니다.
6. 작업 공간 연결 화면에서 조직이 제공한 사용자 ID를 입력하고 **연결**을 클릭합니다.
7. Azure Authenticator 앱에 로그인하려면 조직 계정과 암호를 입력하고 **로그인**을 클릭합니다.
8. MFA(다단계 인증) 정보를 표시하는 다음 화면은 추가 보안을 위한 것이며 선택 사항입니다. 회사 또는 학교에서 회사 계정을 만들 때 두 번째 인증 단계를 요구하는 경우 이 화면이 나타납니다. 추가로 계정을 인증하기 위한 지침을 제공합니다.
9. 작업 공간 연결 화면에 "**작업 공간 연결 중**" 메시지가 표시됩니다. Azure Authenticator 앱이 장치를 작업 공간에 연결하려고 합니다.
10. 다음 화면에 작업 공간 연결됨 메시지가 표시되어야 합니다.

> [!NOTE]
> 장치마다 하나의 회사 계정이 허용됩니다.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>설정 메뉴에서 회사 계정 추가
Azure Authenticator 앱을 설치한 후 Android 계정 관리자에서 회사 계정을 만들 수도 있습니다.

1. 설정 메뉴에서 **계정**으로 이동한 다음 **계정 추가**를 클릭합니다.
2. 앱 홈 화면을 통해 회사 계정 추가 절차의 3-10단계를 수행하여 회사 계정을 추가합니다.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>삼성 이외의 Android 장치에서 회사 계정 추가
### <a name="adding-the-work-account-through-the-app-home-screen"></a>앱 홈 화면을 통해 회사 계정 추가
1. 앱의 홈 화면에서 EULA(최종 사용자 사용권 계약)에 동의합니다.
2. 계정 활성화 화면에서 오른쪽에 있는 상황에 맞는 메뉴를 클릭하고 **회사 계정**을 선택합니다.
3. 계정 화면에서 **계정 추가**를 클릭합니다.
4. 계정 화면이 표시되면 **계정 추가**를 클릭합니다. 이전에 회사 계정을 만든 경우 기존 회사 계정을 보여 주는 동기화 화면이 표시됩니다. 뒤로 화살표를 눌러 홈 화면으로 돌아가면 회사 계정을 유지할 수 있습니다. 또는 제거할 계정을 선택하고 새 회사 계정을 다시 만들 수 있습니다. 작업 공간 연결 화면에서 조직이 제공한 사용자 ID를 입력하고 연결을 클릭합니다.
5. Azure Authenticator 앱에 로그인하려면 조직 계정과 암호를 입력하고 **로그인**을 클릭합니다.
6. MFA(다단계 인증) 정보를 표시하는 다음 화면은 추가 보안을 위한 것이며 선택 사항입니다. 회사 또는 학교에서 회사 계정을 만들 때 두 번째 인증 단계를 요구하는 경우 이 화면이 나타납니다. 추가로 계정을 인증하기 위한 지침을 제공합니다.
7. 다음 화면에서 **확인**을 클릭합니다. 인증서 이름을 변경하지 마세요.
   “작업 공간에 연결하는 중” 메시지가 나타납니다. Azure Authenticator 앱이 장치를 작업 공간에 연결하려고 합니다.
   다음 화면에 작업 공간 연결됨 메시지가 표시되어야 합니다.

> [!NOTE]
> 장치마다 하나의 회사 계정이 허용됩니다.
> 
> 

Azure Authenticator 앱을 설치한 후 Android 계정 관리자에서 회사 계정을 만들 수도 있습니다.

1. 설정 메뉴에서 **계정**으로 이동한 다음 **계정 추가**를 클릭합니다.
2. 앱 홈 화면을 통해 회사 계정 추가 절차의 2-7단계를 수행하여 회사 계정을 추가합니다.

### <a name="how-to-find-out-which-version-is-installed"></a>설치된 버전을 확인하는 방법
1. 장치에 설치된 Azure Authenticator 앱 및 관련된 서비스 버전을 확인할 수 있습니다.
2. 팝업 메뉴에서 **정보**를 클릭합니다.
3. 정보 화면에 장치에 설치된 앱의 서비스 및 버전이 표시됩니다.

### <a name="sending-log-files-to-report-issues"></a>로그 파일을 보내 문제 보고
1. Microsoft 지원의 지침에 따라 Azure Authenticator 앱과 관련된 인시던트를 보고하고, 인시던트 번호를 받고, 할당된 인시던트 번호에 대해 다음과 같이 로그 파일을 보냅니다.
2. 팝업 메뉴에서 **로깅**을 클릭합니다.
3. Microsoft 지원에 개설된 인시던트가 있는 경우 인시던트 번호를 적어둡니다(이후 단계에서 필요함). 지원 인시던트를 아직 만들지 않았으며 지원이 필요한 경우 [Microsoft 지원](https://support.microsoft.com/en-us/contactus) 의 지침에 따라 새 인시던트를 엽니다.
4. 로깅 화면에서 **지금 보내기**를 클릭합니다.
5. 사용할 메일 공급자를 선택합니다.
6. Microsoft 지원에 개설된 인시던트가 있는 경우 해당 문제에 할당된 지원 엔지니어에게 문의하여 로그 데이터를 보내고 인시던트에 연결시키는 방법을 확인합니다. 지원 엔지니어가 메일 주소 및 제목 줄에 대한 정보를 제공합니다. 아직 지원 인시던트가 없는 경우 Microsoft 지원의 지침에 따라 새 인시던트를 엽니다.
7. Microsoft 지원에서 받은 정보를 사용하여 **받는 사람** 줄과 **제목** 줄을 편집합니다.
8. Azure Authenticator 앱이 보내는 메일에 로그 파일을 첨부합니다. 발생한 문제를 설명하고 받는 사람 목록을 업데이트(선택 사항)한 다음 메일을 보냅니다.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>회사 계정 삭제 및 작업 공간 나가기
언제든지 직접 만든 회사 계정을 다음과 같이 제거할 수 있습니다.

**설정 메뉴에서 회사 계정을 삭제하려면**

1. 계정 관리자에서 **회사 계정**을 선택합니다.
2. 회사 계정 화면의 **일반 설정**에서 **계정 설정 – 회사 네트워크 나가기**를 선택합니다.
3. **작업 공간 연결** 화면에서 **나가기**를 선택합니다.
4. "작업 공간을 나가시겠어요?" 메시지가 표시되면 **확인** 을 클릭합니다.
5. 이렇게 하면 작업 공간에서 회사 계정이 삭제됩니다.

> [!NOTE]
> 회사 계정을 삭제할 때 계정 제거 옵션을 사용하지 않는 것이 좋습니다. 이 옵션은 이전 버전의 Android에서 작동하지 않을 수도 있습니다.
> 
> 

## <a name="uninstalling-the-app"></a>앱 제거
삼성 Android 장치에서 앱을 제거하기 전에 장치 관리자 권한을 다음과 같이 제거해야 합니다. 

1. **설정**의 **시스템** 아래에서 **보안**을 선택합니다.
2. **장치 관리** 내에서 **장치 관리자**를 클릭합니다. **Azure Authenticator** 옆에 있는 확인란이 선택 취소되었는지 확인합니다.

## <a name="troubleshooting"></a>문제 해결
**키 저장소 오류**가 표시되는 경우 잠금 화면이 PIN으로 설정되지 않았기 때문일 수 있습니다. 이 문제를 해결하려면 Azure Authenticator 앱을 제거하고 잠금 화면에 대한 PIN을 구성한 다음 앱을 다시 설치합니다.

