---
title: 비갤러리 응용 프로그램에 대해 암호 Single Sign-On 구성 문제 | Microsoft Docs
description: Azure AD 응용 프로그램 갤러리에 나열되지 않은 사용자 지정 비갤러리 응용 프로그램에 대해 암호 Single Sign-On을 구성할 때 발생하는 일반적인 문제 이해
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7b671089bfac04b359717874928d6a342c44b3d4
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34069677"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>비갤러리 응용 프로그램에 대해 암호 Single Sign-On 구성 문제

이 문서는 비갤러리 응용 프로그램에 대해 **암호 Single Sign-On**을 구성할 때 발생하는 일반적인 문제를 이해하는 데 도움이 됩니다.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>응용 프로그램에 대한 로그인 필드를 캡처하는 방법

로그인 필드 캡처는 HTML 기반 로그인 페이지에 대해서만 지원되고 Flash나 기타 비HTML 기반 기술을 사용하는 로그인 페이지처럼 **비표준 로그인 페이지에 대해서는 지원되지 않습니다.**

두 가지 방법으로 사용자 지정 응용 프로그램에 대한 로그인 필드를 캡처할 수 있습니다.

-   자동 로그인 필드 캡처

-   수동 로그인 필드 캡처

**자동 로그인 필드 캡처**는 **사용자 이름 및 암호 입력에 대해 잘 알려진 DIV ID** 필드를 사용하는 경우 대부분의 HTML 기반 로그인 페이지에서 잘 작동합니다. 자동 로그인 필드 캡처는 페이지의 HTML을 스크랩하여 특정 기준과 일치하는 DIV ID를 찾은 다음, 나중에 암호를 재생할 수 있도록 이 응용 프로그램에 대한 메타데이터를 저장합니다.

**수동 로그인 필드 캡처**는 응용 프로그램 **공급업체에서 로그인에 대해 사용되는 입력 필드에 레이블을 지정하지 않은** 경우에 사용할 수 있습니다. 또한 수동 로그인 필드 캡처는 **공급업체에서 자동으로 검색할 수 없는 여러 필드를 렌더링하는 경우**에 사용할 수 있습니다. 해당 필드가 있는 페이지 위치를 알려주면 Azure AD에서는 로그인 페이지에 있는 많은 필드에 대해 데이터를 저장할 수 있습니다.

일반적으로 **자동 로그인 필드 캡처가 작동하지 않는 경우 수동 옵션을 사용해 보세요.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>응용 프로그램에 대한 로그인 필드를 자동으로 캡처하는 방법

**자동 로그인 필드 캡처**를 사용하여 응용 프로그램에 대한 **암호 기반 Single Sign-On**을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **암호 기반 로그온** 모드를 선택합니다.

9.  사용자가 로그인하려면 사용자 이름과 암호를 입력해야 하는 **로그인 URL**을 입력합니다. **입력하는 URL에서 로그인 필드가 표시되는지 확인합니다**.

10. **저장** 단추를 클릭합니다.

11. 이렇게 하면 사용자 이름과 암호 입력 상자에 해당 URL을 자동으로 가져오고, Azure AD를 통해 액세스 패널 브라우저 확장을 사용하여 해당 응용 프로그램에 암호를 안전하게 전송할 수 있습니다.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>응용 프로그램에 대한 로그인 필드를 수동으로 캡처하는 방법

