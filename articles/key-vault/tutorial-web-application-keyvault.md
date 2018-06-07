---
title: Key Vault에서 비밀을 읽도록 Azure 웹 응용 프로그램 구성 자습서 | Microsoft Docs
description: Key Vault에서 비밀을 읽도록 ASP.Net Core 응용 프로그램 구성 자습서
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 146ea04081a4adebe4a6e9249bb1fe34ba76e3a4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305177"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>자습서: Key Vault에서 비밀을 읽도록 Azure 웹 응용 프로그램 구성

이 자습서에서는 관리되는 서비스 ID를 사용하여 Key Vault에서 정보를 읽는 Azure 웹 응용 프로그램을 만드는 단계를 살펴봅니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Key Vault를 만듭니다.
> * Key Vault에 비밀을 저장합니다.
> * Azure 웹 응용 프로그램을 만듭니다.
> * 관리되는 서비스 ID를 사용하도록 설정합니다.
> * 응용 프로그램이 Key Vault에서 데이터를 읽기 위해 필요한 권한을 부여합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

방금 만든 리소스 그룹은 이 자습서 전체에서 사용됩니다.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

다음으로 이전 단계에서 만든 리소스 그룹에 Key Vault를 만듭니다. 이 자습서 전체에서 Key Vault의 이름으로 "ContosoKeyVault"를 사용하되, 고유한 이름을 사용해야 합니다. 다음 정보를 지정합니다.

* 자격 증명 모음 이름은 **ContosoKeyVault**입니다.
* 리소스 그룹 이름은 **ContosoResourceGroup**입니다.
* 위치는 **미국 동부**입니다.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

이 명령의 출력에는 새로 만든 Key Vault의 속성이 표시됩니다. 아래에 나열된 두 개의 속성을 기록합니다.

* **자격 증명 모음 이름**: 예제에서는 **ContosoKeyVault**입니다. 모든 Key Vault 명령에 이 Key Vault 이름을 사용할 것입니다.
* **Vault URI**: 예제에서는 https://<YourKeyVaultName>.vault.azure.net/입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 응용 프로그램은 URI를 사용해야 합니다.

>[!IMPORTANT]
> 매개 변수 'vault_name'이 '^[a-zA-Z0-9-]{3,24}$' 패턴을 준수해야 한다는 오류가 발생하는 경우 -name 매개 변수 값이 고유하지 않거나 3-24자의 영숫자로 구성된 문자열에 부합하지 않은 것입니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 보관해야 하면서도 응용 프로그램에 제공해야 하는 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다. 이 자습서에서 암호는 **AppSecret**이고 그 안에 **MySecret** 값이 저장됩니다.

아래 명령을 입력하여 값 **MySecret**을 저장하는 **AppSecret**이라는 비밀을 Key Vault에 만듭니다.

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

이 명령은 URI를 포함한 비밀 정보를 표시합니다. 이러한 단계를 완료하면 Azure Key Vault의 비밀에 대한 URI이 생깁니다. 이 정보를 기록해 둡니다. 나중에 필요합니다.

## <a name="create-a-web-app"></a>웹앱 만들기

이 섹션에서는 ASP.NET MVC 응용 프로그램 만들고 Azure에 웹앱으로 배포합니다. Azure Web Apps에 대한 자세한 내용은 [Web Apps 개요](../app-service/app-service-web-overview.md)를 참조하세요.

1. Visual Studio에서 **파일 > 새로 만들기 > 프로젝트**를 선택하여 프로젝트를 만듭니다. 

2. **새 프로젝트** 대화 상자에서 **Visual C# > 웹 > ASP.NET Core 웹 응용 프로그램**을 선택합니다.

