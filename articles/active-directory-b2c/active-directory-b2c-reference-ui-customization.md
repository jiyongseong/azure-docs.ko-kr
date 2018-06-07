---
title: UI(사용자 인터페이스) 사용자 지정 - Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C에서 UI(사용자 인터페이스) 사용자 지정 기능의 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: b202f30e5fb47bcd16f25c5961f8345dd0324139
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: Azure AD B2C UI(사용자 인터페이스)를 사용자 지정

사용자 환경은 고객 관련 응용 프로그램에서 가장 중요합니다.  브랜드의 모양과 느낌으로 사용자 환경을 만들어 고객 기반을 늘릴 수 있습니다. Azure AD B2C(Azure Active Directory B2C)를 사용하면 픽셀을 완벽하게 제어하여 등록, 로그인, 프로필 편집 및 암호 재설정 페이지를 사용자 지정할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명하는 페이지 UI 사용자 지정 기능은 로그인 전용 정책, 함께 제공되는 암호 재설정 페이지 및 확인 전자 메일에는 적용되지 않습니다.  이러한 기능은 대신 [회사 브랜딩 기능](../active-directory/customize-branding.md)을 사용합니다.
>
> 마찬가지로 사용자가 로그인하기 *전에* 프로필 정책 편집을 시작한 경우 이 사용자는 [회사 브랜드 기능](../active-directory/customize-branding.md)을 사용하여 사용자 지정 가능한 페이지로 리디렉션됩니다.

이 문서는 다음 항목을 설명합니다.

* 페이지 UI 사용자 지정 기능
* 페이지 UI 사용자 지정 기능을 사용하기 위해 Azure Blob Storage에 HTML 콘텐츠를 업로드하기 위한 도구
* CSS 스타일시트를 사용하여 사용자 지정할 수 있는 Azure AD B2C에서 사용되는 UI 요소
* 이 기능을 실행할 때 모범 사례입니다.

## <a name="the-page-ui-customization-feature"></a>페이지 UI 사용자 지정 기능

[정책](active-directory-b2c-reference-policies.md)을 구성하여 고객 등록, 로그인(위의 브랜딩 관련 예외 참조), 암호 재설정 및 프로필 편집 페이지의 모양과 느낌을 사용자 지정할 수 있습니다. 고객이 Azure AD B2C에 의해 제공된 응용 프로그램과 페이지 간에 이동하는 경우 원활한 환경을 유지합니다.

UI 옵션이 있는 다른 서비스와 달리, Azure AD B2C는 간단한 최신 방식의 UI 사용자 지정을 사용합니다.

