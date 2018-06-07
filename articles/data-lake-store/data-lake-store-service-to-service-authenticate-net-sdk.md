---
title: '서비스 간 인증: Azure Active Directory를 사용하여 .NET SDK로 Data Lake Store 인증 | Microsoft Docs'
description: Azure Active Directory를 사용하여 .NET SDK로 Data Lake Store 서비스 간 인증을 수행하는 방법을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 794e46bd4bdbb29158a7d7111712fbf62621c79b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>.NET SDK를 사용하여 Data Lake Store로 서비스 간 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK 사용](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API 사용](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

이 문서에서는 .NET SDK를 사용하여 Azure Data Lake Store로 서비스 간 인증을 수행하는 방법을 배웁니다. .NET SDK를 사용한 Data Lake Store 최종 사용자 인증의 경우 [.NET SDK를 사용하여 Data Lake Store로 최종 사용자 인증](data-lake-store-end-user-authenticate-net-sdk.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건
* **Visual Studio 2013, 2015 또는 2017**. 아래 지침에서는 Visual Studio 2017을 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "웹" 응용 프로그램 만들기**. [Azure Active Directory를 사용하여 Data Lake Store로 서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

## <a name="create-a-net-application"></a>.NET 응용 프로그램 만들기
1. Visual Studio를 열고 콘솔 응용 프로그램을 만듭니다.
2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

   | 자산 | 값 |
   | --- | --- |
   | Category |Templates/Visual C#/Windows |
   | Template |콘솔 응용 프로그램 |
   | Name |CreateADLApplication |
4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. NuGet 패키지를 프로젝트에 추가합니다.

   1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   2. **NuGet 패키지 관리자** 탭에서 **패키지 원본**이 **nuget.org**로 설정되어 있고 **시험판 포함** 확인란이 선택되어 있는지 확인합니다.
   3. 다음 NuGet 패키지를 검색하고 설치합니다.

      * `Microsoft.Azure.Management.DataLake.Store` - 이 자습서에서는 v2.1.3-미리 보기를 사용합니다.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 이 자습서는 v2.2.12를 사용합니다.

        ![NuGet 원본 추가](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "새 Azure Data Lake 계정 만들기")
   4. **NuGet 패키지 관리자**를 닫습니다.

6. **Program.cs**를 열고 기존 코드를 삭제한 후 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

## <a name="service-to-service-authentication-with-client-secret"></a>클라이언트 암호를 사용하여 서비스 간 인증
.NET 클라이언트 응용 프로그램에서 이 코드 조각을 추가합니다. 자리 표시자 값을 Microsoft Azure Active Directory 웹 응용 프로그램에서 검색된 값(필수 구성 요소로 나열된)으로 바꿉니다.  이 코드 조각을 사용하면 Azure Data Lake Store 웹 응용 프로그램의 클라이언트 암호/키를 사용하여 응용 프로그램을 Data Lake Store에 **비 대화형**으로 인증할 수 있습니다. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

앞의 코드 조각은 도우미 함수 `GetCreds_SPI_SecretKey`를 사용합니다. 이 도우미 함수에 대한 코드는 [여기 Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey)에서 사용할 수 있습니다.

## <a name="service-to-service-authentication-with-certificate"></a>인증서를 사용하여 서비스 간 인증

.NET 클라이언트 응용 프로그램에서 이 코드 조각을 추가합니다. 자리 표시자 값을 Microsoft Azure Active Directory 웹 응용 프로그램에서 검색된 값(필수 구성 요소로 나열된)으로 바꿉니다. 이 코드 조각을 사용하면 Microsoft Azure Active Directory 웹 응용 프로그램의 인증서를 사용하여 응용 프로그램을 Data Lake Store에 **비 대화형**으로 인증할 수 있습니다. Microsoft Azure Active Directory 응용 프로그램을 만드는 방법에 대한 지침은 [인증서로 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate)를 참조하세요.

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
        var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
        var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
    }

앞의 코드 조각은 도우미 함수 `GetCreds_SPI_Cert`를 사용합니다. 이 도우미 함수에 대한 코드는 [여기 Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 서비스 간 인증을 사용하여 .NET SDK로 Azure Data Lake Store를 인증하는 방법을 배웠습니다. 이제 다음 문서를 통해 .NET SDK를 Azure Data Lake Store와 함께 사용하는 방법을 살펴볼 수 있습니다.

* [.NET SDK를 사용한 Data Lake Store에서의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)
* [.NET SDK를 사용한 Data Lake Store의 데이터 작업](data-lake-store-data-operations-net-sdk.md)