3. 응용 프로그램의 이름을 **WebKeyVault**로 지정하고 **확인**을 선택합니다.
   >[!IMPORTANT]
   > 복사하여 붙여넣는 코드가 네임스페이스와 일치하도록 앱 이름을 WebKeyVault로 지정해야 합니다. 사이트 이름을 다른 이름으로 지정한 경우 해당 사이트 이름과 일치하도록 코드를 수정해야 합니다.

    ![새 ASP.NET 프로젝트 대화 상자](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. 모든 종류의 ASP.NET Core 웹앱을 Azure에 배포할 수 있습니다. 이 자습서의 경우 **웹 응용 프로그램** 템플릿을 선택하고 인증이 **인증 없음**으로 설정되어 있는지 확인합니다.

    ![ASPNET 인증 없음 대화 상자](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. **확인**을 선택합니다.

6. ASP.NET Core 프로젝트가 만들어지면 시작하는 데 도움이 되는 리소스에 대한 다양한 링크를 제공하는 ASP.NET Core 시작 페이지가 표시됩니다.

7. 메뉴에서 **디버그 > 디버깅하지 않고 시작**을 선택하여 웹앱을 로컬로 실행합니다.

## <a name="modify-the-web-app"></a>웹앱 수정

웹 응용 프로그램을 위해 설치해야 하는 두 가지 NuGet 패키지가 있습니다. 설치하려면 아래 단계를 수행합니다.

1. 솔루션 탐색기에서 웹 사이트 이름을 마우스 오른쪽 단추로 클릭합니다.
2. **솔루션용 NuGet 패키지 관리...** 를 선택합니다.
3. 검색 상자 옆의 확인란을 선택합니다. **시험판 포함**
4. 아래에 나열된 두 NuGet 패키지를 검색하여 솔루션에 추가하는 것을 수락합니다.

    * [Microsoft.Azure.Services.AppAuthentication(미리 보기)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) - 서비스-Azure-서비스 인증 시나리오에 사용할 액세스 토큰을 간편하게 가져올 수 있습니다. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) - Key Vault와 상호 작용하기 위한 메서드를 포함하고 있습니다.

5. 솔루션 탐색기를 사용하여 `Program.cs`를 열고 Program.cs 파일의 내용을 다음 코드로 바꿉니다. ```<YourKeyVaultName>```을 키 자격 증명 모음의 이름으로 바꿉니다.

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
            public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. 솔루션 탐색기를 사용하여 **페이지** 섹션으로 이동하고 `About.cshtml`을 엽니다. **About.cshtml.cs** 파일의 내용을 아래 코드로 바꿉니다.

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. 주 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 선택합니다. 브라우저가 나타나면 **정보** 페이지로 이동합니다. AppSecret의 값이 표시됩니다.

>[!IMPORTANT]
> HTTP 오류 502.5 - 프로세스 오류 메시지가 표시되면 `Program.cs`에 지정된 Key Vault 이름 이름을 확인합니다.

## <a name="publish-the-web-application-to-azure"></a>Azure에 웹 응용 프로그램 게시

1. 편집기 위에서 **WebKeyVault**를 선택합니다.
2. **게시**, **시작**을 차례로 선택합니다.
3. 새 **App Service**를 만들고, **게시**를 선택합니다.
4. **만들기**를 선택합니다.

>[!IMPORTANT]
> 브라우저 창이 열리고 502.5 - 프로세스 오류 메시지가 표시됩니다. 예상된 동작입니다. Key Vault에서 비밀을 읽을 수 있는 응용 프로그램 ID 권한을 부여해야 합니다.

## <a name="enable-managed-service-identity"></a>관리되는 서비스 ID 사용

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

1. Azure CLI로 돌아갑니다.
2. 이 응용 프로그램에 대한 ID를 만들려면 assign-identity 명령을 실행합니다.

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>이 명령은 포털로 이동하여 웹 응용 프로그램 속성에서 **관리되는 서비스 ID**를 **켜기**로 전환하는 것과 동일합니다.

## <a name="grant-rights-to-the-application-identity"></a>응용 프로그램 ID에 권한 부여

Azure Portal을 사용하여 Key Vault의 액세스 정책으로 이동한 후 자신에게 Key Vault에 대한 비밀 관리 액세스를 부여합니다. 이렇게 하면 로컬 개발 컴퓨터에서 응용 프로그램을 실행할 수 있습니다.

1. Azure Portal의 **리소스 검색** 대화 상자에서 Key Vault를 검색합니다.
2. **액세스 정책**을 선택합니다.
3. **새로 추가**를 선택하고, **비밀 권한** 섹션에서 **가져오기**, **목록**을 차례로 선택합니다.
4. **보안 주체 선택**을 선택하고 응용 프로그램 ID를 추가합니다. 그러면 응용 프로그램과 동일한 이름을 갖게 됩니다.
5. **확인**을 선택합니다.

이제 Azure의 계정과 응용 프로그램 ID에는 Key Vault에서 정보를 읽을 수 있는 권한이 있습니다. 페이지를 새로 고치면 사이트의 방문 페이지가 보일 것입니다. **정보**를 선택하는 경우 Key Vault에 저장한 값이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹 및 모든 해당 리소스를 삭제하려면 **az group delete** 명령을 사용합니다.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md)
