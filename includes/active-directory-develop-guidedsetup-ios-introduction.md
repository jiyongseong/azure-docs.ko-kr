
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>iOS 응용 프로그램에서 Microsoft Graph API 호출

이 가이드에서는 기본 iOS 응용 프로그램(Swift)이 Microsoft Azure AD(Azure Active Directory) v2.0 끝점의 액세스 토큰이 필요한 API를 호출하는 방법을 보여 줍니다. 이 가이드는 액세스 토큰을 가져오고 Microsoft Graph API 및 기타 API를 호출하는 데 사용하는 방법을 설명합니다.

이 가이드의 연습을 완료하면 Azure AD가 있는 모든 회사 또는 조직에서 제공하는 보호된 API를 응용 프로그램에서 호출할 수 있습니다. 응용 프로그램에서 outlook.com, live.com 등과 같은 개인 계정뿐 아니라 회사 또는 학교 계정을 사용하여 보호된 API를 호출할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
- 이 가이드에서 만드는 샘플에는 XCode 버전 8.x가 필요합니다. [iTunes 웹 사이트](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 다운로드 URL")에서 XCode를 다운로드할 수 있습니다.
- 패키지 관리에는 [Carthage](https://github.com/Carthage/Carthage) 종속성 관리자가 필요합니다.

## <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 가이드의 작동 방식](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

이 가이드의 응용 프로그램 예제에서는 iOS 응용 프로그램이 Azure AD v2.0 끝점에서 토큰을 수락하는 Microsoft Graph API 또는 Web API를 쿼리하도록 합니다. 이 시나리오에서는 토큰이 **인증** 헤더를 사용하여 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>보호된 Web API에 액세스하기 위한 토큰 획득 처리

사용자가 인증한 후 응용 프로그램 예제는 토큰을 수신합니다. 이 토큰은 Azure AD v2.0 끝점으로 보호되는 Microsoft Graph API 또는 Web API를 쿼리하는 데 사용됩니다.

Microsoft Graph와 같은 API는 액세스 토큰이 있어야만 특정 리소스에 대한 액세스를 허용합니다. 토큰은 사용자 프로필을 읽고, 사용자 일정에 액세스하고, 메일을 보내는 등을 하는 데 필요합니다. 응용 프로그램에서는 MSAL을 사용하고 API 범위를 지정하여 액세스 토큰을 요청할 수 있습니다. 액세스 토큰은 보호되는 리소스에 대한 모든 호출에 사용될 수 있도록 HTTP **인증** 헤더에 추가됩니다.

MSAL은 사용자를 대신해 액세스 토큰 캐싱 및 새로 고침을 관리하므로 응용 프로그램에서 이러한 작업을 수행할 필요가 없습니다.


## <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS용 Microsoft 인증 라이브러리 미리 보기|

