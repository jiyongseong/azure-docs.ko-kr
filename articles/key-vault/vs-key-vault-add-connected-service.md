---
title: Visual Studio를 사용하여 ASP.NET 프로젝트에 Key Vault 지원 추가 | Microsoft Docs
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
ms.openlocfilehash: 9f0cc6ee06042948442aace05d56fcffa3742a8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 연결된 서비스를 사용하여 웹 응용 프로그램에 Key Vault 추가

이 자습서에서는 ASP.NET Core를 사용하든 아니면 모든 종류의 ASP.NET 프로젝트를 사용하든 Visual Studio에서 웹 프로젝트에 대한 사용자의 비밀을 관리하기 위해 Azure Key Vault를 사용하여 시작해야 하는 모든 항목을 쉽게 추가하는 방법에 대해 알아봅니다. Visual Studio 2017에서 연결된 서비스 기능을 사용하면 Azure의 Key Vault에 연결해야 하는 모든 NuGet 패키지 및 구성 설정을 Visual Studio에서 자동으로 추가하도록 할 수 있습니다. 

Key Vault를 사용하도록 설정하기 위해 프로젝트에서 연결된 서비스에서 수행하는 변경 내용에 대한 자세한 내용은 [Key Vault 연결된 서비스 - 내 ASP.NET 4.7.1 프로젝트에서 변경된 내용](vs-key-vault-aspnet-what-happened.md) 또는 [Key Vault 연결된 서비스 - 내 ASP.NET Core 프로젝트에서 변경된 내용](vs-key-vault-aspnet-core-what-happened.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- 설치된 **Visual Studio 2017 버전 15.7**과 **웹 개발** 워크로드. [여기에서 다운로드하세요](https://aka.ms/vsdownload).
- ASP.NET(Core 아님)의 경우 기본적으로 설치되어 있지 않은 .NET Framework 4.7.1 개발 도구가 필요합니다. 설치하려면 Visual Studio 설치 관리자를 시작하고, **수정**을 선택한 다음, **개별 구성 요소**를 선택한 다음, 오른쪽에서 **ASP.NET 및 웹 개발**을 확장하고 **.NET Framework 4.7.1 개발 도구**를 선택합니다.
- ASP.NET 4.7.1 또는 ASP.NET Core 2.0 웹 프로젝트 열기.

## <a name="add-key-vault-support-to-your-project"></a>프로젝트에 Key Vault 지원 추가

1. **솔루션 탐색기**에서 **추가** > **연결된 서비스**를 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.
1. 사용 가능한 서비스 메뉴에서 **Azure Key Vault로 비밀 보호**를 선택합니다.

   ![“Azure Key Vault로 비밀 보호” 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Visual Studio에 로그인하고 사용자 계정과 연결된 Azure 구독이 있는 경우 구독이 포함된 드롭다운 목록이 페이지에 표시됩니다.
1. 사용하려는 구독을 선택한 다음, 새로운 Key Vault 또는 기존 Key Vault를 선택하거나 편집 링크를 선택하여 자동으로 생성된 이름을 수정합니다.

   ![구독 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Key Vault에 사용할 이름을 입력합니다.

   ![Key Vault의 이름 변경 및 리소스 그룹 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 기존 리소스 그룹을 선택하거나 자동으로 생성된 고유한 이름으로 새 레코드를 만듭니다.  다른 이름으로 새 그룹을 만들려는 경우 [Azure Portal](https://portal.azure.com)을 사용한 다음, 페이지를 닫고 리소스 그룹의 목록을 다시 로드할 수 있습니다.
1. Key Vault를 만들 지역을 선택합니다. 웹 응용 프로그램이 Azure에서 호스트되는 경우 최적의 성능을 위해 웹 응용 프로그램을 호스팅하는 지역을 선택합니다.
1. 가격 책정 모델을 선택합니다. 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요.
1. 확인을 선택하여 구성 선택을 수락합니다.
1. **추가**를 선택하여 Key Vault를 만듭니다. 이미 사용되는 이름을 선택하면 만들기 프로세스에 실패할 수 있습니다.  이렇게 되면 **편집** 링크를 사용하여 Key Vault 이름을 바꾸고 다시 시도합니다.

   ![프로젝트에 연결된 서비스 추가 중](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 이제 Azure의 Key Vault에 비밀을 추가합니다. 포털에서 올바른 위치로 이동하려면 이 Key Vault에 저장된 비밀 관리 링크를 클릭합니다. 페이지 또는 프로젝트를 닫은 후 **보안**에서 **모든 서비스**를 선택하여 [Azure Portal](https://portal.azure.com)에서 링크로 이동하고, **Key Vault**를 선택한 다음, 방금 만든 Key Vault를 선택할 수 있습니다.

   ![포털로 이동](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 만든 키 자격 증명 모음의 Key Vault 섹션에서 **비밀**을 선택한 다음, **생성/가져오기**를 선택합니다.

   ![비밀 생성/가져오기](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. “MySecret”과 같은 암호를 입력하고 테스트로 임의의 문자열 값을 입력한 다음, **만들기** 단추를 선택합니다.

   ![비밀 만들기](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (선택 사항) 다른 비밀을 입력합니다. 단, 이번에는 “비밀--MySecret”이라는 이름을 지정하여 범주에 포함시킵니다. 이 구문은 “MySecret”이란 비밀을 포함하는 “비밀” 범주를 지정합니다.
 
이제 코드에서 비밀에 액세스할 수 있습니다. 다음 단계는 ASP.NET 4.7.1 또는 ASP.NET Core를 사용하는지에 따라 달라집니다.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>코드에서 비밀에 액세스(ASP.NET Core 프로젝트)

1. Visual Studio의 ASP.NET Core 프로젝트에서 이제 코드에 다음 식을 사용하여 이러한 비밀을 참조할 수 있습니다.
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. .cshtml 페이지에서 About.cshtml을 입력하고, 파일의 상단에 @inject 지시문을 추가하여 Key Vault 구성에 액세스하는 데 사용할 수 있는 변수를 설정합니다.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. 테스트로 페이지 중 하나에 표시하여 비밀의 값을 사용할 수 있는지 확인할 수 있습니다. @config를 사용하여 구성 변수를 참조합니다.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. 웹 응용 프로그램을 빌드 및 실행하고, 정보 페이지로 이동한 다음, “비밀” 값을 확인합니다.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>코드에서 비밀에 액세스(ASP.NET 4.7.1 프로젝트)

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

축하합니다. 이제 웹앱에서 Key Vault를 사용하여 안전하게 저장된 비밀에 액세스할 수 있음을 확인했습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Key Vault와 관련된 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **TYPE THE RESOURCE GROUP NAME:** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Key Vault 개발자 가이드](key-vault-developers-guide.md)를 확인하여 Key Vault 개발에 대해 자세히 알아봅니다.