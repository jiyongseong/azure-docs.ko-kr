---
title: Azure 의료 분석 청사진
description: HIPAA/HITRUST 의료 분석 청사진을 배포하기 위한 지침입니다.
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: jomolesk
ms.openlocfilehash: e627019f6219509c21ca6245ee1f7f54f2e1669a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33894316"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure 보안 및 규정 준수 청사진 - HIPAA/HITRUST 의료 데이터 및 AI

## <a name="overview"></a>개요

**Azure 보안 및 규정 준수 청사진 - HIPAA/HITRUST 의료 데이터 및 AI는 Azure PaaS 솔루션의 턴키 배포를 제공하여 업계의 규정 준수 요구 사항을 충족할 수 있는 동시에 의료 데이터를 안전하게 수집, 저장, 분석 및 상호 작용하는 방법을 보여 줍니다. 청사진을 사용하면 규제되는 데이터가 있는 고객을 위해 클라우드 채택 및 활용을 촉진할 수 있습니다.**

Azure 보안 및 규정 준수 청사진 - HIPAA/HITRUST 의료 데이터 및 AI 청사진은 HIPAA(HIPAA, 건강 보험 양도 및 책임에 관한 법) 및 HITRUST(Health Information Trust Alliance)를 지원하는 안전한 PaaS(Platform as a Service) 환경을 종단 간 솔루션으로 배포하는 데 도움이 되는 도구와 지침을 제공합니다. 이 솔루션을 사용하면 안전한 다중 계층 클라우드 환경에서 개인 및 비개인 의료 레코드를 수집, 저장, 분석 및 상호 작용할 수 있습니다. 일반 참조 아키텍처를 사용하며 Microsoft Azure 적용을 간소화하도록 설계되었습니다. 제공되는 이 아키텍처는 배포 부담과 비용을 줄이기 위해 클라우드 기반 방식을 찾고 있는 조직의 요구 사항에 적합한 종단 간 솔루션을 보여 줍니다.

![](images/components.png)

이 솔루션은 의료 정보를 전자적으로 교환하기 위한 세계 표준인 FHIR(Fast Healthcare Interoperability Resources)을 사용하여 형식화된 샘플 데이터 집합을 사용하고 안전한 방식으로 저장하도록 설계되었습니다. 고객은 Azure Machine Learning을 사용하여 강력한 비즈니스 인텔리전스 도구 및 분석을 활용하여 샘플 데이터에 대한 예측을 검토할 수 있습니다. Azure Machine Learning에서 용이하게 수행할 수 있는 실험 종류의 한 가지 예로, 청사진에는 환자가 병원 시설에서 체류하는 기간을 예측하기 위한 샘플 데이터 집합, 스크립트 및 도구가 포함되어 있습니다. 

이 청사진은 고객이 특정 요구 사항에 맞게 조정하여 임상 및 운영 사용 사례 시나리오를 모두 해결하는 새로운 Azure Machine Learning 실험을 개발하기 위해 모듈식 기반으로 제공됩니다. 이는 배포 시 안전하고 준수하도록 설계되었습니다. 그러나 고객이 역할을 올바르게 구성하고 모든 수정 사항을 구현해야 합니다. 다음 사항에 유의하세요.

-   이 청사진은 고객이 HITRUST 및 HIPAA 환경에서 Microsoft Azure를 사용하는 데 도움이 되는 기준을 제공합니다.

-   청사진이 HIPAA 및 HITRUST에 맞춰 CSF(일반 보안 프레임워크)를 통해 조정되도록 설계되었지만, HIPAA 및 HITRUST 인증 요구 사항에 따라 외부 감사자가 인증할 때까지 규정을 준수하는 것으로 간주되어서는 안됩니다.

-   고객은 이 기본 아키텍처를 사용하여 빌드된 솔루션에 대해 적절한 보안 및 규정 준수 검토를 수행해야 합니다.

## <a name="deploying-the-automation"></a>자동화 배포

- 솔루션을 배포하려면 배포 가이드에서 제공하는 지침을 따릅니다. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

