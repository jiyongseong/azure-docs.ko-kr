---
title: Visual Studio에서 Key Vault Connected Service 시작하기(ASP.NET Projects) | Microsoft Docs
description: 이 자습서를 통해 ASP.NET 또는 ASP.NET Core 웹 응용 프로그램에 Key Vault 지원을 추가하는 방법을 배울 수 있습니다.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Visual Studio에서 Key Vault 연결된 서비스 시작 (ASP.NET Projects)

> [!div class="op_single_selector"]
> - [시작](vs-key-vault-aspnet-get-started.md)
> - [변경된 내용](vs-key-vault-aspnet-what-happened.md)

이 기사에서는 Visual Studio에서 **연결된 서비스 추가** 명령을 통해 Key Vault를 ASP.NET MVC 프로젝트에 추가한 후 추가 지침을 제공합니다. 프로젝트에 아직 서비스를 추가하지 않은 경우 [Visual Studio 연결된 서비스를 사용하여 웹 응용 프로그램에 Key Vault 추가](vs-key-vault-add-connected-service.md)에 설명된 지침에 따라 언제든지 그렇게 할 수 있습니다.

연결된 서비스를 추가할 때 프로젝트에 대한 변경 사항은 [ASP.NET 프로젝트에 무엇이 변경되었나요?](vs-key-vault-aspnet-core-what-happened.md)를 참조하세요.

## <a name="after-you-connect"></a>연결 후

1. Azure의 Key Vault에 비밀을 추가합니다. 포털에서 올바른 위치로 이동하려면 **이 Key Vault에 저장된 비밀 관리** 링크를 클릭합니다. 페이지 또는 프로젝트를 닫은 후 [Azure Portal](https://portal.azure.com)에서 **보안** 아래의 **모든 서비스**를 선택하고, **Key Vault**를 선택한 후 방금 만든 Key Vault를 선택하여 그것으로 이동합니다.

   ![포털로 이동](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 만든 키 자격 증명 모음의 Key Vault 섹션에서 **비밀**을 선택한 다음, **생성/가져오기**를 선택합니다.

   ![비밀 생성/가져오기](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. **MySecret**과 같은 비밀을 입력하고 임의의 문자열을 테스트로 제공한 다음 **만들기** 버튼을 선택합니다.

   ![비밀 만들기](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (선택 사항) 다른 비밀을 입력합니다. 단, 이번에는 **비밀-MySecret**이라는 이름을 지정하여 범주에 포함시킵니다. 이 구문은 **MySecret**이란 비밀을 포함하는 **비밀** 범주를 지정합니다.

1. web.config를 다음과 같이 수정합니다. 키는 Key Vault의 암호 값을 사용하여 AzureKeyVault ConfigurationBuilder로 바뀌는 자리 표시자입니다.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. HomeController의 컨트롤러 메서드 정보에서 다음 줄을 추가하여 암호를 검색하고 ViewBag에 저장합니다.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. About.cshtml 보기에서 다음을 추가하여 비밀 값을 표시합니다(테스트 전용).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

축하합니다. 이제 Key Vault를 사용하여 안전하게 저장된 비밀에 액세스할 수 있도록 웹앱을 활성화했습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Key Vault와 관련된 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **TYPE THE RESOURCE GROUP NAME:** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

# <a name="next-steps"></a>다음 단계

[Key Vault 개발자 가이드](key-vault-developers-guide.md)에서 Key Vault로 개발하는 방법에 대해 자세히 알아봅니다.