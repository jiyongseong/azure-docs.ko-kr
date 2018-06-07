---
title: '서비스 간 인증: Azure Active Directory를 사용하여 REST API로 Data Lake Store 인증 | Microsoft Docs'
description: Azure Active Directory를 사용하여 REST API로 Data Lake Store 서비스 간 인증을 수행하는 방법을 알아봅니다.
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
ms.openlocfilehash: 89a747fec08ad96c21429d2034eaf395e65566a4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>REST API를 사용하여 Data Lake Store로 서비스 간 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK 사용](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API 사용](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

이 문서에서는 REST API를 사용하여 Azure Data Lake Store로 서비스 간 인증을 수행하는 방법을 배웁니다. REST API를 사용한 Data Lake Store 최종 사용자 인증의 경우 [REST API를 사용하여 Data Lake Store로 최종 사용자 인증](data-lake-store-end-user-authenticate-rest-api.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "웹" 응용 프로그램 만들기**. [Azure Active Directory를 사용하여 Data Lake Store로 서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

## <a name="service-to-service-authentication"></a>서비스 간 인증
이 시나리오에서 응용 프로그램은 고유한 자격 증명을 제공하여 작업을 수행합니다. 이를 위해 다음 코드 조각과 같은 POST 요청을 실행해야 합니다. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

이 요청의 출력에는 이후에 REST API 호출을 사용하여 전달하는 권한 부여 토큰(아래 출력의 `access-token`에서 지정)이 포함됩니다. 인증 토큰을 텍스트 파일에 저장합니다. Data Lake Store에 대한 REST 호출을 만들 때 필요합니다.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

이 문서에서는 **비대화형** 접근 방식을 사용합니다. 비대화형(서비스 간 호출)에 대한 자세한 내용은 [자격 증명을 사용하여 서비스 간 호출](https://msdn.microsoft.com/library/azure/dn645543.aspx)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 서비스 간 인증을 사용하여 REST API로 Azure Data Lake Store를 인증하는 방법을 배웠습니다. 이제 다음 문서를 통해 REST API를 Azure Data Lake Store와 함께 사용하는 방법을 살펴볼 수 있습니다.

* [REST API를 사용한 Data Lake Store에서의 계정 관리 작업](data-lake-store-get-started-rest-api.md)
* [REST API를 사용한 Data Lake Store에서의 데이터 작업](data-lake-store-data-operations-rest-api.md)

