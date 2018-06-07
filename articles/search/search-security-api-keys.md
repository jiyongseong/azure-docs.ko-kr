---
title: Azure Search에 대한 관리자 및 쿼리 api-key 만들기, 관리 및 보호 | Microsoft Docs
description: api-key는 서비스 엔드포인트에 대한 액세스를 제어합니다. 관리자 키는 쓰기 액세스를 부여합니다. 쿼리 키는 읽기 전용 액세스에 대해서만 만들 수 있습니다.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 4215795b7cd2a25427a3ce9b3cde16bfc69cb009
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Azure Search 서비스에 대한 api-key 만들기 및 관리

검색 서비스에 대한 모든 요청에는 해당 서비스용으로 특별히 생성된 api-key가 필요합니다. 이 api-key는 검색 서비스 끝점에 액세스하는 유일한 방법입니다. 

api-key는 임의로 생성된 숫자 및 문자로 구성된 문자열입니다. [역할 기반 권한](search-security-rbac.md)을 통해 키를 삭제하거나 읽을 수 있지만 사용자 정의 암호로 키를 대체하거나 검색 작업에 액세스하기 위한 기본 인증 방법으로 Active Directory를 사용할 수 없습니다. 

검색 서비스에 액세스하는 데 사용되는 두 가지 키 유형은 다음과 같습니다. 관리자(읽기-쓰기) 및 쿼리(읽기 전용)

|키|설명|제한|  
|---------|-----------------|------------|  
|관리자|서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다.<br /><br /> 포털에서 *기본* 및 *보조* 키라고 하는 두 개의 관리자 키는 서비스를 만들 때 생성되고 요청 시 개별적으로 다시 생성할 수 있습니다. 키가 두 개이면 서비스에 대해 액세스를 지속하는 데 하나의 키를 사용하는 동안 다른 키를 롤오버할 수 있습니다.<br /><br /> 관리자 키는 HTTP 요청 헤더에서만 지정됩니다. URL에 관리자 api-key를 배치할 수 없습니다.|서비스당 최대 2개|  
|쿼리|인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 수행하는 클라이언트 응용 프로그램에 배포됩니다.<br /><br /> 쿼리 키는 요청 시 생성됩니다. 포털에서 수동으로 만들거나 [관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 통해 프로그래밍 방식으로 만들 수 있습니다.<br /><br /> 검색, 제안 또는 조회 작업의 HTTP 요청 헤더에서 쿼리 키를 지정할 수 있습니다. 또는 쿼리 키를 URL에 매개 변수로 전달할 수 있습니다. 클라이언트 응용 프로그램이 요청을 생성하는 방법에 따라 키를 쿼리 매개 변수로 전달하는 것이 쉬울 수 있습니다.<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|서비스당 50개|  

 시각적으로는 관리자 키 및 쿼리 키 간의 구분이 없습니다. 두 키는 임의로 생성된 32개의 영숫자 문자로 구성된 문자열입니다. 응용 프로그램에서 지정된 키의 형식을 잃어버린 경우 [포털에서 키 값을 확인](https://portal.azure.com)하거나 [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 사용하여 값 및 키 형식을 반환할 수 있습니다.  

> [!NOTE]  
>  요청 URI에서 `api-key`와 같은 중요한 데이터를 전달하는 낮은 수준의 보안 사례로 간주됩니다. 따라서 Azure Search에서는 쿼리 키만을 쿼리 문자열의 `api-key`로 허용하며, 인덱스 콘텐츠가 공개적으로 제공되어야 하는 경우가 아니면 중요한 데이터를 요청 URI에서 전달해서는 안 됩니다. 일반적으로 `api-key`를 요청 헤더로 전달하는 것이 좋습니다.  

## <a name="find-api-keys-for-your-service"></a>서비스에 대한 api-key 찾기

포털에서 또는 [관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 통해 액세스 키를 가져올 수 있습니다. 자세한 내용은 [관리자 및 쿼리 api-key 관리](search-security-api-keys.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 구독에 대한 [검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 나열합니다.
3. 서비스를 선택하고 서비스 페이지에서 **설정** >**키**를 찾아 관리자 및 쿼리 키를 봅니다.

![포털 페이지, 설정, 키 섹션](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

두 개의 관리자 키는 계속되는 액세스에 대한 보조 키를 사용하여 기본 키를 롤오버할 수 있도록 각 서비스에 대해 생성됩니다.

기본 및 보조 키를 동시에 다시 생성하는 경우 서비스 작업에 액세스하기 위해 두 키 중 하나를 사용하는 모든 응용 프로그램은 더 이상 서비스에 대한 액세스를 갖지 않습니다.

1. **설정** >**키** 페이지에서 보조 키를 복사합니다.
2. 모든 응용 프로그램의 경우 보조 키를 사용하도록 api-key 설정을 업데이트합니다.
3. 기본 키를 다시 생성합니다.
4. 새 기본 키를 사용하도록 모든 응용 프로그램을 업데이트합니다.

## <a name="secure-api-keys"></a>api-key 보안
키 보안은 포털 또는 Resource Manager 인터페이스를 통해 액세스를 제한하여 보장됩니다(PowerShell 또는 명령줄 인터페이스). 설명한 것처럼 구독 관리자는 모든 api-key를 보고 다시 생성할 수 있습니다. 예방 조치로 역할 할당을 검토하여 관리 키에 대한 액세스 권한이 있는 사용자를 파악할 수 있습니다.

+ 서비스 대시보드에서 **액세스 제어(IAM)** 를 클릭하여 서비스에 대한 역할 할당을 봅니다.

다음 역할의 멤버는 키를 보고 다시 생성할 수 있습니다. 소유자, 참가자 [Search Service 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> ID를 기반으로 검색 결과에 액세스하기 위해 보안 필터를 만들어서 ID로 결과를 잘라 요청자에게 액세스 권한이 없어야 하는 문서를 제거할 수 있습니다. 자세한 내용은 [보안 필터](search-security-trimming-for-azure-search.md) 및 [Active Directory로 보안](search-security-trimming-for-azure-search-with-aad.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

+ [Azure Search의 역할 기반 액세스 제어](search-security-rbac.md)
+ [Powershell을 사용하여 관리](search-manage-powershell.md) 
+ [성능 및 최적화 문서](search-performance-optimization.md)