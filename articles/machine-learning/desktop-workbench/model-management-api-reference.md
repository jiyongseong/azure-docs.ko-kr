---
title: Azure Machine Learning에서 모델 관리에 대한 Docker 이미지 만들기 | Microsoft Docs
description: 이 문서에서는 웹 서비스에 대한 Docker 이미지를 만들기 위한 단계를 설명합니다.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 3efc351861a7d5b3c02c08b96494e83a3ccb99f6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362144"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Azure Machine Learning 모델 관리 계정 API 참조

배포 환경 설정에 대한 정보는 [모델 관리 계정 설정](deployment-setup-configuration.md)을 참조하세요.

Azure Machine Learning 모델 관리 계정 API는 다음과 같은 작업을 구현합니다.

- 모델 등록
- 매니페스트 만들기
- Docker 이미지 만들기
- 웹 서비스 만들기

이 이미지를 사용하여 로컬로 또는 원격 Azure Container Service 클러스터나 선택한 다른 Docker 지원 환경에서 웹 서비스를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
[설치 및 생성 빠른 시작](../service/quickstart-installation.md) 문서의 설치 단계를 완료했는지 확인합니다.

계속하기 전에 다음이 필요합니다.
1. 모델 관리 계정 프로비저닝
2. 모델 배포 및 관리를 위한 환경 만들기
3. Machine Learning 모델

### <a name="azure-ad-token"></a>Azure AD 토큰
Azure CLI를 사용하는 경우 `az login`을 사용하여 로그인합니다. CLI는 .azure 파일에서 Azure AD(Azure Active Directory) 토큰을 사용합니다. API를 사용하려는 경우 다음 옵션을 사용할 수 있습니다.

##### <a name="acquire-the-azure-ad-token-manually"></a>수동으로 Azure AD 토큰 획득
`az login`을 사용하고 홈 디렉터리에 있는 .azure 파일에서 최신 토큰을 가져올 수 있습니다.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>프로그래밍 방식으로 Azure AD 토큰 획득
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
서비스 주체를 만든 후 출력을 저장합니다. 이제 Azure AD에서 토큰을 가져오는 데 사용할 수 있습니다.

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
토큰은 API 호출에 대한 승인 헤더에 추가됩니다.


## <a name="register-a-model"></a>모델 등록

모델 등록 단계에서는 만든 Azure 모델 관리 계정에 Machine Learning 모델을 등록합니다. 이 등록을 수행하면 등록 시에 할당된 모델 및 해당 버전을 추적할 수 있습니다. 사용자는 모델의 이름을 제공합니다. 이후에 같은 이름으로 모델을 등록하면 새 버전 및 ID가 생성됩니다.

### <a name="request"></a>요청

