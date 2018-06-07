---
title: Azure Active Directory의 연결된 서비스를 사용하여 오류를 진단하는 방법
description: Active Directory의 연결된 서비스에서 호환되지 않는 인증 유형 검색
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Azure Active Directory의 연결된 서비스를 사용하여 오류 진단

이전 인증 코드를 검색하는 동안 Azure Active Directory 연결 서버에서 호환되지 않는 인증 유형을 검색했습니다.

프로젝트에서 이전 인증 코드를 제대로 감지하려면 프로젝트를 빌드해야 합니다.  이 오류가 발생했고 프로젝트에 이전 인증 코드가 없는 경우 다시 작성한 다음 다시 시도합니다.

## <a name="project-types"></a>프로젝트 형식

이 연결된 서비스는 프로젝트에 올바른 인증 논리를 삽입할 수 있도록 사용자가 개발 중인 프로젝트 형식을 확인합니다. 프로젝트의 `ApiController`에서 파생되는 컨트롤러가 있으면 프로젝트가 WebAPI 프로젝트로 간주됩니다. 프로젝트의 `MVC.Controller`에서 파생되는 컨트롤러만 있으면 프로젝트가 MVC 프로젝트로 간주됩니다. 연결된 서비스는 다른 모든 프로젝트 형식을 지원하지 않습니다.

## <a name="compatible-authentication-code"></a>호환 가능한 인증 코드

또한 연결된 서비스는 이전에 이 서비스로 구성되었거나 이 서비스와 호환되는 인증 설정이 있는지도 확인합니다. 모든 설정이 있는 경우 재진입 사례로 간주되고 연결된 서비스가 열릴 때 해당 설정이 표시됩니다.  설정이 일부만 있으면 오류 사례로 간주됩니다.

MVC 프로젝트에서 이 연결된 서비스는 이전에 서비스를 사용한 결과에 따라 다음과 같은 설정을 확인합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

또한 이 연결된 서비스는 이전에 서비스를 사용한 결과에 따라 다음과 같은 Web API 프로젝트 설정을 확인합니다.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>호환되지 않는 인증 코드

마지막으로, 이 연결된 서비스에서는 이전 버전의 Visual Studio를 사용하여 구성된 인증 코드의 버전을 감지하려고 합니다. 이 오류가 발생한 경우에는 프로젝트에 호환되지 않는 인증 코드가 있음을 의미합니다. 연결된 서비스에서는 이전 버전의 Visual Studio에서 다음과 같은 인증 유형을 감지합니다.

* Windows 인증
* 개별 사용자 계정
* 조직 계정

MVC 프로젝트에서 Windows 인증을 감지하기 위해 연결된 서비스는 사용자의 `web.config` 파일에서 `authentication` 요소를 찾습니다.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

웹 API 프로젝트에서 Windows 인증을 감지하기 위해 연결된 서비스는 프로젝트의 `.csproj` 파일에서 `IISExpressWindowsAuthentication` 요소를 찾습니다.

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

개별 사용자 계정 인증을 감지하기 위해 연결된 서비스는 사용자의 `packages.config` 파일에서 패키지 요소를 찾습니다.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

조직 계정 인증의 이전 양식을 감지하기 위해 연결된 서비스는 `web.config`에서 다음 요소를 찾습니다.

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

인증 유형을 변경하려면 호환되지 않는 인증 유형을 제거하고 연결된 서비스를 다시 추가해 보세요.

자세한 내용은 [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하세요.