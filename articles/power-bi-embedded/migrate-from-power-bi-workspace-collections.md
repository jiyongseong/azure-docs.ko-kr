---
title: Power BI 작업 영역 컬렉션 콘텐츠를 Power BI Embedded로 마이그레이션하는 방법 | Microsoft Docs
description: Power BI 작업 영역 컬렉션에서 Power BI Embedded로 마이그레이션을 수행하고 앱에서 개선된 포함 기능을 활용하는 방법을 알아봅니다.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: d138b0c26ffc0a44947f79811fd586dda7df4509
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Power BI 작업 영역 컬렉션 콘텐츠를 Power BI Embedded로 마이그레이션하는 방법

Power BI 작업 영역 컬렉션에서 Power BI Embedded로 마이그레이션하는 방법을 알아봅니다. 이 문서에서는 Azure Power BI 작업 영역 컬렉션에서 Power BI Embedded로 마이그레이션하기 위한 지침을 제공합니다. 또한 예상되는 응용 프로그램 변경 내용도 확인합니다.

Power BI Premium의 일반 공급 버전이 릴리스된 후에 제한된 기간 동안 Power BI 작업 영역 컬렉션 리소스를 계속 사용할 수 있습니다. 기업계약을 맺은 고객은 기존 계약이 만료될 때까지 기존 작업 영역 컬렉션에 액세스할 수 있습니다. 직접 채널 또는 CSP 채널을 통해 Power BI 작업 영역 컬렉션을 획득한 고객은 Power BI Premium 일반 공급으로부터 1년 동안 계속 제품에 액세스할 수 있습니다.

> [!IMPORTANT]
> 이 마이그레이션은 Power BI 서비스에 종속되지만, 응용 프로그램 사용자는 **Embed 토큰**을 사용할 경우 Power BI에 종속되지 않습니다. 즉, 응용 프로그램에서 포함된 콘텐츠를 보기 위해 Power BI에 등록할 필요가 없습니다. 고객을 위해 이 Power BI 포함 접근 방식을 사용할 수 있습니다.

