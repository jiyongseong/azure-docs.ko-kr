---
title: SQL에 ActiveDirectoryInteractive 연결 | Microsoft Docs
description: SqlAuthenticationMethod.ActiveDirectoryInteractive 모드를 사용하여 Azure SQL Database에 연결하는 C# 코드와 설명입니다.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 6489fb5630e1990c942b461859650e2e469cda73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365018"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>ActiveDirectoryInteractive 모드를 사용하여 Azure SQL Database에 연결

이 문서에서는 Microsoft Azure SQL Database에 연결하는 실행 가능한 C# 코드 예제를 제공합니다. C# 프로그램은 Azure AD MFA(Multi-Factor Authentication)를 지원하는 대화형 인증 모드를 사용합니다. 예를 들어 연결 시도에 사용자의 휴대폰으로 전송될 확인 코드를 포함할 수 있습니다.

SQL 도구의 MFA 지원에 대한 자세한 내용은 [SSDT(SQL Server Data Tools)의 Azure Active Directory 지원](https://docs.microsoft.com/sql/ssdt/azure-active-directory)을 참조하세요.




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod .ActiveDirectoryInteractive 열거형 값

.NET Framework 버전 4.7.2부터 열거형 [**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod)에 새로운 값 **.ActiveDirectoryInteractive**가 생겼습니다. 이 열거형 값은 클라이언트 C# 프로그램에서 사용될 경우 MFA를 지원하는 Azure AD 대화형 모드를 사용하여 Azure SQL Database를 인증하라고 시스템에 지시합니다. 그러면 프로그램을 실행하는 사용자에게 다음 대화 상자가 표시됩니다.

1. Azure AD 사용자 이름을 표시하고 Azure AD 사용자의 암호를 요청하는 대화 상자.
    - 암호가 필요 없으면 이 대화 상자가 표시되지 않습니다. 사용자의 도메인이 Azure AD와 페더레이션된 경우 암호가 필요 없습니다.

    Azure AD에서 설정된 정책에 따라 MFA가 사용자에 적용되는 경우 다음 대화 상자가 그 다음으로 표시됩니다.

2. 사용자가 MFA 시나리오를 처음 경험할 때만 시스템에서 추가 대화 상자를 표시합니다. 이 대화 상자는 텍스트 메시지를 전송할 휴대폰 번호를 요청합니다. 각 메시지는 사용자가 그 다음 대화 상자에서 입력해야 하는 *확인 코드*를 제공합니다.

3. MFA 확인 코드를 요청하는 또 다른 대화 상자. 이 대화 상자는 시스템에서 휴대폰으로 전송합니다.

MFA를 사용하도록 Azure AD를 구성하는 방법은 [클라우드에서 Azure Multi-Factor Authentication 시작](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)을 참조하세요.

이러한 대화 상자의 스크린샷은 [SQL Server Management Studio 및 Azure AD에 대한 Multi-Factor Authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.

> [!TIP]
> 모든 종류의 .NET Framework API에 대한 일반 검색 페이지는 **.NET API 브라우저** 도구에 대한 다음 링크에 제공됩니다.
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> 선택적 추가 **?term=** 매개 변수에 형식 이름을 추가하면 검색 페이지에 결과를 준비해 놓을 수 있습니다.
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Azure Portal을 사용하여 C# 준비

이미 [Azure SQL Database 서버를 만들었고](sql-database-get-started-portal.md) 사용할 수 있다고 가정합니다.


### <a name="a-create-an-app-registration"></a>a. 앱 등록 만들기

Azure AD 인증을 사용하려면 프로그램에서 연결을 시도할 때 C# 클라이언트 프로그램이 *clientId*로 GUID를 제공해야 합니다. 앱 등록을 완료하면 GUID가 생성되고 **응용 프로그램 ID**라는 레이블이 붙어서 Azure Portal에 표시됩니다. 탐색 단계는 다음과 같습니다.

1. Azure Portal &gt; **Azure Active Directory** &gt; **앱 등록**

    ![앱 등록](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. **응용 프로그램 ID** 값이 생성 및 표시됩니다.

    ![표시된 앱 ID](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **등록된 앱** &gt; **설정** &gt; **필요한 권한** &gt; **앱**

    ![등록된 앱의 권한 설정](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **필요한 권한** &gt; **API 액세스 추가** &gt; **API 선택** &gt; **Azure SQL Database**

    ![Azure SQL Database에 대한 API 액세스 추가](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **API 액세스** &gt; **권한 선택** &gt; **위임된 권한**

    ![Azure SQL Database에 대한 API에 권한 위임](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. SQL 데이터베이스 서버에서 Azure AD 관리자 설정

각 Azure SQL Database 서버에는 Azure AD의 자체 SQL 논리 서버가 있습니다. 우리가 보고 있는 C# 시나리오에서는 Azure SQL 서버에 대한 Azure AD 관리자를 설정해야 합니다.

1. **SQL Server** &gt; **Active Directory 관리자** &gt; **관리자 설정**

    - Azure AD 관리자 및 Azure SQL Database의 사용자에 대한 자세한 내용은 [SQL Database를 사용하여 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)의 스크린샷과 그 안에 있는 **Azure SQL Database 서버에 대한 Azure Active Directory 관리자 프로비전** 섹션을 참조하세요.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. 특정 데이터베이스에 연결하도록 Azure AD 사용자 준비

Azure SQL Database 서버로 한정된 Azure AD에서는 특정 데이터베이스에 액세스할 수 있는 사용자를 추가할 수 있습니다.

자세한 내용은 [SQL Database, 관리되는 인스턴스 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)을 참조하세요.


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. 관리자가 아닌 사용자를 Azure AD에 추가

SQL Database 서버의 Azure AD 관리자를 사용하여 SQL Database 서버에 연결할 수 있습니다. 그러나 관리자가 아닌 사용자를 Azure AD에 추가하는 것이 보다 일반적입니다. 관리자가 아닌 사용자를 연결에 사용하면 Azure AD에서 이 사용자에 MFA를 적용할 때 MFA 시퀀스가 호출됩니다.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure ADAL(Active Directory Authentication Library)

C# 프로그램은 **Microsoft.IdentityModel.Clients.ActiveDirectory** 네임스페이스를 사용합니다. 이 네임스페이스에 대한 클래스는 같은 이름으로 어셈블리에 있습니다.

- NuGet을 사용하여 ADAL 어셈블리를 다운로드하고 설치합니다.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- C# 프로그램 컴파일을 지원하려면 어셈블리에 대한 참조를 추가합니다.




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod 열거형

C# 예제에서 사용하는 네임스페이스 중 하나는 **System.Data.SqlClient**입니다. 특히 흥미로운 것은 열거형 **SqlAuthenticationMethod**입니다. 이 열거형의 값은 다음과 같습니다.

- **SqlAuthenticationMethod.ActiveDirectory*Interactive***:&nbsp; MFA(Multi-Factor Authentication)를 구현하려면 Azure AD 사용자 이름과 함께 이 값을 사용합니다.
    - 이 문서에서는 이 값을 집중적으로 다룹니다. 이 값은 사용자 암호를 요청하는 대화 상자를 표시하여 대화형 경험을 생성한 다음, 이 사용자에게 MFA가 적용되면 MFA 유효성 검사를 위한 대화 상자를 표시합니다.
    - 이 값은 .NET framework 버전 4.7.2부터 사용할 수 있습니다.

- **SqlAuthenticationMethod.ActiveDirectory*Integrated***:&nbsp; 이 값은 *페더레이션된* 계정에 사용합니다. 페더레이션된 계정에서 사용자 이름은 Windows 도메인에 알려져 있습니다. 이 방법은 MFA를 지원하지 않습니다.

- **SqlAuthenticationMethod.ActiveDirectory*Password***:&nbsp; Azure AD 사용자 및 사용자 암호가 필요한 인증에는 이 값을 사용합니다. Azure SQL Database가 인증을 수행합니다. 이 방법은 MFA를 지원하지 않습니다.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Azure Portal에서 C# 매개 변수 값 준비

C# 프로그램을 성공적으로 실행하려면 다음 정적 필드에 적절한 값을 할당해야 합니다. 이러한 정적 필드는 프로그램의 매개 변수처럼 작동합니다. 이 필드는 가장 값과 함께 여기에 표시됩니다. 또한 적절한 값을 얻을 수 있는 위치가 Azure Portal에 표시됩니다.


| 정적 필드 이름 | 가장 값 | Azure Portal 내 위치 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-favorite-sqldb-svr.database.windows.net" | **SQL 서버** &gt; **이름으로 필터링** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **사용자** &gt; **새 게스트 사용자** |
| Initial_DatabaseName | "master" | **SQL 서버** &gt; **SQL 데이터베이스** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **앱 등록**<br /> &nbsp; &nbsp; &gt; **이름으로 검색** &gt; **응용 프로그램 ID** |
| RedirectUri | 새 Uri( "https://bing.com/") | **Azure Active Directory** &gt; **앱 등록**<br /> &nbsp; &nbsp; &gt; **이름으로 검색** &gt; *[Your-App-regis]* &gt;<br /> &nbsp; &nbsp; **설정** &gt; **RedirectURIs**<br /><br />이 문서에서는 어떤 값이라도 유효하기만 하다면 RedirectUri에 사용할 수 있습니다. 이 값은 실제로 사용되지 않습니다. |
| &nbsp; | &nbsp; | &nbsp; |


시나리오에 따라 앞에서 나온 표의 일부 매개 변수에는 값이 필요하지 않을 수도 있습니다.




## <a name="run-ssms-to-verify"></a>SSMS를 실행하여 확인

C# 프로그램을 실행하기 전에 SSMS(SQL Server Management Studio)를 실행하는 것이 좋습니다. SSMS를 실행하여 다양한 구성이 올바른지 확인할 수 있습니다. 그러면 C# 프로그램의 오류 범위를 소스 코드로 좁힐 수 있습니다.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>SQL Database 방화벽 IP 주소 확인

나중에 C# 프로그램을 실행할 같은 컴퓨터, 같은 건물에서 SSMS를 실행합니다. 본인이 가장 쉽다고 느끼는 **인증** 모드를 사용하면 됩니다. 데이터베이스 서버 방화벽에서 IP 주소를 허용하지 않는 경우 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙](sql-database-firewall-configure.md)의 설명에 따라 해결할 수 있습니다.


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Azure AD에 대한 MFA(Multi-Factor Authentication) 확인

이번에는 **인증**을 **Active Directory - MFA 지원을 통한 유니버설 인증**으로 설정하고 SSMS를 다시 실행합니다. 이 옵션을 사용하려면 SSMS 버전 17.5 이상이 있어야 합니다.

자세한 내용은 [SSMS 및 Azure AD에 대한 Multi-Factor Authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.




## <a name="c-code-example"></a>C# 코드 예제

이 C# 예제를 컴파일하려면 **Microsoft.IdentityModel.Clients.ActiveDirectory**라는 DLL 어셈블리에 대한 참조를 추가해야 합니다.


#### <a name="reference-documentation"></a>참조 설명서

- **System.Data.SqlClient** 네임스페이스:
    - 검색:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - 직접:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** 네임스페이스:
    - 검색:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - 직접:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>두 부분으로 된 C# 소스 코드

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>C# 프로그램의 두 번째 절반

보기 편하도록 C# 프로그램은 두 개의 코드 블록으로 분할됩니다. 프로그램을 실행하려면 두 코드 블록을 함께 붙여넣어야 합니다.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>C#의 실제 테스트 출력

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>다음 단계

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

