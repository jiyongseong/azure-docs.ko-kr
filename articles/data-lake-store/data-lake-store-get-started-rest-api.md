---
title: "REST API: Azure Data Lake Store에서의 계정 관리 작업 | Microsoft Docs"
description: "Azure Data Lake Store 및 WebHDFS REST API를 사용하여 Data Lake Store에서 계정 관리 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5fafde870a01a6ceb5e86f7b00b0ca11b748c68a
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>REST API를 사용한 Azure Data Lake Store의 계정 관리 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

이 문서에서는 REST API를 사용하여 Data Lake Store에서 계정 관리 작업을 수행하는 방법을 알아 봅니다. 계정 관리 작업에는 Data Lake Store 계정 만들기, Data Lake Store 계정 삭제 등이 포함됩니다. REST API를 사용하여 Data Lake Store에서 파일 시스템 작업을 수행하는 방법에 대한 지침은 [REST API를 사용한 Data Lake Store의 파일 시스템 작업](data-lake-store-data-operations-rest-api.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **[cURL](http://curl.haxx.se/)**. 이 문서에서는 cURL을 사용하여 Data Lake 저장소 계정에 대해 REST API 호출을 수행하는 방법을 설명합니다.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?
Azure Active Directory를 사용한 인증에는 두 가지 접근 방식이 있습니다.

* 응용 프로그램에 대한 최종 사용자 인증의 경우(대화형) [.NET SDK를 사용한 Data Lake Store의 최종 사용자 인증](data-lake-store-end-user-authenticate-rest-api.md)을 참조하세요.
* 응용 프로그램에 대한 서비스 간 인증의 경우(비대화형) [.NET SDK를 사용한 Data Lake Store의 서비스 간 인증](data-lake-store-service-to-service-authenticate-rest-api.md)을 참조하세요.


## <a name="create-a-data-lake-store-account"></a>Data Lake 저장소 계정 만들기
이 작업은 [여기](https://msdn.microsoft.com/library/mt694078.aspx)에 정의된 REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **\<yourstorename>**을 Data Lake Store 이름으로 바꿉니다.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

위 명령에서 \<`REDACTED`\>을 이전에 검색한 권한 부여 토큰으로 바꿉니다. 이 명령에 대한 요청 페이로드는 위의 `-d` 매개 변수에 대해 제공된 **input.json** 파일에 포함됩니다. input.json 파일의 내용은 다음 코드 조각과 유사합니다.

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Data Lake 저장소 계정 삭제
이 작업은 [여기](https://msdn.microsoft.com/library/mt694075.aspx)에 정의된 REST API 호출을 기반으로 합니다.

Data Lake 저장소 계정을 삭제하려면 다음 cURL 명령을 사용합니다. **\<yourstorename>**을 Data Lake Store 이름으로 바꿉니다.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

다음 코드 조각과 유사한 출력이 표시됩니다.

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>다음 단계
* [REST API를 사용한 Data Lake Store에서의 파일 시스템 작업](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>참고 항목
* [Azure Data Lake Store REST API 참조](https://docs.microsoft.com/rest/api/datalakestore/)
* [Azure Data Lake 저장소와 호환되는 오픈 소스 빅 데이터 응용 프로그램](data-lake-store-compatible-oss-other-applications.md)