로그인 필드를 수동으로 캡처하려면 먼저 액세스 패널 브라우저 확장이 설치되어 있고 **inPrivate, incognito 또는 개인 모드에서 실행 중이 아니어야 합니다.** 브라우저 확장을 설치하려면 [액세스 패널 브라우저 확장을 설치하는 방법](#i-cannot-manually-detect-sign-in-fields-for-my-application) 섹션의 단계를 수행합니다.

**수동 로그인 필드 캡처**를 사용하여 응용 프로그램에 대한 **암호 기반 Single Sign-On**을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **암호 기반 로그온** 모드를 선택합니다.

9.  사용자가 로그인하려면 사용자 이름과 암호를 입력해야 하는 **로그인 URL**을 입력합니다. **입력하는 URL에서 로그인 필드가 표시되는지 확인합니다**.

10. **저장** 단추를 클릭합니다.

11. 이렇게 하면 사용자 이름과 암호 입력 상자에 해당 URL을 자동으로 가져오고, Azure AD를 통해 액세스 패널 브라우저 확장을 사용하여 해당 응용 프로그램에 암호를 안전하게 전송할 수 있습니다. 이 작업에 실패하는 경우 12단계를 계속하여 **수동 로그인 필드 캡처를 사용하도록 로그인 모드를 변경**할 수 있습니다.

12. **&lt;앱 이름&gt; 암호 Single Sign-On 설정 구성**을 클릭합니다.

13. **로그인 필드 수동 검색** 구성 옵션을 선택합니다.

14. **Ok**를 클릭합니다.

15. **저장**을 클릭합니다.

16. 화면의 지시에 따라 액세스 패널을 사용합니다.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>“해당 URL에서 로그인 필드를 찾을 수 없습니다” 오류가 표시됨

로그인 필드 자동 검색에 실패하면 이 오류가 표시됩니다. 이 문제를 해결하려면 [응용 프로그램에 대한 로그인 필드를 수동으로 캡처하는 방법](#how-to-manually-capture-sign-in-fields-for-an-application) 섹션의 단계에 따라 수동 로그인 필드 검색을 수행하세요.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>“Single Sign-On 구성을 저장할 수 없음” 오류가 표시됨

드물지만 Single Sign-On 구성 업데이트에 실패할 수 있습니다. 문제를 해결하려면 Single Sign-On 구성을 다시 저장해 보세요.

계속 실패하는 경우 지원 케이스를 열고 [포털 알림의 세부 정보를 확인하는 방법](#i-cannot-manually-detect-sign-in-fields-for-my-application) 및 [지원 엔지니어에게 알림 세부 정보를 전송하여 도움을 얻는 방법](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) 섹션에서 수집한 정보를 제공하세요.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>응용 프로그램에 대한 로그인 필드를 수동으로 검색할 수 없음

수동 검색이 작동하지 않을 때 표시되는 동작은 다음과 같습니다.

-   수동 캡처 프로세스가 작동하는 것 같지만 캡처된 필드가 잘못됨

-   캡처 프로세스를 수행할 때 올바른 필드가 강조 표시되지 않음

-   캡처 프로세스를 통해 예상대로 응용 프로그램의 로그인 페이지로 이동하지만 아무 일도 발생하지 않음

-   수동 캡처가 작동하는 것처럼 보이지만 사용자가 액세스 패널에서 응용 프로그램으로 이동할 때 SSO가 발생하지 않음

이러한 문제가 발생하는 경우 다음 사항을 확인하세요.

-   [액세스 패널 브라우저 확장을 설치하는 방법](#how-to-install-the-access-panel-browser-extension) 섹션의 단계에 따라 최신 버전의 액세스 패널 브라우저 확장을 **설치**하고 **활성화**했는지 확인합니다.

-   브라우저가 **incognito, inPrivate 또는 개인 모드**일 때 캡처 프로세스를 시도하지 않았는지 확인합니다. 이러한 모드에서는 액세스 패널 확장이 지원되지 않습니다.

-   **incognito, inPrivate 또는 개인 모드**일 때 사용자가 액세스 패널의 응용 프로그램에 로그인하려고 하지 않았는지 확인합니다. 이러한 모드에서는 액세스 패널 확장이 지원되지 않습니다.

-   빨간색 마커가 올바른 필드 위에 있는지 확인하고 수동 캡처 프로세스를 다시 시도합니다.

-   수동 캡처 프로세스가 중단되거나 로그인 페이지에서 아무 일도 일어나지 않는 경우(위 사례 3) 수동 캡처 프로세스를 다시 시도합니다. 그러나 이번에는 프로세스를 완료한 후 **F12** 단추를 눌러 브라우저의 개발자 콘솔을 엽니다. **콘솔**을 열고 **window.location=”&lt;앱을 구성할 때 지정한 로그인 URL 입력&gt;”** 을 입력하고 **Enter** 키를 누릅니다. 그러면 캡처 프로세스를 종료하는 페이지 리디렉션이 실행되고 캡처한 필드가 저장됩니다.

위 방법으로 해결되지 않는 경우 Microsoft에서 도와드릴 수 있습니다. [포털 알림의 세부 정보를 확인하는 방법](#i-cannot-manually-detect-sign-in-fields-for-my-application) 및 [지원 엔지니어에게 알림 세부 정보를 전송하여 도움을 얻는 방법](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) 섹션(해당하는 경우)에서 수집한 정보뿐만 아니라 시도한 내용의 세부 정보와 함께 지원 케이스를 여세요.

## <a name="how-to-install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장을 설치하는 방법

액세스 패널 브라우저 확장을 설치하려면 아래 단계를 수행합니다.

1.  지원되는 브라우저 중 하나에서 [액세스 패널](https://myapps.microsoft.com)을 열고 Azure AD에서 **사용자**로 로그인합니다.

2.  액세스 패널에서 **암호-SSO 응용 프로그램**을 클릭합니다.

3.  소프트웨어를 설치하라는 프롬프트에서 **지금 설치**를 선택합니다.

4.  브라우저에 따라 다운로드 링크로 이동됩니다. 브라우저에 확장을 **추가**합니다.

5.  브라우저에서 요청하면 확장을 **사용** 또는 **허용**하도록 선택합니다.

6.  설치되면 브라우저 세션을 **다시 시작**합니다.

7.  액세스 패널에 로그인하고 암호 SSO 응용 프로그램을 **시작**할 수 있는지 확인합니다.

아래와 같은 직접 링크에서 Chrome 및 Firefox에 대한 확장을 다운로드할 수 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>포털 알림의 세부 정보를 확인하는 방법

다음 단계를 수행하여 포털 알림의 세부 정보를 확인할 수 있습니다.

1.  Azure Portal의 오른쪽 위에 있는 **알림** 아이콘(벨)을 클릭합니다.

2.  **오류** 상태(옆에 빨간색(!)이 있는)에서 알림을 선택합니다.

  >[!NOTE] **성공** 또는 **진행 중** 상태에서 알림을 클릭할 수 없습니다.
  >
  >

3.  **알림 세부 정보** 창이 열립니다.

4.  이 정보를 사용하여 문제에 대한 자세한 내용을 이해합니다.

5.  여전히 도움이 필요한 경우 문제에 대한 도움을 얻도록 이 정보를 지원 엔지니어 또는 제품 그룹과 공유할 수도 있습니다.

6.  **오류 복사** 텍스트 상자 오른쪽의 **복사** **아이콘**을 클릭하여 지원 또는 제품 그룹 엔지니어와 공유하도록 모든 알림 세부 정보를 복사합니다.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>지원 엔지니어에게 알림 세부 정보를 전송하여 도움을 얻는 방법

도움이 필요한 경우 지원 엔지니어가 신속하게 도움을 줄 수 있도록 **아래에 나열된 모든 세부 정보**를 공유하는 것은 매우 중요합니다. **스크린샷을 찍거나** **오류 복사** 텍스트 상자 오른쪽에 있는 **오류 복사 아이콘**을 클릭할 수 있습니다.

## <a name="notification-details-explained"></a>알림 세부 정보 설명

다음은 각 알림 항목이 의미하는 내용을 자세히 설명하고 각 항목의 예를 제공합니다.

### <a name="essential-notification-items"></a>중요 알림 항목

-   **제목** - 알림의 설명이 포함된 제목

    -   예제 - **응용 프로그램 프록시 설정**

-   **설명** – 작업의 결과로 발생한 문제에 대한 설명

    -   예제 - **입력한 내부 url은 이미 다른 응용 프로그램에서 사용 중입니다.**

-   **알림 ID** - 알림의 고유 ID

    -   예제 – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **클라이언트 요청 ID** -브라우저에서 만든 특정 요청 ID

    -   예제 – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **타임스탬프 UTC** – 알림이 발생한 동안의 타임스탬프(UTC)

    -   예제 – **2017-03-23T19:50:43.7583681Z**

-   **내부 트랜잭션 ID** – 시스템에서 오류를 찾는 데 사용할 수 있는 내부 ID

    -   예제 – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – 작업을 수행한 사용자

    -   예제 – **tperkins@f128.info**

-   **테넌트 ID** – 작업을 수행한 사용자가 구성원인 테넌트의 고유 ID

    -   예제 – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **사용자 개체 ID** – 작업을 수행한 사용자의 고유 ID

    -   예제 – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>자세한 알림 항목

-   **표시 이름** – **(비어 있을 수 있음)** 오류에 대한 보다 자세한 표시 이름

    -   예* - **응용 프로그램 프록시 설정**

-   **상태** - 알림의 특정 상태

    -   예* – **실패**

-   **개체 ID** – **(비어 있을 수 있음)** 작업이 수행된 개체 ID

    -   예제 – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **세부 정보** – 작업의 결과로 발생한 문제에 대한 자세한 설명

    -   예제 – **내부 url 'http://bing.com/'은 이미 사용 중이므로 유효하지 않습니다.**

-   **오류 복사** - **오류 복사** 텍스트 상자 오른쪽의 **복사 아이콘**을 클릭하여 지원 또는 제품 그룹 엔지니어와 공유하도록 모든 알림 세부 정보 복사

    -   예 – ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시를 사용하여 앱에 Single Sign-On 제공](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