![Power BI Embedded 흐름](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>마이그레이션 준비

Power BI 작업 영역 컬렉션 서비스에서 Power BI Embedded로의 마이그레이션 준비를 위해 수행해야 하는 몇 가지 작업이 있습니다. Power BI Pro 라이선스가 있는 사용자와 사용할 수 있는 테넌트가 필요합니다.

1. Azue AD(Azure Active Directory) 테넌트에 액세스할 수 있는지 확인합니다.

    어떤 테넌트를 사용하나요?

    * 기존 회사 Power BI 테넌트를 사용하나요?
    * 응용 프로그램을 위한 별도 테넌트를 사용하나요?
    * 고객별로 별도 테넌트를 사용하나요?

    응용 프로그램 또는 각 고객을 위해 새 테넌트를 만들려는 경우 다음 중 하나를 참조하세요.

    * [Azure Active Directory 테넌트 만들기](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Azure Active Directory 테넌트를 얻는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)

2. 응용 프로그램 "마스터" 계정 역할을 하는 사용자를 이 새 테넌트 내에서 만듭니다. 이 계정은 Power BI에 등록해야 하며 Power BI Pro 라이선스가 할당되어야 합니다.

## <a name="accounts-within-azure-ad"></a>Azure AD 내 계정

다음 계정이 테넌트 내에 존재해야 합니다.

> [!NOTE]
> 앱 작업 영역을 사용하려면 이러한 계정에 Power BI Pro 라이선스가 있어야 합니다.

1. 테넌트 관리자 사용자.

    포함 앱 작업 영역에 구성원으로 나열되는 테넌트 관리자가 있는 것이 좋습니다.

2. 콘텐츠를 만드는 분석가용 계정.

    필요에 따라 이러한 사용자를 앱 작업 영역에 할당해야 합니다.

3. 응용 프로그램 *마스터* 사용자 계정 또는 서비스 계정.

    응용 프로그램 백 엔드는 이 계정에 대한 자격 증명을 저장합니다. Power BI REST API에서 사용할 Azure AD 토큰을 획득하기 위해 *마스터* 계정을 사용합니다. 이 계정은 응용 프로그램에 대한 Embed 토큰을 생성하는 데 사용됩니다. *마스터* 계정은 포함을 위해 만든 앱 작업 영역의 관리자여야 합니다.

    **이 계정은 포함을 위해 사용되는 조직의 일반 사용자 계정일 뿐입니다.**

## <a name="app-registration-and-permissions"></a>앱 등록 및 사용 권한

REST API 호출을 수행하려면 Azure AD에 응용 프로그램을 등록합니다. 추가 구성이 Power BI 앱 등록 페이지 외에 Microsoft Azure Portal 내에도 적용됩니다. 자세한 내용은 [Azure AD 앱을 등록하여 Power BI 콘텐츠 포함](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)을 참조하세요.

응용 프로그램 **마스터** 계정을 사용하여 응용 프로그램을 등록하는 것이 좋습니다.

## <a name="create-app-workspaces-required"></a>앱 작업 영역 만들기(필수)

응용 프로그램이 여러 고객에게 서비스를 제공하는 경우 앱 작업 영역을 사용하여 더 나은 격리를 제공할 수 있습니다. 대시보드 및 보고서는 고객 간에 격리됩니다. 그러면 앱 작업 영역별 Power BI 계정을 사용하여 고객 간에 응용 프로그램 환경을 좀 더 격리할 수 있지만 하나의 계정만 사용해서 간소화할 수 있습니다.

> [!IMPORTANT]
> 고객에 대한 포함을 위해 개인 작업 영역("내 작업 영역")을 사용할 수 없습니다.

Power BI 내에서 앱 작업 영역을 만들려면 Pro 라이선스를 보유하는 사용자가 필요합니다. 앱 작업 영역을 만드는 Power BI 사용자는 기본적으로 작업 영역의 관리자입니다. **응용 프로그램 *마스터* 계정은 작업 영역의 관리자여야 합니다.**

## <a name="content-migration"></a>콘텐츠 마이그레이션

작업 영역 컬렉션에서 Power BI Embedded로의 콘텐츠 마이그레이션은 현재 솔루션과 동시에 수행할 수 있으며 가동 중지 시간이 필요하지 않습니다.

Power BI 작업 영역 컬렉션에서 Power BI Embedded로의 콘텐츠 복사를 지원하기 위해 **마이그레이션 도구**를 사용할 수 있습니다. 보고서가 많은 경우에 특히 유용합니다. 자세한 내용은 [Power BI Embedded 마이그레이션 도구](migrate-tool.md)를 참조하세요.

콘텐츠 마이그레이션은 주로 두 개의 API에 의존합니다.

1. PBIX 다운로드 - 이 API는 2016년 10월 이후에 Power BI에 업로드된 PBIX 파일을 다운로드할 수 있습니다.
2. PBIX 가져오기 - 이 API는 Power BI로 모든 PBIX를 업로드합니다.

일부 관련 코드 조각을 보려면 [Power BI Embedded에서의 콘텐츠 마이그레이션과 관련된 코드 조각](migrate-code-snippets.md)을 참조하세요.

### <a name="report-types"></a>보고서 유형

몇 가지 유형의 보고서가 있으며 유형마다 다른 마이그레이션 흐름이 필요합니다.

#### <a name="cached-dataset-and-report"></a>캐시된 데이터 집합 및 보고서

캐시된 데이터 집합은 라이브 연결 또는 DirectQuery 연결과 달리, 가져온 데이터가 있는 PBIX 파일을 가리킵니다.

**흐름**

1. Power BI 작업 영역 컬렉션 작업 영역에서 PBIX 다운로드 API를 호출합니다.
2. PBIX를 저장합니다.
3. Power BI Embedded 작업 영역에 대한 PBIX 가져오기를 호출합니다.

#### <a name="directquery-dataset-and-report"></a>DirectQuery 데이터 집합 및 보고서

**흐름**

1. GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources를 호출하고 수신하는 연결 문자열을 저장합니다.
2. Power BI 작업 영역 컬렉션 작업 영역에서 PBIX 다운로드 API를 호출합니다.
3. PBIX를 저장합니다.
4. Power BI Embedded 작업 영역에 대한 PBIX 가져오기를 호출합니다.
5. 호출하여 연결 문자열 업데이트 - POST https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections
6. 호출하여 GW ID 및 데이터 원본 ID 가져오기 - GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. 호출하여 사용자의 자격 증명 업데이트 - PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>이전 데이터 집합 및 보고서

2016년 10월 이전에 업로드한 보고서는 PBIX 다운로드 기능을 지원하지 않습니다.

**흐름**

1. 개발 환경에서 PBIX를 가져옵니다(내부 소스 제어).
2. Power BI Embedded 작업 영역에 대한 PBIX 가져오기를 호출합니다.

#### <a name="push-dataset-and-report"></a>푸시 데이터 집합 및 보고서

PBIX 다운로드는 *푸시 API* 데이터 집합을 지원하지 않습니다. 푸시 API 데이터 집합 데이터를 Power BI 작업 영역 컬렉션에서 Power BI Embedded로 이식할 수 없습니다.

**흐름**

1. Json 데이터 집합을 통해 "데이터 집합 만들기" API를 호출하여 Power BI Embedded 작업 영역에 대한 데이터 집합을 만듭니다.
2. 만든 데이터 집합*에 대한 보고서를 다시 작성합니다.

다음을 통해 일부 해결 방법을 사용하여 푸시 API 보고서를 Power BI 작업 영역 컬렉션에서 Power BI Embedded로 마이그레이션할 수 있습니다.

1. 일부 더미 PBIX를 Power BI 작업 영역 컬렉션 작업 영역으로 업로드합니다.
2. 푸시 API 보고서를 복제한 후 1단계의 더미 PBIX에 바인딩합니다.
3. 더미 PBIX를 사용하여 푸시 API 보고서를 다운로드합니다.
4. 더미 PBIX를 Power BI Embedded 작업 영역으로 업로드합니다.
5. Power BI Embedded 작업 영역에 푸시 데이터 집합을 만듭니다.
6. 보고서를 푸시 API 데이터 집합에 다시 바인딩합니다.

## <a name="create-and-upload-new-reports"></a>새 보고서 만들기 및 업로드

Power BI 작업 영역 컬렉션에서 마이그레이션한 콘텐츠뿐 아니라 Power BI Desktop을 사용하여 보고서 및 데이터 집합을 만든 후 해당 보고서를 앱 작업 영역에 게시할 수 있습니다. 보고서를 게시하는 최종 사용자는 앱 작업 영역에 게시하려면 Power BI Pro 라이선스가 있어야 합니다.

## <a name="rebuild-your-application"></a>응용 프로그램 다시 빌드

1. Power BI REST API 및 powerbi.com 내의 보고서 위치를 사용하도록 응용 프로그램을 수정합니다.

2. 응용 프로그램에 대한 *마스터* 계정을 사용하여 AuthN/AuthZ 인증을 다시 작성합니다. [Embed 토큰](https://msdn.microsoft.com/library/mt784614.aspx)을 사용하여 이 사용자가 다른 사용자 대신 작업하도록 할 수 있습니다.

3. Power BI Embedded에서 응용 프로그램으로 보고서를 포함합니다. 자세한 내용은 [Power BI 대시보드, 보고서 및 타일을 포함하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)을 참조하세요.

## <a name="map-your-users-to-a-power-bi-user"></a>사용자를 Power BI 사용자에 매핑

응용 프로그램 내에서 관리하는 사용자를 응용 프로그램 용도에 맞게 *마스터* Power BI 자격 증명에 매핑합니다. 이 Power BI *마스터* 계정에 대한 자격 증명은 응용 프로그램 내에 저장되며 Embed 토큰을 만드는 데 사용됩니다.

## <a name="what-to-do-when-you-are-ready-for-production"></a>프로덕션 사용 준비가 완료되었을 때 수행할 작업

프로덕션으로 이동할 준비가 되면 다음을 수행해야 합니다.

- 개발을 위해 별도 테넌트를 사용하는 경우 대시보드 및 보고서와 함께 앱 작업 영역을 프로덕션 환경에서 사용할 수 있는지 확인해야 합니다. 프로덕션 테넌트를 위해 Azure AD에서 응용 프로그램을 만들고 1단계에 명시된 대로 적절한 앱 사용 권한을 할당했는지 확인합니다.

- 필요에 맞는 용량을 구입합니다. [포함된 분석 용량 계획 백서](https://aka.ms/pbiewhitepaper)를 사용하여 필요한 항목을 이해할 수 있습니다. 구매 준비가 되면 Azure Portal 내에서 Power BI Embedded 리소스를 구입할 수 있습니다.

- 앱 작업 영역을 편집하고 고급에서 용량에 할당합니다.

    ![powerbi.com 내에서 용량에 앱 작업 영역 할당](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- 업데이트된 응용 프로그램을 프로덕션에 배포하고 Power BI Embedded에서 보고서 포함을 시작합니다.

## <a name="after-migration"></a>마이그레이션 후

Power BI 작업 영역 컬렉션 내에서 일부 정리 작업이 필요합니다.

- Power BI 작업 영역 컬렉션의 Azure 서비스 내에서 배포된 솔루션의 모든 작업 영역을 제거합니다.
- Azure 내에 있는 모든 작업 영역 컬렉션을 삭제합니다.

## <a name="next-steps"></a>다음 단계

축하합니다. 이제 응용 프로그램이 Power BI Embedded로 마이그레이션됩니다. Power BI 대시보드, 보고서 및 데이터 집합을 포함하는 방법에 대한 내용은 [Power BI 대시보드, 보고서 및 타일을 포함하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)을 참조하세요.

궁금한 점이 더 있나요? [Power BI 커뮤니티에 질문하세요.](http://community.powerbi.com/)