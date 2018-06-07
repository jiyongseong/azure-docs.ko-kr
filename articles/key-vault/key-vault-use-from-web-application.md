---
title: 웹 응용 프로그램에서 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 웹 응용 프로그램에서 Azure Key Vault를 사용하는 방법을 알아볼 수 있습니다.
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: adhurwit
ms.openlocfilehash: de8276b22377db37075c6793d0710bb18625446c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="use-azure-key-vault-from-a-web-application"></a>웹 응용 프로그램에서 Azure Key Vault 사용

## <a name="introduction"></a>소개

이 자습서에서는 Azure의 웹 응용 프로그램에서 Azure Key Vault를 사용하는 방법을 알아볼 수 있습니다. 웹 응용 프로그램에서 사용할 수 있도록 Azure Key Vault의 암호에 액세스하는 과정을 안내합니다.

**예상 완료 시간:** 15분

Azure Key Vault에 대한 개요는 [Azure Key Vault란?](key-vault-whatis.md)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* Azure Key Vault의 암호에 대한 URI
* 주요 자격 증명 모음에 액세스할 수 있는, Azure Active Directory에 등록된 웹 응용 프로그램의 클라이언트 ID 및 클라이언트 암호
* 웹 응용 프로그램. Azure에 웹앱으로 배포된 ASP.NET MVC 응용 프로그램에 대한 단계를 살펴보겠습니다.

