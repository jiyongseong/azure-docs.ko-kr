---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 794fd51c38f66b24193c7da7a145d58f7a225b30
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
## <a name="setting-up-your-web-server-or-project"></a>웹 서버 또는 프로젝트 설정

> 이 샘플의 프로젝트를 다운로드하고 싶으세요? 
> - [Visual Studio 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> 또는
> - 로컬 웹 서버(예: python)용 [프로젝트 파일 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)
>
> 그런 후 [구성 단계](#register-your-application)로 건너뛰어 실행 전에 코드 샘플을 구성합니다.

## <a name="prerequisites"></a>필수 조건
이 단계별 설치를 실행하려면 [Python http.server](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core) 또는 IIS Express와 같은 로컬 웹 서버가 [Visual Studio 2017](https://www.visualstudio.com/downloads/)과 통합되어 있어야 합니다. 

이 가이드의 지침은 Python 및 Visual Studio 2017 둘 다를 기준으로 하지만 다른 개발 환경 또는 웹 서버를 사용해도 무방합니다.

## <a name="create-your-project"></a>프로젝트 만들기 

> ### <a name="option-1-visual-studio"></a>옵션 1: Visual Studio 
> Visual Studio를 사용하며 새 프로젝트를 만드는 경우 아래 단계에 따라 새 Visual Studio 솔루션을 만드세요.
> 1.    Visual Studio:  `File` > `New` > `Project`
> 2.    `Visual C#\Web`에서 `ASP.NET Web Application (.NET Framework)`을 선택합니다.
> 3.    응용 프로그램의 이름을 지정하고 *확인*을 클릭합니다.
> 4.    `New ASP.NET Web Application`에서 `Empty`를 선택합니다.

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>옵션 2: Python/기타 웹 서버
> [Python](https://www.python.org/downloads/)을 설치했는지 확인한 후 아래 단계를 따릅니다.
> - 응용 프로그램을 호스트할 폴더를 만듭니다.


## <a name="create-your-single-page-applications-ui"></a>단일 페이지 응용 프로그램의 UI 만들기
1.  JavaScript SPA에 대한 *index.html* 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 마우스 오른쪽 단추로 클릭하고 `Add` > `New Item` > `HTML page`을 선택하고 이름을 index.html로 지정합니다.
2.  페이지에 다음 코드를 추가합니다.
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