| 방법 | 요청 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>설명
모델을 등록합니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| model | 본문 | 모델을 등록하는 데 사용되는 페이로드입니다. | 예 | [Model](#model) |


### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | OK. 모델 등록이 성공했습니다. | [Model](#model) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>계정의 모델 목록 쿼리
### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>설명
계정의 모델 목록을 쿼리합니다. 태그와 이름을 기준으로 결과 목록을 필터링할 수 있습니다. 필터를 제공하지 않으면 쿼리는 계정의 모든 모델을 표시합니다. 반환된 목록의 페이지가 매겨지고 각 페이지의 항목 수가 선택적 매개 변수로 사용됩니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| 이름 | 쿼리 | 개체 이름입니다. | 아니오 | string |
| tag | 쿼리 | 모델 태그입니다. | 아니오 | string |
| count | 쿼리 | 한 페이지에서 검색할 항목의 수입니다. | 아니오 | string |
| $skipToken | 쿼리 | 다음 페이지를 검색하기 위한 연속 토큰입니다. | 아니오 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [PaginatedModelList](#paginatedmodellist) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>모델 세부 정보 가져오기
### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>설명
ID별로 모델을 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 개체 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [Model](#model) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>등록된 모델 및 모든 종속성을 포함하는 매니페스트 등록

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>설명
등록된 모델 및 모든 종속성을 포함하는 매니페스트를 등록합니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| manifestRequest | 본문 | 매니페스트를 등록하는 데 사용되는 페이로드입니다. | 예 | [Manifest](#manifest) |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 매니페스트 등록이 성공했습니다. | [Manifest](#manifest) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>계정의 매니페스트 목록 쿼리

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>설명
계정의 매니페스트 목록을 쿼리합니다. 모델 ID와 매니페스트 이름을 사용하여 결과 목록을 필터링할 수 있습니다. 필터를 제공하지 않으면 쿼리는 계정의 모든 매니페스트를 표시합니다. 반환된 목록의 페이지가 매겨지고 각 페이지의 항목 수가 선택적 매개 변수로 사용됩니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| modelId | 쿼리 | 모델 ID입니다. | 아니오 | string |
| manifestName | 쿼리 | 매니페스트 이름입니다. | 아니오 | string |
| count | 쿼리 | 한 페이지에서 검색할 항목의 수입니다. | 아니오 | string |
| $skipToken | 쿼리 | 다음 페이지를 검색하기 위한 연속 토큰입니다. | 아니오 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [PaginatedManifestList](#paginatedmanifestlist) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>매니페스트 세부 정보 가져오기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>설명
ID별로 매니페스트를 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 개체 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [Manifest](#manifest) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>이미지 만들기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>설명
Azure Container Registry에 Docker 이미지로 이미지를 만듭니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| imageRequest | 본문 | 이미지를 만드는 데 사용되는 페이로드입니다. | 예 | [ImageRequest](#imagerequest) |

### <a name="responses"></a>응답
| 코드 | 설명 | 헤더 | 스키마 |
|--------------------|--------------------|--------------------|--------------------|
| 202 | 비동기 작업 위치 URL입니다. GET 호출은 이미지 만들기 작업의 상태를 표시합니다. | Operation-Location |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>계정의 이미지 목록 쿼리

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>설명
계정의 이미지 목록을 쿼리합니다. 매니페스트 ID와 이름을 사용하여 결과 목록을 필터링할 수 있습니다. 필터를 제공하지 않으면 쿼리는 계정의 모든 이미지를 표시합니다. 반환된 목록의 페이지가 매겨지고 각 페이지의 항목 수가 선택적 매개 변수로 사용됩니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| manifestId | 쿼리 | 매니페스트 ID입니다. | 아니오 | string |
| manifestName | 쿼리 | 매니페스트 이름입니다. | 아니오 | string |
| count | 쿼리 | 한 페이지에서 검색할 항목의 수입니다. | 아니오 | string |
| $skipToken | 쿼리 | 다음 페이지를 검색하기 위한 연속 토큰입니다. | 아니오 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [PaginatedImageList](#paginatedimagelist) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>이미지 세부 정보 가져오기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>설명
ID별로 이미지를 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 이미지 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [이미지](#image) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>서비스 만들기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>설명
이미지에서 서비스를 만듭니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| serviceRequest | 본문 | 서비스를 만드는 데 사용되는 페이로드입니다. | 예 | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>응답
| 코드 | 설명 | 헤더 | 스키마 |
|--------------------|--------------------|--------------------|--------------------|
| 202 | 비동기 작업 위치 URL입니다. GET 호출은 서비스 만들기 작업의 상태를 표시합니다. | Operation-Location |
| 409 | 지정된 이름의 서비스가 이미 있습니다. |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>계정의 서비스 목록 쿼리

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>설명
계정의 서비스 목록을 쿼리합니다. 모델 이름/ID, 매니페스트 이름/ID, 이미지 ID, 서비스 이름 또는 Machine Learning 계산 리소스 ID를 사용하여 결과 목록을 필터링할 수 있습니다. 필터를 제공하지 않으면 쿼리는 계정의 모든 서비스를 표시합니다. 반환된 목록의 페이지가 매겨지고 각 페이지의 항목 수가 선택적 매개 변수로 사용됩니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| serviceName | 쿼리 | 서비스 이름입니다. | 아니오 | string |
| modelId | 쿼리 | 모델 이름입니다. | 아니오 | string |
| modelName | 쿼리 | 모델 ID입니다. | 아니오 | string |
| manifestId | 쿼리 | 매니페스트 ID입니다. | 아니오 | string |
| manifestName | 쿼리 | 매니페스트 이름입니다. | 아니오 | string |
| imageId | 쿼리 | 이미지 ID입니다. | 아니오 | string |
| computeResourceId | 쿼리 | Machine Learning 계산 리소스 ID입니다. | 아니오 | string |
| count | 쿼리 | 한 페이지에서 검색할 항목의 수입니다. | 아니오 | string |
| $skipToken | 쿼리 | 다음 페이지를 검색하기 위한 연속 토큰입니다. | 아니오 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [PaginatedServiceList](#paginatedservicelist) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>서비스 세부 정보 가져오기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>설명
ID별로 서비스를 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 개체 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [ServiceResponse](#serviceresponse) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>서비스 업데이트

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>설명
기존 서비스를 업데이트합니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 개체 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| serviceUpdateRequest | 본문 | 기존 서비스를 업데이트하는 데 사용되는 페이로드입니다. | 예 |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>응답
| 코드 | 설명 | 헤더 | 스키마 |
|--------------------|--------------------|--------------------|--------------------|
| 202 | 비동기 작업 위치 URL입니다. GET 호출은 서비스 업데이트 작업의 상태를 표시합니다. | Operation-Location |
| 404 | 지정된 ID의 서비스가 없습니다. |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>서비스 삭제

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| 삭제 |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>설명
서비스를 삭제합니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 개체 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. |  |
| 204 | 지정된 ID의 서비스가 없습니다. |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>서비스 키 가져오기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>설명
서비스 키를 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 서비스 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [AuthKeys](#authkeys)
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>서비스 키 다시 생성

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/regenerateKeys | 

### <a name="description"></a>설명
서비스 키를 다시 생성하고 반환합니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 서비스 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| regenerateKeyRequest | 본문 | 기존 서비스를 업데이트하는 데 사용되는 페이로드입니다. | 예 | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [AuthKeys](#authkeys)
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>계정의 배포 목록 쿼리

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>설명
계정의 배포 목록을 쿼리합니다. 특정 서비스에 대해 생성된 배포만 반환하는 서비스 ID를 사용하여 결과 목록을 필터링할 수 있습니다. 필터를 제공하지 않으면 쿼리는 계정의 모든 배포를 표시합니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |
| serviceId | 쿼리 | 서비스 ID입니다. | 아니오 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [DeploymentList](#deploymentlist) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>배포 세부 정보 가져오기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>설명
ID별로 배포를 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 배포 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [배포](#deployment) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>작업 세부 정보 가져오기

### <a name="request"></a>요청
| 방법 | 요청 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>설명
작업 ID별로 비동기 작업 상태를 가져옵니다.

### <a name="parameters"></a>매개 변수
| Name | 위치 | 설명 | 필수 | 스키마
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 경로 | Azure 구독 ID입니다. | 예 | string |
| resourceGroupName | 경로 | 모델 관리 계정이 있는 리소스 그룹의 이름 | 예 | string |
| accountName | 경로 | 모델 관리 계정의 이름입니다. | 예 | string |
| id | 경로 | 작업 ID입니다. | 예 | string |
| api-version | 쿼리 | 사용할 Microsoft.Machine.Learning 리소스 공급자 API의 버전입니다. | 예 | string |
| 권한 부여 | 머리글 | 권한 부여 토큰입니다. "Bearer XXXXXX"와 같아야 합니다. | 예 | string |

### <a name="responses"></a>응답
| 코드 | 설명 | 스키마 |
|--------------------|--------------------|--------------------|
| 200 | 성공. | [OperationStatus](#asyncoperationstatus) |
| 기본값 | 작업이 실패한 이유를 설명하는 오류 응답입니다. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>정의

<a name="asset"></a>
### <a name="asset"></a>자산
Docker 이미지를 만드는 동안 필요한 자산 개체입니다.


|Name|설명|스키마|
|---|---|---|
|**id**  <br>*선택 사항*|자산 ID입니다.|string|
|**mimeType**  <br>*선택 사항*|모델 콘텐츠의 MIME 형식입니다. MIME 형식에 대한 자세한 내용은 [IANA 미디어 유형 목록](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.|string|
|**unpack**  <br>*선택 사항*|Docker 이미지를 생성하는 동안 콘텐츠의 압축을 풀어야 할지 여부를 나타냅니다.|부울|
|**url**  <br>*선택 사항*|자산 위치 URL입니다.|string|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
비동기 작업 상태입니다.

*Type*: enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
작업 상태입니다.


|Name|설명|스키마|
|---|---|---|
|**createdTime**  <br>*선택 사항*  <br>*읽기 전용*|비동기 작업 생성 시간(UTC)입니다.|string (date-time)|
|**endTime**  <br>*선택 사항*  <br>*읽기 전용*|비동기 작업 종료 시간(UTC)입니다.|string (date-time)|
|**error**  <br>*선택 사항*||[ErrorResponse](#errorresponse)|
|**id**  <br>*선택 사항*|비동기 작업 ID입니다.|string|
|**operationType**  <br>*선택 사항*|비동기 작업 유형입니다.|enum (Image, Service)|
|**resourceLocation**  <br>*선택 사항*|비동기 작업에 의해 리소스가 생성/업데이트됩니다.|string|
|**state**  <br>*선택 사항*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
서비스에 대한 인증 키입니다.


|Name|설명|스키마|
|---|---|---|
|**primaryKey**  <br>*선택 사항*|기본 키입니다.|string|
|**secondaryKey**  <br>*선택 사항*|보조 키입니다.|string|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
autoscaler에 대한 설정입니다.


|Name|설명|스키마|
|---|---|---|
|**autoscaleEnabled**  <br>*선택 사항*|autoscaler를 사용하거나 사용하지 않도록 설정합니다.|부울|
|**maxReplicas**  <br>*선택 사항*|규모를 확대할 최대 pod 복제본 수입니다.  <br>**최소값**: `1`|정수|
|**minReplicas**  <br>*선택 사항*|규모를 축소할 최소 pod 복제본 수입니다.  <br>**최소값**: `0`|정수|
|**refreshPeriodInSeconds**  <br>*선택 사항*|자동 확장 트리거의 새로 고침 시간입니다.  <br>**최소값**: `1`|정수|
|**targetUtilization**  <br>*선택 사항*|자동 확장을 트리거하는 사용 백분율입니다.  <br>**최소값**: `0`  <br>**최대값**: `100`|정수|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Machine Learning 계산 리소스입니다.


|Name|설명|스키마|
|---|---|---|
|**id**  <br>*선택 사항*|리소스 ID입니다.|string|
|**type**  <br>*선택 사항*|리소스의 형식입니다.|enum (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
클러스터의 컨테이너에 대해 리소스를 예약할 구성입니다.


|Name|설명|스키마|
|---|---|---|
|**cpu**  <br>*선택 사항*|CPU 예약을 지정합니다. Kubernetes에 대한 형식: [CPU 의미](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu)를 참조하세요.|string|
|**memory**  <br>*선택 사항*|메모리 예약을 지정합니다. Kubernetes에 대한 형식: [메모리 의미](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory)를 참조하세요.|string|


<a name="deployment"></a>
### <a name="deployment"></a>배포
Azure Machine Learning 배포 인스턴스입니다.


|Name|설명|스키마|
|---|---|---|
|**createdAt**  <br>*선택 사항*  <br>*읽기 전용*|배포 만든 시간(UTC)입니다.|string (date-time)|
|**expiredAt**  <br>*선택 사항*  <br>*읽기 전용*|배포 만료 시간(UTC)입니다.|string (date-time)|
|**id**  <br>*선택 사항*|배포 ID입니다.|string|
|**imageId**  <br>*선택 사항*|이 배포에 연결된 이미지 ID입니다.|string|
|**serviceName**  <br>*선택 사항*|서비스 이름입니다.|string|
|**상태**  <br>*선택 사항*|현재 배포 상태입니다.|string|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
배포 개체의 배열입니다.

*형식*: <[배포](#deployment)> 배열


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
모델 관리 서비스 오류 세부 정보입니다.


|Name|설명|스키마|
|---|---|---|
|**code**  <br>*필수*|오류 코드|string|
|**message**  <br>*필수*|오류 메시지입니다.|string|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
모델 관리 서비스 오류 개체입니다.


|Name|설명|스키마|
|---|---|---|
|**code**  <br>*필수*|오류 코드|string|
|**details**  <br>*선택 사항*|오류 세부 정보 개체의 배열입니다.|<[ErrorDetail](#errordetail)> 배열|
|**message**  <br>*필수*|오류 메시지입니다.|string|
|**statusCode**  <br>*선택 사항*|HTTP 상태 코드입니다.|정수|


<a name="image"></a>
### <a name="image"></a>이미지
Azure Machine Learning 이미지입니다.


|Name|설명|스키마|
|---|---|---|
|**computeResourceId**  <br>*선택 사항*|Machine Learning 계산 리소스에서 만든 환경 ID입니다.|string|
|**createdTime**  <br>*선택 사항*|이미지 만든 시간(UTC)입니다.|string (date-time)|
|**creationState**  <br>*선택 사항*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*선택 사항*|이미지 설명 텍스트입니다.|string|
|**error**  <br>*선택 사항*||[ErrorResponse](#errorresponse)|
|**id**  <br>*선택 사항*|이미지 ID입니다.|string|
|**imageBuildLogUri**  <br>*선택 사항*|이미지 빌드에서 업로드한 로그의 URI입니다.|string|
|**imageLocation**  <br>*선택 사항*|만든 이미지에 대한 Azure Container Registry 위치 문자열입니다.|string|
|**imageType**  <br>*선택 사항*||[ImageType](#imagetype)|
|**manifest**  <br>*선택 사항*||[Manifest](#manifest)|
|**name**  <br>*선택 사항*|이미지 이름입니다.|string|
|**version**  <br>*선택 사항*|모델 관리 서비스에 의해 설정된 이미지 버전입니다.|정수|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Auzre Machine Learning 이미지 생성 요청입니다.


|Name|설명|스키마|
|---|---|---|
|**computeResourceId**  <br>*필수*|Machine Learning 계산 리소스에서 만든 환경 ID입니다.|string|
|**description**  <br>*선택 사항*|이미지 설명 텍스트입니다.|string|
|**imageType**  <br>*필수*||[ImageType](#imagetype)|
|**manifestId**  <br>*필수*|이미지를 만들 매니페스트의 ID입니다.|string|
|**name**  <br>*필수*|이미지 이름입니다.|string|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
이미지의 형식을 지정합니다.

*Type* : enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>매니페스트
Azure Machine Learning 매니페스트입니다.


|Name|설명|스키마|
|---|---|---|
|**assets**  <br>*필수*|자산 목록입니다.|<[자산](#asset)> 배열|
|**createdTime**  <br>*선택 사항*  <br>*읽기 전용*|매니페스트 만든 시간(UTC)입니다.|string (date-time)|
|**description**  <br>*선택 사항*|매니페스트 설명 텍스트입니다.|string|
|**driverProgram**  <br>*필수*|매니페스트의 드라이버 프로그램입니다.|string|
|**id**  <br>*선택 사항*|매니페스트 ID입니다.|string|
|**modelIds**  <br>*선택 사항*|등록된 모델의 모델 ID 목록입니다. 포함된 모델을 등록하지 않은 경우 요청이 실패합니다.|<string> 배열|
|**modelType**  <br>*선택 사항*|모델이 모델 관리 서비스에 이미 등록되어 있음을 지정합니다.|enum (Registered)|
|**name**  <br>*필수*|매니페스트 이름입니다.|string|
|**targetRuntime**  <br>*필수*||[TargetRuntime](#targetruntime)|
|**version**  <br>*선택 사항*  <br>*읽기 전용*|모델 관리 서비스에 의해 할당된 매니페스트 버전입니다.|정수|
|**webserviceType**  <br>*선택 사항*|매니페스트에서 작성될 웹 서비스의 원하는 형식을 지정합니다.|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>모델
Azure Machine Learning 모델 인스턴스입니다.


|Name|설명|스키마|
|---|---|---|
|**createdAt**  <br>*선택 사항*  <br>*읽기 전용*|모델 만든 시간(UTC)입니다.|string (date-time)|
|**description**  <br>*선택 사항*|모델 설명 텍스트입니다.|string|
|**id**  <br>*선택 사항*  <br>*읽기 전용*|모델 ID입니다.|string|
|**mimeType**  <br>*필수*|모델 콘텐츠의 MIME 형식입니다. MIME 형식에 대한 자세한 내용은 [IANA 미디어 유형 목록](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.|string|
|**name**  <br>*필수*|모델 이름입니다.|string|
|**태그**  <br>*선택 사항*|모델 태그 목록입니다.|<string> 배열|
|**unpack**  <br>*선택 사항*|Docker 이미지를 생성하는 동안 모델의 압축을 풀어야 할지 여부를 나타냅니다.|부울|
|**url**  <br>*필수*|모델의 URL입니다. 일반적으로 여기에 공유 액세스 서명 URL을 넣습니다.|string|
|**version**  <br>*선택 사항*  <br>*읽기 전용*|모델 관리 서비스에 의해 할당된 모델 버전입니다.|정수|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
모델 데이터 수집 정보입니다.


|Name|설명|스키마|
|---|---|---|
|**eventHubEnabled**  <br>*선택 사항*|서비스에 대한 이벤트 허브를 사용하도록 설정합니다.|부울|
|**storageEnabled**  <br>*선택 사항*|서비스에 대한 저장소를 사용하도록 설정합니다.|부울|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
페이지가 매겨진 이미지 목록입니다.


|Name|설명|스키마|
|---|---|---|
|**nextLink**  <br>*선택 사항*|목록에서 결과의 다음 페이지에 대한 연속 링크(절대 URI)입니다.|string|
|**값**  <br>*선택 사항*|모델 개체의 배열입니다.|<[이미지](#image)> 배열|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
페이지가 매겨진 매니페스트 목록입니다.


|Name|설명|스키마|
|---|---|---|
|**nextLink**  <br>*선택 사항*|목록에서 결과의 다음 페이지에 대한 연속 링크(절대 URI)입니다.|string|
|**값**  <br>*선택 사항*|매니페스트 개체의 배열입니다.|<[매니페스트](#manifest)> 배열|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
페이지가 매겨진 모델 목록입니다.


|Name|설명|스키마|
|---|---|---|
|**nextLink**  <br>*선택 사항*|목록에서 결과의 다음 페이지에 대한 연속 링크(절대 URI)입니다.|string|
|**값**  <br>*선택 사항*|모델 개체의 배열입니다.|<[모델](#model)> 배열|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
페이지가 매겨진 서비스 목록입니다.


|Name|설명|스키마|
|---|---|---|
|**nextLink**  <br>*선택 사항*|목록에서 결과의 다음 페이지에 대한 연속 링크(절대 URI)입니다.|string|
|**값**  <br>*선택 사항*|서비스 개체의 배열입니다.|<[ServiceResponse](#serviceresponse)> 배열|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
서비스 생성 요청입니다.


|Name|설명|스키마|
|---|---|---|
|**appInsightsEnabled**  <br>*선택 사항*|서비스에 대해 Application Insights를 사용하도록 설정합니다.|부울|
|**autoScaler**  <br>*선택 사항*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*필수*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*선택 사항*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*선택 사항*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*필수*|서비스를 만들 이미지입니다.|string|
|**maxConcurrentRequestsPerContainer**  <br>*선택 사항*|최대 동시 요청 수입니다.  <br>**최소값**: `1`|정수|
|**name**  <br>*필수*|서비스 이름입니다.|string|
|**numReplicas**  <br>*선택 사항*|언제든지 실행되는 pod 복제본의 수입니다. Autoscaler가 사용되도록 설정되면 지정할 수 없습니다.  <br>**최소값**: `0`|정수|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
서비스에 대한 키 다시 생성 요청입니다.


|Name|설명|스키마|
|---|---|---|
|**keyType**  <br>*선택 사항*|다시 생성할 키를 지정합니다.|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
서비스의 자세한 상태입니다.


|Name|설명|스키마|
|---|---|---|
|**createdAt**  <br>*선택 사항*|서비스 만든 시간(UTC)입니다.|string (date-time)|
|**ID**  <br>*선택 사항*|서비스 ID입니다.|string|
|**image**  <br>*선택 사항*||[이미지](#image)|
|**manifest**  <br>*선택 사항*||[Manifest](#manifest)|
|**models**  <br>*선택 사항*|모델의 목록입니다.|<[모델](#model)> 배열|
|**name**  <br>*선택 사항*|서비스 이름입니다.|string|
|**scoringUri**  <br>*선택 사항*|서비스의 점수를 매기기 위한 URI입니다.|string|
|**state**  <br>*선택 사항*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*선택 사항*|마지막 업데이트 시간(UTC)입니다.|string (date-time)|
|**appInsightsEnabled**  <br>*선택 사항*|서비스에 대해 Application Insights를 사용하도록 설정합니다.|부울|
|**autoScaler**  <br>*선택 사항*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*필수*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*선택 사항*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*선택 사항*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*선택 사항*|최대 동시 요청 수입니다.  <br>**최소값**: `1`|정수|
|**numReplicas**  <br>*선택 사항*|언제든지 실행되는 pod 복제본의 수입니다. Autoscaler가 사용되도록 설정되면 지정할 수 없습니다.  <br>**최소값**: `0`|정수|
|**error**  <br>*선택 사항*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
서비스 업데이트 요청입니다.


|Name|설명|스키마|
|---|---|---|
|**appInsightsEnabled**  <br>*선택 사항*|서비스에 대해 Application Insights를 사용하도록 설정합니다.|부울|
|**autoScaler**  <br>*선택 사항*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*선택 사항*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*선택 사항*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*선택 사항*|서비스를 만들 이미지입니다.|string|
|**maxConcurrentRequestsPerContainer**  <br>*선택 사항*|최대 동시 요청 수입니다.  <br>**최소값**: `1`|정수|
|**numReplicas**  <br>*선택 사항*|언제든지 실행되는 pod 복제본의 수입니다. Autoscaler가 사용되도록 설정되면 지정할 수 없습니다.  <br>**최소값**: `0`|정수|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
대상 런타임의 유형입니다.


|Name|설명|스키마|
|---|---|---|
|**properties**  <br>*필수*||<문자열, 문자열> 맵|
|**runtimeType**  <br>*필수*|런타임을 지정합니다.|enum (SparkPython, Python)|