작동 방식은 다음과 같습니다. Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 [CORS(원본 간 리소스 공유)](http://www.w3.org/TR/cors/)라는 최신의 방법을 사용합니다.  런타임에, 정책에서 지정한 URL에서 콘텐츠가 로드됩니다. 다른 페이지에 다른 URL을 지정할 수 있습니다. URL에서 로드된 콘텐츠가 Azure AD B2C에서 삽입된 HTML 조각에 병합된 후에는 해당 페이지가 고객에게 표시됩니다. 꼭 해야 할 일:

1. `<body>` 어딘가에 있는 빈 `<div id="api"></div>` 요소를 사용하여 잘 구성된 HTML5 콘텐츠를 만듭니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 표시합니다.
1. (허용된 CORS로)HTTPS 끝점의 콘텐츠를 호스트합니다. CORS를 구성할 때 GET 및 OPTIONS 요청 메서드를 둘 다 사용하도록 설정해야 합니다.
1. CSS를 사용하여 Azure AD B2C가 삽입하는 UI 요소의 스타일을 만듭니다.

### <a name="a-basic-example-of-customized-html"></a>사용자 지정된 HTML의 기본 예제

다음 예제는 이 기능을 테스트하는 데 사용할 수 있는 가장 기본적인 HTML 콘텐츠입니다. [도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)를 사용하여 이 콘텐츠를 Azure Blob Storage에 업로드하고 구성합니다. 그런 다음 각 페이지의 양식화되지 않은 기본 단추 및 양식 필드가 표시되고 작동하는지 확인할 수 있습니다.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>UI 사용자 지정 기능 테스트하기

샘플 HTML 및 CSS 콘텐츠를 사용하여 UI 사용자 지정 기능을 사용하시겠습니까?  Azure Blob Storage에 샘플 콘텐츠를 업로드하고 구성하는 [도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)를 제공하고 있습니다.

> [!NOTE]
> 웹 서버, CDN, AWS S3, 파일 공유 시스템 등 어느 곳에나 UI 콘텐츠를 호스트할 수 있습니다. CORS가 설정된 공개적으로 사용 가능한 HTTPS 끝점에 콘텐츠가 호스트된다면 계속 진행하세요. Azure Blob 저장소를 설명 목적으로만 사용합니다.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Azure AD B2C에 의해 포함된 UI 조각

다음 섹션에는 Azure AD B2C가 콘텐츠의 `<div id="api"></div>` 요소에 병합하는 HTML5 조각이 나와 있습니다. **HTML 5 콘텐츠에 이러한 조각을 삽입하지 마십시오.** Azure AD B2C 서비스는 런타임에 삽입합니다. 사용자 고유의 CSS 스타일 시트를 디자인할 때 이러한 조각을 참조하세요.

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>"ID 공급자 선택 페이지"에 삽입되는 조각

이 페이지는 등록 또는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 단추에는 Facebook, Google+ 또는 로컬 계정(메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자가 포함됩니다.

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>"로컬 계정 등록 페이지"에 삽입되는 조각

이 페이지에는 전자 메일 주소 또는 사용자 이름을 기준으로 하는 로컬 계정 등록 양식이 있습니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>"소셜 계정 등록 페이지"에 삽입되는 조각

이 페이지는 Facebook 또는 Google+와 같은 소셜 ID 공급자에서 기존 계정을 사용하여 등록하는 경우 나타날 수 있습니다.  등록 양식을 사용하여 최종 사용자로부터 추가 정보를 수집해야 할 때 사용됩니다. 이 페이지는 암호 입력 필드를 제외하고 로컬 계정 등록 페이지와 유사합니다.(이전 섹션에 표시됨)

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>"통합 등록 또는 로그인 페이지"에 삽입되는 조각

이 페이지는 Facebook 또는 Google+ 또는 로컬 계정과 같은 소셜 ID 공급자를 사용할 수 있는 고객의 등록 및 로그인을 모두 다룹니다.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>"Multi-Factor Authentication 페이지"에 삽입되는 조각

이 페이지에서 등록 또는 로그인하는 동안 사용자가 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>"오류 페이지"에 삽입되는 조각

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>HTML 콘텐츠 지역화

HTML 콘텐츠를 지역화하는 방법은 두 가지가 있습니다. 하나는 [언어 사용자 지정](active-directory-b2c-reference-language-customization.md)을 켜는 것입니다. 이 기능을 사용하도록 설정하면 Azure AD B2C가 Open ID Connect 매개 변수 `ui-locales`를 끝점으로 전달할 수 있습니다.  콘텐츠 서버는 이 매개 변수를 사용하여 언어 관련 사용자 지정된 HTML 페이지를 제공할 수 있습니다.

또는 사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정할 수 있습니다. 와일드 카드 값 `{Culture:RFC5646}`을 사용하는 경우 적합한 구조를 호출합니다.  예를 들어 사용자 지정 페이지 URI가 다음과 같다고 가정해 봅시다.

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
`fr`로 페이지를 로드할 수 있습니다. 페이지가 HTML 및 CSS 콘텐츠를 가져올 때 다음 위치에서 가져옵니다.
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="things-to-remember-when-building-your-own-content"></a>고유의 콘텐츠를 작성할 때 유의할 점

페이지 UI 사용자 지정 기능을 사용하려는 경우 다음 모범 사례를 검토합니다.

* Azure AD B2C의 기본 콘텐츠를 복사하거나 수정하려고 하지 마세요. 처음부터 HTML5 콘텐츠를 작성하고 기본 콘텐츠를 참조로 사용하는 것이 가장 좋습니다.
* 보안상의 이유로 콘텐츠에 JavaScript를 포함할 수 없습니다. 대부분의 필요한 것을 즉시 사용할 수 있어야 합니다. 그렇지 않으면 [사용자 의견](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) 을 통해 새로운 기능을 요청합니다.
* 지원되는 브라우저 버전:
  * Internet Explorer 11, 10, Edge
  * Internet Explorer 9, 8에 대한 지원 제한
  * Google Chrome 42.0 이상
  * Mozilla Firefox 38.0 이상
* Azure AD B2C에서 삽입된 HTML에서 생성되는 POST 작업을 방해하므로 HTML에 `<form>` 태그가 포함되지 않도록 합니다.
