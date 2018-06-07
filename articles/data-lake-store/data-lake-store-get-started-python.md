---
title: 'Python: Azure Data Lake Store에서의 계정 관리 작업 | Microsoft Docs'
description: Python SDK를 사용하여 Data Lake Store 계정 관리 작업을 수행하는 방법을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 89d4110679d7a02d1468a9dab6816fb9e6b02181
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Python을 사용한 Azure Data Lake Store의 계정 관리 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Python SDK를 사용하여 Azure Data Lake Store에서 Data Lake Store 계정 만들기, Data Lake Store 계정 나열 등과 같은 기본 계정 관리 작업을 수행하는 방법을 알아봅니다. Python을 사용하여 Data Lake Store에서 파일 시스템 작업을 수행하는 방법에 대한 지침은 [Python을 사용한 Data Lake Store의 파일 시스템 작업](data-lake-store-data-operations-python.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Python**. Python을 [여기](https://www.python.org/downloads/)에서 다운로드할 수 있습니다. 이 문서에서는 Python 3.6.2를 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure 리소스 그룹** 지침에 대해서는 [Azure 리소스 그룹 만들기](../azure-resource-manager/resource-group-portal.md)를 참조하세요.

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
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py의 변경 내용을 저장합니다.

## <a name="authentication"></a>인증

이 섹션에서는 Azure AD로 인증하는 다양한 방법에 대해 설명합니다. 제공되는 옵션은 다음과 같습니다.

* 응용 프로그램에 대한 최종 사용자 인증의 경우 [Python을 사용한 Data Lake Store의 최종 사용자 인증](data-lake-store-end-user-authenticate-python.md)을 참조하세요.
* 응용 프로그램에 대한 서비스 간 인증의 경우 [Python을 사용한 Data Lake Store의 서비스 간 인증](data-lake-store-service-to-service-authenticate-python.md)을 참조하세요.

## <a name="create-client-and-data-lake-store-account"></a>클라이언트 및 Data Lake Store 계정 만들기

다음 코드 조각은 Data Lake Store 계정 클라이언트를 먼저 만듭니다. 클라이언트 개체를 사용하여 Data Lake Store 계정을 만들 수 있습니다. 마지막으로 코드 조각은 파일 시스템 클라이언트 개체를 만듭니다.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Data Lake Store 계정 나열

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Data Lake Store 계정 삭제

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>다음 단계
* [Python을 사용한 Data Lake Store의 파일 시스템 작업](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>참고 항목
* [Azure Data Lake Store Python(계정 관리) 참조](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Azure Data Lake Store Python(파일 시스템) 참조](http://azure-datalake-store.readthedocs.io/en/latest)
* [Azure Data Lake 저장소와 호환되는 오픈 소스 빅 데이터 응용 프로그램](data-lake-store-compatible-oss-other-applications.md)
