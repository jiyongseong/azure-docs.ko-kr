---
title: 사용자 지정 정책으로 사용자 경험의 UI 사용자 지정 | Microsoft Docs
description: Azure Active Directory B2C 사용자 지정 정책에 대해 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 4fe9e90996c56773480eb147e5aef7475453fe43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>사용자 지정 정책으로 사용자 경험의 UI 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> 이 문서는 UI 사용자 지정이 작동하는 방식과 Identity Experience Framework를 사용하여 Azure AD B2C 사용자 정의 정책을 사용하도록 설정하는 방법에 대해 자세히 설명합니다.


비즈니스-소비자 솔루션에서는 원활한 사용자 환경이 핵심입니다. 원활한 사용자 환경이란 장치 또는 브라우저에 관계 없이 서비스를 통한 사용자 경험을, 사용 중인 고객 서비스의 경험과 구분할 수 없는 환경을 말합니다.

## <a name="understand-the-cors-way-for-ui-customization"></a>UI 사용자 지정을 위한 CORS 이해

Azure AD B2C를 사용하면 사용자 지정 정책을 사용하여 Azure AD B2C에서 제공하고 표시하는 다양한 페이지에서 사용자 환경(UX)의 모양과 느낌을 사용자 지정할 수 있습니다.

이러한 용도로, Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 현대적이고 표준 방식인 [크로스-원본 자원 공유(CORS)](http://www.w3.org/TR/cors/)를 사용하여 HTML5/CSS 템플릿을 가리키도록 사용자 지정 정책에서 지정한 특정 URL에서 사용자 지정 콘텐츠를 로드합니다. CORS는 리소스의 출처가 된 도메인 외부의 다른 도메인에서 웹 페이지의 글꼴과 같은 제한된 자원을 요청할 수 있는 메커니즘입니다.

제한된 텍스트 및 이미지를 제공한 솔루션에서 템플릿 페이지를 소유하고 레이아웃과 느낌을 제한적으로 제어하여 원활한 환경을 달성하는 데 어려움을 겪었던 구식의 전통적인 방식에 비해, CORS 방식은 HTML5 및 CSS를 지원하며 다음이 가능합니다.

- 콘텐츠를 호스팅하고 솔루션은 클라이언트 측 스크립트를 사용하여 컨트롤을 삽입합니다.
- 레이아웃과 느낌의 모든 픽셀을 완벽하게 제어할 수 있습니다.

HTML5/CSS 파일을 적절하게 선별하여 원하는 만큼 콘텐츠 페이지를 제공할 수 있습니다.

> [!NOTE]
> 보안상의 이유로 사용자 지정에 JavaScript의 사용이 현재 차단됩니다. JavaScript를 차단 해제하려면 Azure AD B2C 테넌트의 사용자 지정 도메인 이름을 사용해야 합니다.

각 HTML5/CSS 템플릿에서 HTML 또는 콘텐츠 페이지에서 필수 `<div id=”api”>` 요소에 해당하는 *anchor* 요소를 제공합니다(이 내용은 이후 설명됨). Azure AD B2C에서는 모든 콘텐츠 페이지에 이 특정 div가 있어야 합니다.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

페이지의 Azure AD B2C 관련 콘텐츠는 이 div에 삽입되는 반면, 나머지 페이지는 사용자가 제어할 수 있습니다. Azure AD B2C JavaScript 코드는 콘텐츠를 가져와서 HTML을 이 특정 div 요소에 삽입합니다. Azure AD B2C가 계정 선택기 컨트롤, 로그인 컨트롤, 다중 요소(현재 전화 기반) 컨트롤 및 특성 컬렉션 컨트롤 등을 적절히 삽입합니다. Azure AD B2C는 모든 컨트롤이 HTML5 호환 및 액세스 가능하고 모든 컨트롤을 완전히 스타일 지정할 수 있으며 컨트롤 버전이 이전으로 되돌려지지 않도록 합니다.

병합된 콘텐츠는 결국 소비자에게 동적 문서로 표시됩니다.

모든 내용이 예상대로 작동하도록 하려면 다음을 수행해야 합니다.

- 콘텐츠가 HTML5 호환되고 액세스 가능해야 함
- 콘텐츠 서버가 CORS에 대해 사용하도록 설정되어야 함
- HTTPS를 통해 콘텐츠를 제공
- 모든 링크 및 CSS 콘텐츠에 대해 절대 URL(예: https://yourdomain/content)을 사용합니다.

> [!TIP]
> 콘텐츠를 호스트하는 사이트에 CORS가 활성화되어 있고 CORS 요청을 테스트했는지 확인하려면 http://test-cors.org/ 사이트를 사용할 수 있습니다. 이 사이트 덕분에 CORS 요청을 원격 서버로 보내거나(CORS가 지원되는지 테스트) CORS 요청을 테스트 서버로 보낼 수 있습니다(CORS의 특정 기능 탐색).

> [!TIP]
> http://enable-cors.org/ 사이트는 또한 CORS에 대한 유용한 리소스를 구성합니다.

이 CORS 기반 접근 방식 덕분에 최종 사용자는 Azure AD B2C에서 제공하는 페이지와 응용 프로그램 간에 일관된 경험을 얻게 됩니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

필수 요소로, 저장소 계정을 만들어야 합니다. Azure Blob Storage 계정을 만들려면 Azure 구독이 필요합니다. [Azure 웹 사이트](https://azure.microsoft.com/pricing/free-trial/)에서 무료 평가판에 등록할 수 있습니다.

1. 브라우저 세션을 열고 [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 관리자 자격 증명으로 로그인합니다.
3. **리소스 만들기** > **저장소** > **저장소 계정**을 클릭합니다.  **저장소 계정 만들기** 창이 열립니다.
4. **이름**에 저장소 계정의 이름을 제공합니다(예: *contoso369b2c*). 이 값은 나중에 *storageAccountName*으로 참조됩니다.
5. 가격 책정 계층, 리소스 그룹 및 구독에 적절한 선택 항목을 고릅니다. **시작 보드에 고정** 옵션이 선택되었는지 확인합니다. **만들기**를 클릭합니다.
6. 시작 보드에 돌아가서 만든 저장소 계정을 클릭합니다.
7. **서비스** 섹션에서 **Blob**을 클릭합니다. **Blob Service 창**이 열립니다.
8. **+ 컨테이너**를 클릭합니다.
9. **이름**에 컨테이너의 이름을 입력합니다(예:*b2c*). 이 값은 나중에 *containerName*으로 참조됩니다.
9. **액세스 유형**으로 **Blob**을 선택합니다. **만들기**를 클릭합니다.
10. 만든 컨테이너가 **Blob Service 창**의 목록에 표시됩니다.
11. **Blob** 창을 닫습니다.
12. **저장소 계정 창**에서 **키** 아이콘을 클릭합니다. **액세스 키 창**이 열립니다.  
13. **key1**의 값을 기록해 둡니다. 이 값은 나중에 *key1*로 참조됩니다.

## <a name="downloading-the-helper-tool"></a>도우미 도구 다운로드

1.  [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)에서 도우미 도구를 다운로드합니다.
2.  *B2C-AzureBlobStorage-Client-master.zip* 파일을 로컬 컴퓨터에 저장합니다.
3.  로컬 디스크에 B2C-AzureBlobStorage-Client-master.zip 파일의 콘텐츠를 추출합니다(예: *B2C-AzureBlobStorage-Client-master* 폴더 아래에 만든 **UI-Customization-Pack** 폴더 아래).
4.  폴더를 열고 그 안에 있는 아카이브 파일 *B2CAzureStorageClient.zip*의 콘텐츠를 추출합니다.

## <a name="upload-the-ui-customization-pack-sample-files"></a>UI-Customization-Pack 샘플 파일 업로드

1.  Windows 탐색기를 사용하여 이전 섹션에서 생성한 *UI-Customization-Pack* 폴더 아래에 있는 *B2C-AzureBlobStorage-Client-master* 폴더로 이동합니다.
2.  *B2CAzureStorageClient.exe* 파일을 실행합니다. 이 프로그램은 저장소 계정에 사용자가 지정한 디렉터리의 모든 파일을 업로드하고 해당 파일에 대한 CORS 액세스가 가능하도록 합니다.
3.  메시지가 표시되면 다음을 지정합니다. a.  저장소 계정의 이름 *storageAccountName*(예: *contoso369b2c*).
    나.  Azure Blob Storage의 기본 액세스 키 *key1*(예: *contoso369b2c*).
    다.  Storage Blob Storage 컨테이너의 이름 *containerName*(예: *b2c*).
    d.  *Starter-Pack* 샘플 파일의 경로(예: *..\B2CTemplates\wingtiptoys*).

이전 단계를 따랐다면 **wingtiptoys**라는 가상의 회사에 대한 *UI-Customization-Pack*의 HTML5 및 CSS 파일은 이제 사용자의 저장소 계정을 가리킵니다.  Azure Portal에서 관련 컨테이너 창을 열어 콘텐츠를 제대로 업로드했는지 확인할 수 있습니다. 또는 브라우저에서 페이지에 액세스하여 콘텐츠를 제대로 업로드했는지 확인할 수 있습니다. 자세한 내용은 [Azure Active Directory B2C: 페이지 UI(사용자 인터페이스) 사용자 지정 기능을 설명하기 위해 사용된 도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)를 참조하세요.

## <a name="ensure-the-storage-account-has-cors-enabled"></a>저장소 계정에 CORS가 활성화되었는지 확인

콘텐츠를 로드하려면 Azure AD B2C에 대한 엔드포인트에서 CORS(원본 간 리소스 공유)를 활성화해야 합니다. 이는 Azure AD B2C가 페이지를 제공하는 도메인이 아닌 다른 도메인에서 콘텐츠가 호스팅되기 때문입니다.

콘텐츠를 호스팅하는 저장소에 CORS를 사용할 수 있는지 확인하려면 다음 단계를 진행합니다.

1. 브라우저 세션을 열고 저장소 계정 `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`에서 해당 위치의 전체 URL을 사용하여 *unified.html* 페이지로 이동합니다. 예: https://contoso369b2c.blob.core.windows.net/b2c/unified.html
2. http://test-cors.org로 이동합니다. 이 사이트를 통해 사용 중인 페이지에 CORS가 사용하도록 설정되었는지 확인할 수 있습니다.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. **원격 URL**에서 unified.html 콘텐츠에 대한 전체 URL을 입력하고 **요청 보내기**를 클릭합니다.
4. **결과** 섹션의 출력에 CORS가 활성화되어 있음을 나타내는 *XHR 상태: 200*이 포함되어 있는지 확인합니다.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
이제 저장소 계정에는 *Starter-Pack*의 다음 wingtiptoys 템플릿이 포함된 그림에 *b2c*라는 이름의 Blob 컨테이너가 포함되어야 합니다.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

다음 표에서는 이전 HTML5 페이지의 용도를 설명합니다.

| HTML5 템플릿 | 설명 |
|----------------|-------------|
| *phonefactor.html* | 이 페이지를 Multi-Factor Authentication 페이지에 대한 템플릿으로 사용할 수 있습니다. |
| *resetpassword.html* | 이 페이지를 암호 찾기 페이지에 대한 템플릿으로 사용할 수 있습니다. |
| *selfasserted.html* | 이 페이지를 소셜 계정 등록 페이지, 로컬 계정 등록 페이지 또는 로컬 계정 로그인 페이지에 대한 템플릿을 사용할 수 있습니다. |
| *unified.html* | 이 페이지를 통합 등록 또는 로그인 페이지에 대한 템플릿으로 사용할 수 있습니다. |
| *updateprofile.html* | 이 페이지를 프로필 업데이트 페이지에 대한 템플릿으로 사용할 수 있습니다. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>HTML5/CSS 템플릿에 대한 링크를 사용자 경험에 추가

사용자 지정 정책을 직접 편집하여 HTML5/CSS 템플릿에 대한 링크를 사용자 경험에 추가할 수 있습니다.

사용자 경험에 사용할 사용자 지정 HTML5/CSS 템플릿은 해당 사용자 경험에 사용할 수 있는 콘텐츠 정의 목록에 지정해야 합니다. 이를 위해 사용자 지정 정책 XML 파일의 *<BuildingBlocks>* 섹션 아래에 선택 사항인 *<ContentDefinitions>* XML 요소를 선언해야 합니다.

다음 표는 Azure AD B2C ID 경험 엔진이 인식할 수 있는 콘텐츠 정의 ID 집합과 이에 관련된 페이지 유형을 설명합니다.

| 콘텐츠 정의 ID | 설명 |
|-----------------------|-------------|
| *api.error* | **오류 페이지**입니다. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. |
| *api.idpselections* | **ID 공급자 선택 페이지**입니다. 이 페이지는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 공급자는 Facebook, Google+ 또는 로컬 계정(이메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자, 엔터프라이즈 ID 공급자입니다. |
| *api.idpselections.signup* | **등록을 위한 ID 공급자 선택 페이지**입니다. 이 페이지는 등록하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 공급자는 Facebook, Google+ 또는 로컬 계정(이메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자, 엔터프라이즈 ID 공급자입니다. |
| *api.localaccountpasswordreset* | **암호 찾기 페이지**. 이 페이지에는 사용자가 자신의 암호 재설정을 시작하기 위해 입력해야 하는 양식이 들어 있습니다.  |
| *api.localaccountsignin* | **로컬 계정 로그인 페이지**. 이 페이지는 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정에 로그인하는 경우 사용자가 작성해야 하는 등록 양식을 포함합니다. 양식에는 텍스트 입력 상자 및 암호 입력란이 포함될 수 있습니다. |
| *api.localaccountsignup* | **로컬 계정 등록 페이지**. 이 페이지는 메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정을 등록하는 경우 사용자가 작성해야 하는 등록 양식을 포함합니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다. |
| *api.phonefactor* | **Multi-Factor Authentication 페이지**. 이 페이지에서 등록 또는 로그인하는 동안 사용자가 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. |
| *api.selfasserted* | **소셜 계정 등록 페이지**. 이 페이지는 Facebook 또는 Google+와 같은 소셜 ID 공급자에서 기존 계정을 사용하여 등록하는 경우 사용자가 작성해야 하는 등록 양식을 포함합니다. 이 페이지는 암호 입력 필드를 제외하고 이전 소셜 계정 등록 페이지와 유사합니다. |
| *api.selfasserted.profileupdate* | **프로필 업데이트 페이지**. 이 페이지에는 사용자가 자신의 프로필을 업데이트하기 위해 사용할 수 있는 양식이 들어 있습니다. 이 페이지는 암호 입력 필드를 제외하고 이전 소셜 계정 등록 페이지와 유사합니다. |
| *api.signuporsignin* | **통합 등록 또는 로그인 페이지**입니다.  이 페이지는 Facebook, Google+ 또는 로컬 계정과 같은 소셜 ID 공급자, 엔터프라이즈 ID 공급자를 사용할 수 있는 사용자의 등록 및 로그인을 모두 다룹니다.

## <a name="next-steps"></a>다음 단계
[참조: B2C에서 사용자 지정 정책과 Identity Experience Framework가 작동하는 방식 이해](active-directory-b2c-reference-custom-policies-understanding-contents.md)
