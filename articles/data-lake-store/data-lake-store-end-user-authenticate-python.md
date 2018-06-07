---
title: '최종 사용자 인증: Azure Active Directory를 사용하여 Python으로 Data Lake Store 인증 | Microsoft Docs'
description: Azure Active Directory와 Python을 사용하여 Data Lake Store로 최종 사용자 인증을 수행하는 방법을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: a93881c7a504279a1c48ba30d8d07e253f4b865b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Python을 사용하여 Data Lake Store로 최종 사용자 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK 사용](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-end-user-authenticate-python.md)
> * [REST API 사용](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

이 문서에서는 Python SDK를 사용하여 Azure Data Lake Store로 최종 사용자 인증을 수행하는 방법을 배웁니다. 최종 사용자 인증을 두 가지 범주로 세분화할 수 있습니다.

* 다단계 인증을 사용하지 않는 최종 사용자 인증
* 다단계 인증을 사용하는 최종 사용자 인증

이 문서에서는 두 옵션을 모두 설명합니다. Python을 사용하는 Data Lake Store 서비스 간 인증의 경우 [Python을 사용한 Data Lake Store의 서비스 간 인증](data-lake-store-service-to-service-authenticate-python.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Python**. Python을 [여기](https://www.python.org/downloads/)에서 다운로드할 수 있습니다. 이 문서에서는 Python 3.6.2를 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "네이티브" 응용 프로그램을 만듭니다**. [Azure Active Directory를 사용하여 Data Lake Store로 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

## <a name="install-the-modules"></a>모듈 설치

Python을 사용한 Data Lake Store 작업을 위해서는 3가지 모듈을 설치해야 합니다.

* Active Directory 등 Azure 모듈을 포함하는 `azure-mgmt-resource` 모듈.
* Azure Data Lake Store 계정 관리 작업을 포함하는 `azure-mgmt-datalake-store` 모듈. 이 모듈에 대한 자세한 내용은 [Azure Data Lake Store 관리 모듈 참조](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)를 참조하세요.
* Azure Data Lake Store 파일 시스템 작업을 포함하는 `azure-datalake-store` 모듈. 이 모듈에 대한 자세한 내용은 [Azure Data Lake Store 파일 시스템 모듈 참조](http://azure-datalake-store.readthedocs.io/en/latest/)를 참조하세요.

다음 명령을 사용하여 모듈을 설치합니다.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>새 Python 응용 프로그램 만들기

1. 원하는 IDE에서 **mysample.py**와 같이 새 Python 응용 프로그램을 만듭니다.

2. 다음 코드 조각을 추가하여 필요한 모듈 가져오기

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. mysample.py의 변경 내용을 저장합니다.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>다단계 인증을 사용하는 최종 사용자 인증

### <a name="for-account-management"></a>계정 관리의 경우

Data Lake Store 계정에 대한 계정 관리 작업을 수행할 수 있도록 다음 코드 조각을 사용하여 Microsoft Azure Active Directory에 인증합니다. 다음 코드 조각은 Multi-Factor Authentication을 사용하여 응용 프로그램을 인증하는 데 사용할 수 있습니다. 기존 Microsoft Azure Active Directory **네이티브** 응용 프로그램에 아래 값을 제공합니다.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>파일 시스템 작업의 경우

Data Lake Store 계정에 대한 파일 시스템 작업을 수행할 수 있도록 다음 코드 조각을 사용하여 Microsoft Azure Active Directory에 인증합니다. 다음 코드 조각은 Multi-Factor Authentication을 사용하여 응용 프로그램을 인증하는 데 사용할 수 있습니다. 기존 Microsoft Azure Active Directory **네이티브** 응용 프로그램에 아래 값을 제공합니다.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>다단계 인증을 사용하지 않는 최종 사용자 인증

이 방법은 더 이상 사용되지 않습니다. 자세한 내용은 [Python SDK를 사용한 Azure 인증](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token)을 참조하세요.
   
## <a name="next-steps"></a>다음 단계
이 문서에서는 최종 사용자 인증을 사용하여 Python SDK으로 Azure Data Lake Store를 인증하는 방법을 배웠습니다. 이제 다음 문서를 통해 Python을 Azure Data Lake Store와 함께 사용하는 방법을 살펴볼 수 있습니다.

* [Python을 사용한 Data Lake Store에서의 계정 관리 작업](data-lake-store-get-started-python.md)
* [Python을 사용한 Data Lake Store의 데이터 작업](data-lake-store-data-operations-python.md)