이 솔루션의 작동 방식을 간략히 살펴보려면 배포에 대해 설명하고 보여 주는 이 [비디오](https://aka.ms/healthblueprintvideo)를 확인하세요.

- 자주 묻는 질문은 [FAQ](https://aka.ms/healthblueprintfaq) 가이드에서 찾을 수 있습니다.

-   **아키텍처 다이어그램**. 청사진 및 사용 사례 예제 시나리오에 사용되는 참조 아키텍처를 보여 줍니다.

-   **배포 템플릿**. 이 배포에서는 설정 중 구성 매개 변수를 지정하여 아키텍처 구성 요소를 Microsoft Azure에 자동으로 설치하는 데 [Azure Resource Manager 템플릿](/azure/azure-resource-manager/resource-group-overview#template-deployment)을 사용합니다.

-   **[자동화된 배포 스크립트](https://aka.ms/healthblueprintdeploy)**. 솔루션을 배포하는 데 도움이 됩니다. 스크립트는 다음으로 구성됩니다.


-   모듈 설치 및 [전역 관리자](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 설치 스크립트는 필요한 PowerShell 모듈을 설치하고 전역 관리자 역할이 올바르게 구성되어 있는지 확인하는 데 사용됩니다. 
-   설치 PowerShell 스크립트는 솔루션을 배포하는 데 사용되며, 미리 빌드된 데모 함수가 포함된 .zip 파일을 통해 제공됩니다.

## <a name="solution-components"></a>솔루션 구성 요소


기본 아키텍처는 다음과 같은 구성 요소로 이루어집니다.

-   **[위협 모델](https://aka.ms/healththreatmodel)** 포괄적인 위협 모델은 [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168)에서 사용할 수 있는 tm7 형식으로 제공되어 솔루션의 구성 요소, 이들 간의 데이터 흐름 및 트러스트 경계를 보여 줍니다. 이 모델은 기계 학습 구성 요소 또는 다른 수정 사항을 개발할 때 고객이 시스템 인프라에서 발생할 수 있는 잠재적 위험 요소를 이해하는 데 도움이 됩니다.

-   **[고객 구현 매트릭스](https://aka.ms/healthcrmblueprint)** Microsoft Excel 통합 문서에는 관련 HITRUST 요구 사항이 나와 있으며, Microsoft와 고객이 각각의 HITRUST 요구 사항을 충족시키는 방법에 대해 설명합니다.

-   **[상태 검토.](https://aka.ms/healthreviewpaper)** 솔루션은 Coalfire systems, Inc.에서 검토되었습니다. 구현에 대한 의료 규정 준수(HIPAA 및 HITRUST) \'검토 및 지침에서 감사자의 솔루션에 대한 검토 및 청사진을 프로덕션 준비 배포로 변환하기 위한 고려 사항을 제공합니다.

# <a name="architectural-diagram"></a>아키텍처 다이어그램


![](images/refarch.png)

## <a name="roles"></a>역할


청사진에서는 병원 관리 및 환자 치료 측면에서 관리 사용자(운영자)에 대한 두 가지 역할 및 사용자에 대한 세 가지 역할을 정의하고 있습니다. 여섯 번째 역할은 감사자가 HIPAA 및 다른 규정 준수를 평가하도록 정의됩니다. Azure RBAC(역할 기반 액세스 제어)를 사용하면 기본 제공 역할 및 사용자 지정 역할을 통해 솔루션의 각 사용자에 대해 정확하게 초점을 맞춘 액세스 관리를 수행할 수 있습니다. RBAC, 역할 및 권한에 대한 자세한 내용은 [Azure Portal에서 역할 기반 액세스 제어 시작](https://docs.microsoft.com/azure/role-based-access-control/overview) 및 [Azure 역할 기반 액세스 제어의 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

### <a name="site-administrator"></a>사이트 관리자


사이트 관리자는 고객의 Azure 구독에 대한 책임이 있습니다. 전체적인 배포를 제어하지만 환자 레코드에는 액세스할 수 없습니다.

-   기본 역할 할당: [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   사용자 지정 역할 할당: 해당 없음

-   범위: 구독

### <a name="database-analyst"></a>데이터베이스 분석가

데이터베이스 분석가는 SQL Server 인스턴스와 데이터베이스를 관리합니다.
환자 레코드에는 액세스할 수 없습니다.

-   기본 역할 할당: [SQL DB 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [SQL Server 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   사용자 지정 역할 할당: 해당 없음

-   범위: ResourceGroup

 ### <a name="data-scientist"></a>데이터 과학자


데이터 과학자는 Azure Machine Learning 서비스를 운영합니다. 데이터를 가져오고, 내보내고, 관리하고, 보고서를 실행할 수 있습니다. 데이터 과학자는 환자 데이터에 액세스할 수 있지만 관리자 권한은 없습니다.

-   기본 제공 역할 할당: [저장소 계정 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   사용자 지정 역할 할당: 해당 없음

-   범위: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>CMIO(수석 의료 정보 책임자)


CMIO는 의료 조직의 정보학/기술과 의료 전문가 간의 격차를 해소합니다. 이들의 업무에는 일반적으로 분석을 통해 조직 내에서 리소스가 적절하게 할당되고 있는지 확인하는 작업이 포함됩니다.

-   기본 제공 역할 할당: 없음

### <a name="care-line-manager"></a>진료 라인 관리자


환자 진료와 직접 관련됩니다.
이 역할은 개별 환자의 상태를 모니터링하고, 직원이 환자의 특정 진료 요구 사항을 충족할 수 있는지 확인해야 합니다. 진료 라인 관리자는 환자 레코드를 추가하고 업데이트해야 합니다.

-   기본 제공 역할 할당: 없음

-   사용자 지정 역할 할당: HealthcareDemo.ps1을 실행하여 환자의 입원 및 퇴원을 모두 수행할 수 있는 권한이 있습니다.

-   범위: ResourceGroup

### <a name="auditor"></a>감사자


솔루션의 규정 준수를 평가하며, 네트워크에 직접 액세스할 수 없습니다.

-   기본 제공 역할 할당: [구독자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   사용자 지정 역할 할당: 해당 없음

-   범위: 구독

## <a name="example-use-case"></a>사용 사례 예제


이 청사진에 포함된 사용 사례 예제는 청사진을 사용하여 클라우드의 의료 데이터에 대한 기계 학습 및 분석을 활성화하는 방법을 보여 줍니다. Contosoclinic은 미국에 있는 작은 병원입니다. 병원 네트워크 관리자는 운영 작업 효율성을 높이고 제공할 수 있는 진료의 품질을 향상시키기 위해 Azure Machine Learning을 사용하여 입원 당시의 환자 체류 기간을 더 효율적으로 예측하려고 합니다.

### <a name="predicting-length-of-stay"></a>체류 기간 예측


사용 사례 예제 시나리오에서는 Azure Machine Learning을 사용하여 환자 입원에서 가져온 의료 세부 정보와 이전 환자로부터 집계된 과거 데이터를 비교하여 새로 입원한 환자의 체류 기간을 예측합니다.
청사진에는 솔루션의 학습 및 예측 기능을 보여 주기 위해 익명화된 대규모 의료 레코드 집합이 포함되어 있습니다. 프로덕션 배포에서 고객은 자신의 레코드를 사용하여 환경, 시설 및 환자의 고유한 세부 정보를 반영하는 더 정확한 예측을 위해 솔루션을 학습합니다.

### <a name="users-and-roles"></a>사용자 및 역할


**사이트 관리자 - Alex**

*이메일: Alex\_SiteAdmin*

Alex의 업무는 온-프레미스 네트워크를 관리하는 부담을 줄이고 관리 비용을 절감할 수 있는 기술을 평가하는 것입니다. Alex는 몇 시간 동안 Azure를 평가하고 있지만 환자 데이터를 클라우드에 저장하기 위해 HiTrust 규정 준수 요구 사항을 충족하는 데 필요한 서비스를 구성하기 위해 노력했습니다. Alex는 Azure 의료 AI를 선택하여 HiTrust에 대한 고객 요구 사항을 충족하기 위한 요구 사항을 해결한 규정 준수 준비 의료 솔루션을 배포했습니다.

**데이터 과학자 - Debra**

*이메일: Debra\_DataScientist*

Debra는 의료 레코드를 분석하여 환자 진료에 대한 정보를 제공하는 모델을 사용하고 만드는 업무를 담당하고 있습니다. Debra는 SQL 및 R 통계 프로그래밍 언어를 사용하여 모델을 만듭니다.

**데이터베이스 분석가 - Danny**

*이메일: Danny\_DBAnalyst*

Danny는 Contosoclinic의 모든 환자 데이터를 저장하는 Microsoft SQL Server와 관련된 모든 항목에 대한 기본 연락처입니다. Danny는 최근에 Azure SQL Database에 익숙해진 숙련된 SQL Server 관리자입니다.

**CMIO - Caroline**

Caroline은 Chris(진료 라인 관리자) 및 Debra(데이터 과학자)와 협력하여 환자의 체류 기간에 영향을 주는 요소를 결정합니다.
Caroline은 LOS(체류 기간) 솔루션의 예측을 사용하여 리소스가 병원 네트워크에서 적절하게 할당되고 있는지 확인합니다. 예를 들어 이 솔루션에 제공된 대시보드를 사용합니다.

**진료 라인 관리자 - Chris**

*이메일: Chris\_CareLineManager*

Contosoclinic에서 환자의 입원과 퇴원을 직접 관리해야 하는 개인으로서 Chris는 LOS 솔루션에서 생성된 예측을 사용하여 환자가 시설에 체류하고 있는 동안 적절한 직원이 환자를 돌볼 수 있도록 합니다.

**감사자 - Han**

*이메일: Han\_Auditor*

Han은 ISO, SOC 및 HiTrust에 대한 감사 경험이 있는 공인 감사자이며, Contosoclinc의 네트워크를 검토하기 위해 고용되었습니다. Han은 청사진 및 LOS 솔루션을 통해 중요한 개인 데이터를 저장, 처리 및 표시할 수 있도록 하기 위해 솔루션과 함께 제공된 고객 책임 매트릭스를 검토할 수 있습니다.


# <a name="design-configuration"></a>디자인 구성


이 섹션에서는 청사진에 기본 제공된 기본 구성 및 보안 조치에 대해 자세히 설명합니다.

- **수집** - 데이터 원시 원본(FHIR 데이터 원본 포함)을 수집합니다.
- **저장** - 중요한 정보를 저장합니다.
- **분석** - 결과를 분석하고 예측합니다.
- **상호 작용** - 결과 및 예측과 상호 작용합니다.
- **ID** - 솔루션에서 ID를 관리합니다.
- **보안** - 보안 기능을 사용하도록 설정합니다.


## <a name="identity"></a>ID 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory 및 RBAC(역할 기반 액세스 제어)


**인증:**

-   [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft\'의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. SQL Database에 액세스하는 사용자를 포함하여 모든 솔루션 사용자가 Azure Active Directory에서 만들어졌습니다.



-   응용 프로그램에 대한 인증은 Azure AD를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요.

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 미치는 잠재적인 취약성을 검색하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 응답을 구성하며, 의심스러운 인시던트를 조사하고, 이를 해결하기 위해 적절한 조치를 수행합니다.

-   [Azure RBAC(Role-based Access Control)](/azure/role-based-access-control/role-assignments-portal)은 Azure에 대해 세밀하게 초점을 맞춘 액세스 관리를 구현합니다. 구독 액세스는 구독 관리자로 제한되며, Azure Key Vault 액세스는 사이트 관리자로 제한됩니다. 강력한 암호(12자 이상, 하나 이상의 대/소문자, 숫자 및 특수 문자 포함)가 필요합니다.

-   배포 시 -enableMFA 스위치를 사용하도록 설정되면 다단계 인증이 지원됩니다.

-   배포 시 -enableADDomainPasswordPolicy 스위치를 사용하도록 설정되면 60일 후에 암호가 만료됩니다.

**역할:**

-   솔루션에서 [기본 제공 역할](/azure/role-based-access-control/built-in-roles)을 사용하여 리소스에 대한 액세스를 관리합니다.

-   기본적으로 특정 기본 제공 역할이 모든 사용자에게 할당됩니다.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Key Vault에 저장된 데이터에는 다음 항목이 포함됩니다.

    -   Application Insight 키
    -   환자 데이터 저장소 액세스 키
    -   환자 연결 문자열
    -   환자 데이터 테이블 이름
    -   Azure ML 웹 서비스 엔드포인트
    -   Azure ML 서비스 API 키

-   필요에 따라 고급 액세스 정책이 구성됩니다.
-   Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
-   Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
-   Key Vault의 모든 키는 HSM \[키 유형 = HSM 보호 2,048비트 RSA 키\]로 보호됩니다.
-   모든 사용자/ID는 RBAC(역할 기반 액세스 제어)를 사용하여 최소 필수 권한을 부여받습니다.
-   응용 프로그램이 서로 신뢰하고 런타임 시 동일한 비밀에 액세스할 필요가 없으면 응용 프로그램에서 Key Vault를 공유하지 않습니다.
-   Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
-   키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

## <a name="ingest"></a>수집 

### <a name="azure-functions"></a>Azure 기능
솔루션에서 [Azure Functions](/azure/azure-functions/)를 사용하여 분석 데모에 사용된 체류 기간 데이터 샘플을 처리하도록 설계되었습니다. 함수에 세 가지 기능이 만들어졌습니다.

**1. 고객 데이터 phi 데이터 대량 가져오기**

데모 스크립트. .\\HealthcareDemo.ps1을 **BulkPatientAdmission** 스위치와 함께 사용하는 경우 **데모 배포 및 실행**의 설명에 따라 다음 처리 파이프라인을 실행합니다.
1. **Azure Blob Storage** - 환자 데이터 .csv 파일 샘플이 저장소에 업로드됩니다.
2. **Event Grid** - 이벤트에서 Azure Function(대량 가져오기 - Blob 이벤트)에 데이터를 게시합니다.
3. **Azure Function** - 처리를 수행하고 보안 함수 - 이벤트(유형, Blob URL)를 사용하여 데이터를 SQL 저장소에 저장합니다.
4. **SQL DB** - 분류에 대한 태그를 사용하는 환자 데이터용 데이터베이스 저장소이며, ML 프로세스를 시작하여 학습 실험을 수행합니다.

![](images/dataflow.png)

또한 Azure Function에서 다음 태그를 사용하여 샘플 데이터 집합에 지정된 중요한 데이터를 읽고 보호하도록 설계되었습니다.
- dataProfile => "ePHI"
- owner => \<사이트 관리자 UPN\>
- environment => "파일럿"
- department => "전역 에코시스템" 환자 'names'(이름)가 일반 텍스트로 식별되는 샘플 데이터 집합에 태그 지정이 적용되었습니다.

**2. 새 환자 입원**

데모 스크립트. .\\HealthcareDemo.ps1을 **BulkPatientadmission** 스위치와 함께 사용하는 경우 **데모 배포 및 실행**의 설명에 따라 다음 처리 파이프라인을 실행합니다.![](images/securetransact.png)
**1. Azure Function**이 트리거되고 함수에서 Azure Active 디렉터리의 [전달자 토큰](/rest/api/)을 요청합니다.

**2. Key Vault**에 요청한 토큰과 연결된 비밀을 요청합니다.

**3. Azure 역할에서 요청의 유효성을 검사하고 액세스 요청에 대한 권한을 Key Vault에 부여합니다.

**4. Key Vault**에서 비밀(이 경우 SQL DB 연결 문자열)을 반환합니다.

**5. Azure Function**에서 연결 문자열을 사용하여 SQL Database에 안전하게 연결하고 ePHI 데이터를 저장하기 위한 추가 처리를 계속합니다.

데이터 저장소를 얻기 위해 FHIR(Fast Healthcare Interoperability Resources, '파이어'로 발음)에 따라 공통 API 스키마가 구현되었습니다. 함수에서 다음과 같은 FHIR 교환 요소가 제공되었습니다.

-   [Patient 스키마](https://www.hl7.org/fhir/patient.html)는 환자에 대한 "인물" 정보를 다루고 있습니다.

-   [Observation 스키마](https://www.hl7.org/fhir/observation.html)는 진단을 지원하고, 진행 상황을 모니터링하며, 기준과 패턴을 결정하고, 인구 통계학적 특성을 파악하는 데 사용되는 의료 관리의 핵심 요소를 다루고 있습니다. 

-   [Encounter 스키마](https://www.hl7.org/fhir/encounter.html)는 외래, 응급, 가정 보건, 입원 환자 및 가상 대면과 같은 대면 진료의 유형을 다루고 있습니다.

-   [Condition 스키마](https://www.hl7.org/fhir/condition.html)는 관심 수준으로 상승한 조건, 문제, 진단 또는 기타 이벤트, 상황, 결과 또는 임상 개념에 대한 자세한 정보를 다루고 있습니다.  



### <a name="event-grid"></a>Event Grid


솔루션은 모든 원본에서 대상으로의 모든 이벤트 라우팅을 관리하기 위한 단일 서비스인 Azure Event Grid를 지원하여 다음을 제공합니다.

-   [보안 및 인증](/azure/event-grid/security-authentication)

-   [역할 기반 액세스 제어](/azure/event-grid/security-authentication#management-access-control) - 이벤트 구독 나열, 새 구독 만들기 및 키 생성과 같은 다양한 관리 작업에 대한 제어입니다.

-   감사

## <a name="store"></a>저장 

### <a name="sql-database-and-server"></a>SQL Database 및 SQL Server 


-   [TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)는 Azure Key Vault에 저장된 키를 사용하여 Azure SQL Database에 저장된 데이터의 실시간 암호화 및 해독을 제공합니다.

-   [SQL 취약성 평가](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 간편한 구성 도구입니다.

-   [SQL Database 위협 탐지](/azure/sql-database/sql-database-threat-detection) 사용

-   [SQL Database 감사](/azure/sql-database/sql-database-auditing) 사용

-   [SQL Database 메트릭 및 진단 로깅](/azure/sql-database/sql-database-metrics-diag-logging) 사용

-   [서버 및 데이터베이스 수준 방화벽 규칙](/azure/sql-database/sql-database-firewall-configure)이 강화되었습니다.

-   [Always Encrypted 열](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)이 환자의 이름, 중간 이름 및 성을 보호하는 데 사용됩니다.
    또한 데이터베이스 열 암호화도 Azure AD(Azure Active Directory)를 사용하여 Azure SQL Database에 응용 프로그램을 인증합니다.

-   SQL Database 및 SQL Server에 대한 액세스는 최소 권한 원칙에 따라 구성됩니다.

-   필수 IP 주소만 SQL 방화벽을 통해 액세스할 수 있습니다.

### <a name="storage-accounts"></a>Storage 계정


-   [이동 중인 데이터는 TLS/SSL을 통해서만 전송됩니다](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   컨테이너에 대한 익명 액세스가 허용되지 않습니다.

-   익명 활동을 추적하기 위한 경고 규칙이 구성됩니다.

-   HTTPS는 저장소 계정 리소스에 액세스하는 데 필요합니다.

-   인증 요청 데이터가 기록되고 모니터링됩니다.

-   Blob 저장소의 미사용 데이터가 암호화됩니다.

## <a name="analyze"></a>분석

### <a name="machine-learning"></a>Machine Learning


-   Machine Learning 웹 서비스에 대해 [로깅을 사용하도록 설정](/azure/machine-learning/studio/web-services-logging)됩니다.
- [Machine Learning](/azure/machine-learning/desktop-workbench/experimentation-service-configuration) Workbench를 사용하면 솔루션 집합을 예측하는 기능을 제공하는 실험을 개발할 필요가 있습니다. [Workbench를 통합](/azure/machine-learning/desktop-workbench/using-git-ml-project)하면 실험 관리를 간소화할 수 있습니다.

## <a name="security"></a>보안

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/)는 모든 Azure 리소스의 보안 상태에 대한 중앙 집중식 보기를 제공합니다. 적절한 보안 제어가 준비되고 올바르게 구성되었는지와 주의가 필요한 리소스를 한눈에 빠르게 확인할 수 있습니다. 

- [Azure Advisor](/azure/advisor/advisor-overview)는 Azure 배포를 최적화하기 위한 모범 사례를 따르는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. 리소스 구성 및 사용량 원격 분석을 수행하고 Azure 리소스의 경제성, 성능, 고가용성 및 보안을 개선하는 데 도움이 되는 해결 방법을 권장합니다.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장성 있는 APM(Application Performance Management) 서비스입니다. 이를 사용하여 라이브 웹 응용 프로그램을 모니터링합니다. 성능 이상을 검색합니다. 사용자가 문제를 진단하고 앱을 사용하여 실제로 수행할 작업을 이해할 수 있도록 돕는 강력한 분석 도구를 포함합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

### <a name="azure-alerts"></a>Azure Alerts
- Alerts는 Azure 서비스를 모니터링하는 방법을 제공하며, 데이터에 대한 조건을 구성할 수 있도록 합니다. 또한 경고 조건이 모니터링 데이터와 일치할 때 알림을 제공합니다.

### <a name="operations-management-suite-oms"></a>OMS(Operations Management Suite)
[OMS(Operations Management Suite)](/azure/operations-management-suite/operations-management-suite-overview)는 관리 서비스의 모음입니다.

-   Security Center에 대한 작업 영역을 사용할 수 있습니다.

-   작업 모니터링에 대한 작업 영역을 사용할 수 있습니다.

-   작업 모니터링에 사용할 수 있는 항목은 다음과 같습니다.

    -   ID 및 액세스

    -   보안 및 감사

    -   Azure SQL DB 분석

    -   [Azure Web Apps 분석](/azure/log-analytics/log-analytics-azure-web-apps-analytics) 솔루션

    -   Key Vault 분석

    -   변경 내용 추적

-   [Application Insights 커넥터(미리 보기)](/azure/log-analytics/log-analytics-app-insights-connector) 사용

-   [활동 로그 분석](/azure/log-analytics/log-analytics-activity) 사용