>[!IMPORTANT]
>* 이 샘플은 수동으로 AAD ID를 프로비저닝하는 이전 방식을 사용합니다. 현재는 AAD ID를 자동으로 프로비저닝할 수 있는 [MSI(관리 서비스 ID)](https://docs.microsoft.com/azure/active-directory/msi-overview)라는 새로운 기능이 미리 보기에 포함되어 있습니다. 자세한 내용은 [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/)의 다음 샘플을 참조하세요.

> [!NOTE]
>* 이 자습서에 대해 [Azure Key Vault 시작](key-vault-get-started.md)에 나열된 단계를 완료하여 웹 응용 프로그램의 클라이언트 ID 및 클라이언트 암호에 대한 URI가 있어야 합니다.


Azure Active Directory에 등록되고 주요 자격 증명 모음에 대한 액세스 권한이 부여된 웹 응용 프로그램에서 주요 자격 증명 모음에 액세스합니다. 그렇지 않은 경우 시작 자습서의 응용 프로그램 등록으로 돌아가서 나열된 단계를 반복합니다.

이 자습서는 Azure에서 웹 응용 프로그램을 만들기 위한 기본 사항을 잘 알고 있는 웹 개발자를 대상으로 합니다. Azure Web Apps에 대한 자세한 내용은 [Web Apps 개요](../app-service/app-service-web-overview.md)를 참조하세요.

## <a id="packages"></a>NuGet 패키지 추가

웹 응용 프로그램을 위해 설치해야 하는 2개의 패키지가 있습니다.

* Active Directory 인증 라이브러리 - Azure Active Directory를 조작하고 사용자 ID를 관리하기 위한 메서드를 포함합니다.
* Azure Key Vault 라이브러리 - Azure Key Vault를 조작하기 위한 메서드를 포함합니다.

패키지 관리자 콘솔에서 Install-Package 명령을 사용하여 이러한 패키지를 모두 설치할 수 있습니다.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Web.Config 수정

다음과 같이 web.config 파일에 추가해야 하는 3개의 응용 프로그램 설정이 있습니다.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

응용 프로그램을 Azure 웹앱으로 호스트하지 않으려는 경우 web.config에 실제 ClientId, 클라이언트 암호 및 암호 URI 값을 추가해야 합니다. 그렇지 않으면 보안 강화를 위해 Azure Portal에서 실제 값을 추가할 것이므로 이러한 더미 값을 그대로 둡니다.

## <a id="gettoken"></a>액세스 토큰을 가져오는 메서드 추가

주요 자격 증명 모음 API를 사용하려면 액세스 토큰이 필요합니다. 주요 자격 증명 모음 클라이언트가 주요 자격 증명 모음 API 호출을 처리하지만 액세스 토큰을 가져오는 함수를 제공해야 합니다.  

다음은 Azure Active Directory에서 액세스 토큰을 가져오는 코드입니다. 이 코드는 응용 프로그램의 아무 곳에나 배치할 수 있습니다. 이 경우 Utils 또는 EncryptionHelper 클래스를 추가하겠습니다.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* 현재 새 기능 MSI(관리 서비스 ID)는 인증하는 가장 쉬운 방법입니다. 자세한 내용은 [.NET의 응용 프로그램에서 MSI로 Key Vault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/)를 사용하는 샘플에 대한 다음 링크 및 관련 [App Service 및 함수 자습서로 MSI](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)를 참조하세요. 
>* 클라이언트 ID 및 클라이언트 암호를 사용하는 것은 Azure AD 응용 프로그램을 인증하는 또 다른 방법입니다. 웹 응용 프로그램에서 클라이언트 ID 및 클라이언트 암호를 사용하여 의무를 분리하고 키 관리를 보다 세밀하게 제어할 수 있습니다. 하지만 이 방법은 클라이언트 암호를 구성 설정에 배치하는 것을 기반으로 하며 이것은 보호할 암호를 구성 설정에 배치하는 것 만큼이나 위험할 수 있습니다. 클라이언트 ID 및 클라이언트 암호 대신 클라이언트 ID 및 인증서를 사용하여 Azure AD 응용 프로그램을 인증하는 방법에 대한 설명은 다음을 참조하세요.

## <a id="appstart"></a>응용 프로그램 시작 시 암호 검색

이제 주요 자격 증명 모음 API를 호출하고 암호를 검색하는 코드가 필요합니다. 다음 코드는 사용하기 전에 호출되기만 하면 아무 곳에나 배치할 수 있습니다. 이 경우 시작 시 한 번 실행되어 응용 프로그램에서 암호를 사용할 수 있도록 Global.asax의 Application Start 이벤트에 이 코드를 배치했습니다.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Azure Portal에서 앱 설정 추가(선택 사항)

Azure 웹앱이 있는 경우 이제 Azure Portal에서 AppSettings의 실제 값을 추가할 수 있습니다. 이 작업을 수행하면 실제 값이 web.config에 포함되지 않고 별도 액세스 제어 기능이 있는 포털을 통해 보호됩니다. 이러한 값은 web.config에 입력된 값을 대체합니다. 이름이 같아야 합니다.

![Azure Portal에 표시되는 응용 프로그램 설정][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>클라이언트 암호 대신 인증서로 인증

클라이언트 ID 및 클라이언트 암호 대신 클라이언트 ID 및 인증서를 사용하여 Azure AD 응용 프로그램을 인증하는 다른 방법입니다. Azure 웹앱에서 인증서를 사용하는 단계는 다음과 같습니다.

1. 인증서 얻기 또는 만들기
2. 인증서를 Azure AD 응용 프로그램에 연결
3. 인증서를 사용하도록 웹앱에 코드 추가
4. 웹앱에 인증서 추가

### <a name="get-or-create-a-certificate"></a>인증서 얻기 또는 만들기

여기서는 테스트 인증서를 만듭니다. 다음은 개발자 명령 프롬프트에서 인증서를 만들기 위해 사용할 수 있는 몇 가지 명령입니다. 인증서 파일을 만들 디렉터리로 변경합니다.  또한 인증서의 시작 및 종료 날짜는 현재 날짜 더하기 1년을 사용합니다.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

.pfx에 대한 종료 날짜와 암호를 메모해 둡니다(이 예에서는 07/31/2018 및 test123). 아래에서 필요합니다.

테스트 인증서 만들기에 대한 자세한 내용은 [방법: 사용자 고유의 테스트 인증서 만들기](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>인증서를 Azure AD 응용 프로그램에 연결

인증서를 만들었으니 이제 Azure AD 응용 프로그램에 연결해야 합니다. 현재 Azure Portal에서는 이 워크플로를 지원하지 않으며, PowerShell을 통해 완료할 수 있습니다. 다음 명령을 실행하여 인증서를 Azure AD 응용 프로그램과 연결합니다.

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

이러한 명령을 실행하면 Azure AD에서 응용 프로그램을 볼 수 있습니다. 검색 시 검색 대화 상자에서 “회사에서 사용하는 응용 프로그램” 대신 “회사가 보유한 응용 프로그램”을 선택하세요.

Azure AD 응용 프로그램 개체 및 ServicePrincipal 개체에 대해 자세히 알아보려면 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)를 참조하세요.

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>인증서를 사용하도록 웹앱에 코드 추가

이제 웹앱에 코드를 추가하여 인증서에 액세스하고 인증에 사용합니다.

먼저 인증서에 액세스하기 위한 코드가 있습니다.

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

StoreLocation은 LocalMachine이 아닌, CurrentUser입니다. 테스트 인증서를 사용 중이므로 Find 메서드에 'false'를 입력합니다.

다음은 CertificateHelper를 사용하고 인증에 필요한 ClientAssertionCertificate를 만드는 코드입니다.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

다음은 액세스 토큰을 가져오는 새로운 코드입니다. 앞의 예제에서 GetToken 메서드를 대체합니다. 편의를 위해 다른 이름을 부여했습니다.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

사용 편의를 위해 이 모든 코드를 웹앱 프로젝트의 Utils 클래스에 넣어 두었습니다.

마지막 코드 변경은 Application_Start 메서드입니다. 먼저 GetCert() 메서드를 호출하여 ClientAssertionCertificate를 로드해야 합니다. 그런 다음 새 KeyVaultClient를 만들 때 제공한 콜백 메서드를 변경합니다. 이 코드는 앞의 예제에서 사용한 코드를 대체합니다.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Azure Portal을 통해 웹앱에 인증서 추가

웹앱에 인증서를 추가하는 것은 간단한 두 단계 프로세스입니다. 먼저 Azure Portal로 이동하여 웹앱으로 이동합니다. 웹앱에 대한 설정 블레이드에서 "사용자 지정 도메인 및 SSL" 항목을 클릭합니다. 열리는 블레이드에서 앞의 예제에서 만든 인증서인 KVWebApp.pfx를 업로드할 수 있습니다. pfx에 대한 암호는 기억하고 있어야 합니다.

![Azure Portal에서 웹앱에 인증서 추가][2]

마지막으로 수행해야 하는 일은 응용 프로그램 설정을 이름이 WEBSITE\_LOAD\_CERTIFICATES이고 값이 *인 웹앱에 추가하는 것입니다. 이렇게 하면 모든 인증서가 로드됩니다. 업로드한 인증서만 로드하려면 지문 복사의  쉼표로 구분된 목록을 입력하면 됩니다.

Web App에 인증서 추가에 대해 자세히 알아보려면 [Azure Websites 응용 프로그램에서 인증서 사용](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Key Vault에 인증서를 비밀로 추가

웹앱 서비스에 인증서를 직접 업로드하는 대신, 암호로 주요 자격 증명 모음에 저장하고 해당 위치에서 배포할 수 있습니다. 다음은 다음 블로그 게시물 [주요 자격 증명 모음을 통해 Azure 웹앱 인증서 배포](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>다음 단계

프로그래밍 참조는 [Azure Key Vault C# 클라이언트 API 참조](https://msdn.microsoft.com/library/azure/dn903628.aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
