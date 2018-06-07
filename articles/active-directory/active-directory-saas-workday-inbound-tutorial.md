---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Workday 구성 | Microsoft Docs'
description: Active Directory 및 Azure Active Directory의 ID 데이터의 원본으로 Workday를 사용하는 방법에 대해 알아봅니다.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2018
ms.author: asmalser
ms.openlocfilehash: 8dbe995ac3c6799c2fa17d9faa8be0cb74d6ee23
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259000"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Workday 구성

이 자습서의 목표는 Workday에서 Active Directory 및 Azure Active Directory로 사람을 가져오기 위해 수행해야 하는 단계, 그리고 선택 사항으로 일부 특성을 Workday에 쓰는 방법을 보여주는 것입니다. 



## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로비전 서비스](active-directory-saas-app-provisioning.md)는 사용자 계정을 프로비전하기 위해 [Workday 인적 자원 API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)와 통합됩니다. Azure AD는 이 연결을 사용하여 다음 사용자 프로비전 워크플로를 가능하게 합니다.

* **사용자를 Active Directory에 프로비전** - Workday에서 선택한 사용자 집합을 하나 이상의 Active Directory 포리스트와 동기화합니다. 

* **클라우드 전용 사용자를 Azure Active Directory에 프로비전** - Active Directory와 Azure Active Directory에 모두 있는 하이브리드 사용자는 [AAD Connect](connect/active-directory-aadconnect.md)를 사용하여 Azure Active Directory에 프로비전할 수 있습니다. 그러나 클라우드 전용 사용자는 Azure AD 사용자 프로비전 서비스를 사용하여 Workday에서 직접 Azure Active Directory에 프로비전할 수 있습니다.

* **Workday에 이메일 주소 쓰기 저장** - Azure AD 사용자 프로비전 서비스는 선택한 Azure AD 사용자 특성(예: 이메일 주소)을 Workday에 쓸 수 있습니다.

### <a name="what-human-resources-scenarios-does-it-cover"></a>여기서 다루는 인적 자원 시나리오

Azure AD 사용자 프로비전 서비스에서 지원되는 Workday 사용자 프로비전 워크플로는 다음과 같은 인적 자원 및 ID 수명 주기 관리 시나리오의 자동화를 지원합니다.

* **신규 직원 고용** - 신규 직원이 Workday에 추가되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](active-directory-saas-app-provisioning.md)에서 사용자 계정이 자동으로 생성되며, Workday에 이메일 주소를 다시 씁니다.

* **직원 특성 및 프로필 업데이트** - Workday에서 이름, 직함, 관리자 등의 직원 레코드가 업데이트되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](active-directory-saas-app-provisioning.md)에서 해당 사용자 계정이 자동으로 업데이트됩니다.

* **직원 퇴사** - Workday에서 직원이 퇴사하면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](active-directory-saas-app-provisioning.md)에서 해당 사용자 계정이 자동으로 비활성화됩니다.

* **직원 재고용** - Workday에서 직원이 다시 고용되면 Active Directory, Azure Active Directory 그리고 선택적으로 Office 365 및 [Azure AD에서 지원하는 기타 SaaS 응용 프로그램](active-directory-saas-app-provisioning.md)에서 해당 직원의 기존 계정이 자동으로 다시 활성화되거나 다시 프로비전됩니다(기본 설정에 따라).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 Workday 사용자 프로비전 솔루션은 현재 공개 미리 보기 상태이며 다음과 같은 경우에 가장 적합합니다.

* Workday 사용자 프로비전에 미리 작성된 클라우드 기반 솔루션을 원하는 조직

* Workday에서 Active Directory 또는 Azure Active Directory로 직접 사용자 프로비전이 필요한 조직

* Workday HCM 모듈에서 가져온 데이터를 사용하여 사용자를 프로비전해야 하는 조직([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 참조) 

* Workday HCM 모듈에서 감지된 변경 내용 정보에 따라서만 하나 이상의 Active Directory 포리스트, 도메인 및 OU를 동기화하도록 사용자를 조인하고, 이동시키고, 유지해야 하는 조직([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 참조)

* 이메일에 Office 365를 사용하는 조직


## <a name="planning-your-solution"></a>솔루션 계획

Workday 통합을 시작하기 전에 다음과 같은 필수 조건을 확인하고, 현재 Active Directory 아키텍처 및 사용자 프로비전 요구 사항을 Azure Active Directory에서 제공하는 솔루션과 일치시키는 방법에 대한 지침을 읽어보세요.

### <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 전역 관리자 액세스 권한이 있는 유효한 Azure AD Premium P1 구독
* 테스트 및 통합을 위한 Workday 구현 테넌트
* 테스트 목적으로 시스템 통합 사용자를 만들고 직원 데이터를 변경하기 위한 관리자 권한
* Active Directory에 사용자 프로비전의 경우 [온-프레미스 동기화 에이전트](https://go.microsoft.com/fwlink/?linkid=847801)를 호스트하려면 Windows Service 2012 이상을 실행하는 도메인에 가입된 서버가 필요합니다.
* Active Directory와 Azure AD 간의 동기화를 위한 [Azure AD Connect](connect/active-directory-aadconnect.md)

### <a name="solution-architecture"></a>솔루션 아키텍처

Azure AD는 Workday에서 Active Directory, Azure AD, SaaS 앱 등으로 프로비전하고 ID 수명 주기 관리 문제를 해결할 수 있는 다양한 프로비전 커넥터를 제공합니다. 사용하게 될 기능과 솔루션 설정 방법은 조직의 환경과 요구 사항에 따라 달라집니다. 첫 번째 단계로 조직에서 다음 항목 중 몇 개를 갖고 있으며 몇 개가 배포되었는지 점검합니다.

* Active Directory 포리스트를 몇 개나 사용하고 있나요?
* Active Directory 도메인을 몇 개나 사용하고 있나요?
* Active Directory OU(조직 구성 단위)를 몇 개나 사용하고 있나요?
* Azure Active Directory 테넌트를 몇 개나 사용하고 있나요?
* Active Directory와 Azure Active Directory에 모두 프로비전해야 하는 사용자(예: "하이브리드" 사용자)가 있나요?
* Azure Active Directory에만 프로비전하고 Active Directory에는 프로비전하면 안 되는 사용자(예: "클라우드 전용" 사용자)가 있나요?
* 사용자 이메일 주소를 Workday에 다시 써야 하나요?


이러한 질문에 대답한 후에는 아래 지침에 따라 Workday 프로비전 배포를 계획할 수 있습니다.

#### <a name="using-provisioning-connector-apps"></a>프로비전 커넥터 앱 사용

Azure Active Directory는 Workday용으로 사전 통합된 프로비전 커넥터와 수많은 기타 SaaS 응용 프로그램을 지원합니다. 

단일 프로비전 커넥터가 단일 원본 시스템의 API와 상호 작용하고, 단일 대상 시스템에 데이터를 프로비전하도록 도와줍니다. Azure AD에서 지원하는 대부분의 프로비전 커넥터는 단일 원본 및 대상 시스템용이며(예: Azure AD에서 ServiceNow로), Azure AD 앱 갤러리에서 문제의 앱(예: ServiceNow)을 추가하여 설치할 수 있습니다. 

Azure AD의 프로비전 커넥터 인스턴스와 앱 인스턴스는 일대일 관계입니다.

| 원본 시스템 | 대상 시스템 |
| ---------- | ---------- | 
| Azure AD 테넌트 | SaaS 응용 프로그램 |


그러나 Workday 및 Active Directory를 사용할 때 여러 원본 및 대상 시스템을 고려해야 합니다.

| 원본 시스템 | 대상 시스템 | 메모 |
| ---------- | ---------- | ---------- |
| Workday | Active Directory 포리스트 | 각 포리스트는 고유한 대상 시스템으로 처리됨 |
| Workday | Azure AD 테넌트 | 클라우드 전용 사용자에 필요한 경우 |
| Active Directory 포리스트 | Azure AD 테넌트 | 이 흐름은 현재 AAD Connect에서 처리 |
| Azure AD 테넌트 | Workday | 이메일 주소의 쓰기 저장 |

이러한 여러 워크플로를 여러 원본 및 대상 시스템에 사용할 수 있도록 Azure AD는 Azure AD 앱 갤러리에서 추가할 수 있는 여러 프로비전 커넥터 앱을 제공합니다.

![AAD 앱 갤러리](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Workday에서 Active Directory로 프로비전** - 이 앱은 Workday에서 단일 Active Directory 포리스트로 사용자 계정 프로비전을 가능하게 합니다. 포리스트가 여러 개 있는 경우 프로비전해야 하는 각 Active Directory 포리스트에 대해 Azure AD 앱 갤러리에서 이 앱의 하나의 인스턴스를 추가할 수 있습니다.

* **Workday에서 Azure AD로 프로비전** - AAD Connect는 Active Directory 사용자를 Azure Active Directory와 동기화하는 데 사용되는 도구이지만, 이 앱을 사용하여 Workday에서 단일 Azure Active Directory 테넌트로 클라우드 전용 사용자를 프로비전할 수 있습니다.

* **Workday 쓰기 저장** - 이 앱은 사용자 이메일 주소를 Azure Active Directory에서 Workday로 쓰기 저장할 수 있습니다.

> [!TIP]
> 일반 "Workday" 앱은 Workday와 Azure Active Directory 간의 Single Sign-On을 설정하는 데 사용됩니다. 

이러한 특수 프로비전 커넥터 앱을 설정하고 구성하는 방법은 본 자습서의 나머지 섹션에서 다루겠습니다. 구성을 위해 선택할 앱은 프로비전해야 하는 시스템, 환경에 있는 Active Directory 포리스트 및 Azure AD 테넌트 수에 따라 결정됩니다.

![개요](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Workday에서 시스템 통합 사용자 구성
모든 Workday 프로비전 커넥터의 일반적인 요구 사항으로, Workday 시스템 통합 계정이 Workday 인적 자원 API에 연결하는 데 사용할 할 자격 증명이 필요합니다. 이 섹션에서는 Workday에서 시스템 통합자 계정을 만드는 방법을 설명합니다.

> [!NOTE]
> 이 절차를 건너뛰고 Workday 전역 Administrator 계정을 시스템 통합 계정으로 사용할 수도 있습니다. 이 방법이 데모에서는 제대로 작동할 수 있지만 프로덕션 배포에는 권장하지 않습니다.

### <a name="create-an-integration-system-user"></a>통합 시스템 사용자 만들기

**통합 시스템 사용자를 만들려면**

1. Administrator 계정을 사용하여 Workday 테넌트에 로그인합니다. **Workday Workbench**의 검색 상자에서 사용자 만들기를 입력하고 **통합 시스템 사용자 만들기**를 클릭합니다. 
   
    ![사용자 만들기](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "사용자 만들기")
2. 새 통합 시스템 사용자에 대한 사용자 이름과 암호를 입력하여 **통합 시스템 사용자 만들기** 작업을 완료합니다.  
 * 이 사용자는 프로그래밍 방식으로 로그인할 것이므로 **다음 로그인할 때 새 암호 필요** 옵션을 선택하지 않습니다. 
 * 사용자의 세션이 너무 빨리 시간 초과되지 않도록 **세션 시간 초과 분**을 기본값인 0으로 둡니다. 
   
    ![통합 시스템 사용자 만들기](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "통합 시스템 사용자 만들기")

### <a name="create-a-security-group"></a>보안 그룹 만들기
무제한 통합 시스템 보안 그룹을 만들고 사용자를 할당해야 합니다.

**보안 그룹을 만들려면**

1. 검색 상자에 보안 그룹 만들기를 입력하고 **보안 그룹 만들기**를 클릭합니다. 
   
    ![보안 그룹 만들기](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "보안 그룹 만들기")
2. **보안 그룹 만들기** 작업을 완료합니다.  
3. **테넌트 보안 그룹의 형식**에서 **통합 시스템 보안 그룹(제한되지 않음)** 을 선택합니다.
4. 구성원이 명시적으로 추가될 보안 그룹을 만듭니다. 
   
    ![보안 그룹 만들기](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "보안 그룹 만들기")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>보안 그룹에 통합 시스템 사용자 할당

**통합 시스템 사용자를 할당하려면**

1. 검색 상자에 보안 그룹 편집을 입력하고 **보안 그룹 편집**를 클릭합니다. 
   
    ![보안 그룹 편집](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "보안 그룹 편집")
2. 이름으로 새 통합 보안 그룹을 검색하고 선택합니다. 
   
    ![보안 그룹 편집](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "보안 그룹 편집")
3. 새 보안 그룹에 새 통합 시스템 사용자 할당 
   
    ![시스템 보안 그룹](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "시스템 보안 그룹")  

### <a name="configure-security-group-options"></a>보안 그룹 옵션 구성
이 단계에서는 보안 그룹에 대한 작업자 데이터에 도메인 보안 정책 권한을 부여합니다.

**보안 그룹 옵션을 구성하려면**

1. 검색 상자에 **도메인 보안 정책**을 입력한 다음, **기능 영역에 대한 보안 정책** 링크를 클릭합니다.  
   
    ![도메인 보안 정책](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "도메인 보안 정책")  
2. 시스템을 검색하여 **시스템** 기능 영역을 선택합니다.  **확인**을 클릭합니다.  
   
    ![도메인 보안 정책](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "도메인 보안 정책")  
3. 시스템 기능 영역에 대한 보안 정책 목록에서 **보안 관리**를 확장하고, 도메인 보안 정책 **외부 계정 프로비저닝**을 선택합니다.  
   
    ![도메인 보안 정책](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "도메인 보안 정책")  
4. **사용 권한 편집**을 클릭한 다음, **사용 권한 편집** 대화 상자 페이지에서 **Get** 및 **Put** 통합 사용 권한이 있는 보안 그룹의 목록에 새 보안 그룹을 추가합니다. 
   
    ![사용 권한 편집](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "사용 권한 편집")  
    
5. 이러한 나머지 보안 정책 각각에 대해 위의 1-4단계를 반복합니다.

| 작업 | 도메인 보안 정책 |
| ---------- | ---------- | 
| 가져오기 및 넣기 | 작업자 데이터: 공용 작업자 보고서 |
| 가져오기 및 넣기 | 작업자 데이터: 작업 연락처 정보 |
| 가져오기 | 작업자 데이터: 모든 위치 |
| 가져오기 | 작업자 데이터: 현재 인력 관리 정보 |
| 가져오기 | 작업자 데이터: 작업자 프로필 직함 |
   
    
### <a name="activate-security-policy-changes"></a>보안 정책 변경 사항 활성화

**보안 정책 변경 사항을 활성화하려면**

1. 검색 상자에 활성화를 입력하고 **보류 중인 보안 정책 변경 내용 활성화** 링크를 클릭합니다. 
   
    ![활성화](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "활성화") 
2. 감사 목적에 대한 메모를 입력하고 **확인**을 클릭하여 보류 중인 보안 정책 변경 내용의 활성화 태스크를 시작합니다. 
   
    ![보류 중인 보안 활성화](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "보류 중인 보안 활성화")   
3. 다음 화면에서 **확인** 확인란을 선택하여 태스크를 완료한 다음 **확인**을 클릭합니다. 
   
    ![보류 중인 보안 활성화](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "보류 중인 보안 활성화")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday에서 Active Directory로 사용자 프로비전 구성
다음 지침에 따라 Workday에서 프로비전이 필요한 각 Active Directory 포리스트로 사용자 계정 프로비전을 구성합니다.

### <a name="planning"></a>계획

Active Directory 포리스트로 사용자 프로비전을 구성하기 전에 다음과 같은 질문을 고려하세요. 이러한 질문에 대한 답은 범위 지정 필터 및 특성 매핑을 설정하는 방법을 결정합니다. 

* **이 Active Directory 포리스트로 프로비전되어야 하는 Workday의 사용자**

   * *예제: Workday "회사" 특성에 "Contoso" 값이 포함되고 "Worker_Type" 특성에 "기본"이 포함되는 사용자입니다.*

* **다른 OU(조직 단위)에 사용자를 라우팅하는 방법**

   * *예제: Workday "Municipality" 및 "Country_Region_Reference" 특성에 정의된 대로 사무실 위치에 해당하는 OU로 사용자를 라우팅합니다.*

* **Active Directory에서 다음과 같은 특성을 채우는 방법**

   * 일반 이름(cn)
      * *예제: 인적 자원에서 설정한 대로 Workday User_ID 값을 사용합니다.*
      
   * 직원 ID(employeeId)
      * *예제: Workday Worker_ID 값을 사용합니다.*
      
   * SAM 계정 이름(sAMAccountName)
      * *예제: Workday User_ID 값을 사용하여 잘못된 문자를 제거하도록 Azure AD 프로비전 식을 통해 필터링됩니다.*
      
   * 사용자 계정 이름(userPrincipalName)
      * *예제: 도메인 이름을 추가하도록 Azure AD 프로비전 식에서 Workday User_ID 값을 사용합니다.*

* **Workday와 Active Directory 간에 사용자를 일치시키는 방법**

  * *예제: 특정 workday "Worker_ID" 값과 일치하는 사용자는 "employeeID"에 같은 값을 가지는 Active Directory 사용자와 일치합니다. Worker_ID 값이 Active Directory에 없는 경우 새로운 사용자를 만듭니다.*
  
* **일치하는 논리가 작동하기 위해 필요한 사용자 ID가 Active Directory 포리스트에 포함되었는지 여부**

  * *예제: 새 Workday 배포인 경우 Active Directory를 올바른 Workday Worker_ID 값(또는 선택한 고유 ID 값)으로 미리 채워서 일치하는 논리를 최대한 단순하게 유지하는 것이 좋습니다.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1부: 프로비전 커넥터 앱을 추가하고 Workday에 대한 연결 만들기

**Workday에서 Active Directory로의 프로비전을 구성하려면:**

1.  <https://portal.azure.com>으로 이동합니다.

2.  왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3.  **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램**을 차례로 선택합니다.

4.  **응용 프로그램 추가**를 선택하고 **모두** 범주를 선택합니다.

5.  **Active Directory에 Workday 프로비전**을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6.  앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7.  **프로비전** **모드**를 **자동**으로 변경합니다.

8.  다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. **다음과 같은 형태여야 합니다. username@contoso4**

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 끝점에 대한 URL을 입력합니다. https://wd3-impl-services1.workday.com/ccx/service/contoso4와 같은 형태여야 하며, 여기서 contoso4를 올바른 테넌트 이름으로 바꾸고 wd3-impl을 올바른 환경 문자열로 바꾸면 됩니다.

   * **Active Directory 포리스트 -** Get-ADForest powershell 명령에서 반환하는 Active Directory 포리스트의 "이름"입니다. 일반적으로 *contoso.com* 형태의 문자열입니다.

   * **Active Directory 컨테이너 -** AD 포리스트의 모든 사용자를 포함하는 컨테이너 문자열을 입력합니다. 예: *OU=Standard Users,OU=Users,DC=contoso,DC=test*

   * **알림 이메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday 자격 증명이 유효한지 다시 확인합니다. 

![Azure portal](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성 

이 섹션에서는 사용자 데이터가 Workday에서 Active Directory로 흐르는 방식을 구성하겠습니다.

1.  **매핑** 아래의 프로비전 탭에서 **Workday 작업자를 온-프레미스와 동기화**를 클릭합니다.

2.  **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의하여 AD 프로비전 범위에 포함할 Workday 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Workday의 모든 사용자"입니다. 예제 필터:

   * 예: 작업자 ID가 1000000-2000000 사이인 사용자를 범위에 포함

      * 특성: WorkerID

      * 연산자: REGEX Match

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예: 정규직 직원만 포함하고 비정규직 직원은 포함하지 않음 

      * 특성: EmployeeID

      * 연산자: IS NOT NULL

3.  **대상 개체 작업** 필드에서 어떤 작업을 Active Directory에서 수행할 수 있도록 허용할 것인지 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트**가 가장 일반적입니다.

4.  **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

5. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

      * **매핑 유형**

         * **직접** – Workday 특성 값을 변경하지 않고 AD 특성에 씁니다.

         * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

         * **식** – 하나 이상의 Workday 특성에 따라 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **원본 특성** - Workday의 사용자 특성입니다. 원하는 특성이 없는 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)을 참조하세요.

      * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

      * **대상 특성** – Active의 사용자 특성입니다.

      * **이 특성을 사용하여 개체 일치** – Workday와 Active Directory 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 Workday의 작업자 ID 필드에서 설정하며, 대개 Active Directory의 직원 ID 특성 중 하나에 매핑됩니다.

      * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

      * **이 매핑 적용**
       
         * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

         * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

6. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

![Azure portal](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**아래는 몇 가지 일반적인 식을 사용한 Workday와 Active Directory 간의 특성 매핑을 보여주는 예입니다.**

-   parentDistinguishedName 특성에 매핑되는 식을 사용하여 하나 이상의 Workday 원본 특성에 따라 다른 OU에 사용자를 프로비전할 수 있습니다. 이 예제에서는 도시에 따라 사용자를 다른 OU에 배치합니다.

-   도메인 접미사가 있는 Workday 사용자 ID를 연결하여 Active Directory의 userPrincipalName 특성을 생성합니다.

-   [식 작성에 대한 설명서는 여기에 있습니다](active-directory-saas-writing-expressions-for-attribute-mappings.md). 여기에는 특수 문자를 제거하는 방법에 대한 예제가 포함되어 있습니다.

  
| WORKDAY 특성 | ACTIVE DIRECTORY 특성 |  ID 일치 여부 | 만들기/업데이트 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **예** | 만들기 작업 시에만 기록 | 
| **UserID**    |  cn    |   |   만들기 작업 시에만 기록 |
| **Join("@",[UserID], "contoso.com")**   | userPrincipalName     |     | 만들기 작업 시에만 기록 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         만들기 작업 시에만 기록 |
| **Switch(\[Active\], , "0", "True", "1",)** |  accountDisabled      |     | 만들기 + 업데이트 |
| **FirstName**   | givenName       |     |    만들기 + 업데이트 |
| **LastName**   |   sn   |     |  만들기 + 업데이트 |
| **PreferredNameData**  |  displayName |     |   만들기 + 업데이트 |
| **Company**         | company   |     |  만들기 + 업데이트 |
| **SupervisoryOrganization**  | department  |     |  만들기 + 업데이트 |
| **ManagerReference**   | manager  |     |  만들기 + 업데이트 |
| **BusinessTitle**   |  title     |     |  만들기 + 업데이트 | 
| **AddressLineData**    |  streetAddress  |     |   만들기 + 업데이트 |
| **Municipality**   |   l   |     | 만들기 + 업데이트 |
| **CountryReferenceTwoLetter**      |   co |     |   만들기 + 업데이트 |
| **CountryReferenceTwoLetter**    |  C  |     |         만들기 + 업데이트 |
| **CountryRegionReference** |  st     |     | 만들기 + 업데이트 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  만들기 + 업데이트 |
| **PostalCode**  |   postalCode  |     | 만들기 + 업데이트 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 만들기 + 업데이트 |
| **Fax**      | facsimileTelephoneNumber     |     |    만들기 + 업데이트 |
| **Mobile**  |    mobile       |     |       만들기 + 업데이트 |
| **LocalReference** |  preferredLanguage  |     |  만들기 + 업데이트 |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  만들기 + 업데이트 |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>3부: 온-프레미스 동기화 에이전트 구성

Active Directory 온-프레미스로 프로비전하려면 원하는 Active Directory 포리스트의 도메인에 가입된 서버에 에이전트를 설치해야 합니다. 이 절차를 완료하려면 도메인 관리자(또는 엔터프라이즈 관리자) 자격 증명이 필요합니다.

**[온-프레미스 동기화 에이전트는 여기서 다운로드 가능](https://go.microsoft.com/fwlink/?linkid=847801)**

에이전트를 설치한 후에는 아래의 Powershell 명령을 실행하여 환경에 맞게 에이전트를 구성합니다.

**명령 #1**

> cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent" Agent\\Modules\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**명령 #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* 입력: "디렉터리 이름"으로 파트 \#2에서 입력한 AD 포리스트 이름 입력
* 입력: Active Directory 포리스트의 관리 사용자 이름 및 암호 입력

**명령 #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* 입력: Azure AD 테넌트의 전역 관리 사용자 이름 및 암호

>[!IMPORTANT]
>현재 사용자 지정 도메인을 사용하는 경우 작동하지 않는 전역 관리자 자격 증명으로 알려진 문제가 있습니다(예: admin@contoso.com). 해결 방법으로 onmicrosoft.com 도메인으로 전역 관리자 계정을 만들고 사용합니다(예: admin@contoso.onmicrosoft.com).

>[!IMPORTANT]
>현재 다단계 인증을 사용하도록 설정하는 경우 작동하지 않는 전역 관리자 자격 증명으로 알려진 문제가 있습니다. 해결 방법으로 전역 관리자에 대한 다단계 인증을 사용하지 않도록 설정합니다.


**명령 #4**

> Get-AdSyncAgentProvisioningTasks

* 작업: 데이터가 반환되었는지 확인합니다. 이 명령은 Azure AD 테넌트에서 Workday 프로비전 앱을 자동으로 검색합니다. 예제 출력:

> Name          : My AD Forest
>
> Enabled       : True
>
> DirectoryName : mydomain.contoso.com
>
> Credentialed  : False
>
> Identifier    : WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**명령 #5**

> Start-AdSyncAgentSynchronization -Automatic

**명령 #6**

> net stop aadsyncagent

**명령 #7**

> net start aadsyncagent

>[!TIP]
>Powershell에서 "net" 명령 외에도 **Services.msc**를 사용하여 동기화 에이전트 서비스를 시작 및 중지할 수 있습니다. Powershell 명령을 실행할 때 오류가 발생하는 경우 **Microsoft Azure AD Connect Provisioning Agent**가 **Services.msc**에서 실행되고 있는지 확인합니다.

![Services](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**유럽 연합에서 고객에 대한 추가 구성**

Azure Active Directory 테넌트가 EU 데이터 센터 중 하나에 있으면 아래 추가 단계를 수행합니다.

1. **Services.msc**를 열고 **Microsoft Azure AD Connect Provisioning Agent** 서비스를 중지합니다.
2. 에이전트 설치 폴더(예: C:\Program Files\Microsoft Azure AD Connect Provisioning Agent)로 이동합니다.
3. 텍스트 편집기에서 **SyncAgnt.exe.config**를 엽니다.
4. https://manage.hub.syncfabric.windowsazure.com/Management을 **https://eu.manage.hub.syncfabric.windowsazure.com/Management**으로 바꿉니다.
5. https://provision.hub.syncfabric.windowsazure.com/Provisioning을 **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**으로 바꿉니다.
6. **SyncAgnt.exe.config** 파일을 저장합니다.
7. **Services.msc**를 열고 **Microsoft Azure AD Connect Provisioning Agent** 서비스를 시작합니다.

**에이전트 문제 해결**

에이전트를 호스팅하는 Windows Server 컴퓨터의 [Windows 이벤트 로그](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)는 에이전트에서 수행하는 모든 작업에 대한 이벤트를 포함합니다. 이러한 이벤트를 보려면:
    
1. **Eventvwr.msc**를 엽니다.
2. **Windows 로그 > 응용 프로그램**을 선택합니다.
3. 원본 **AADSyncAgent** 아래에서 기록된 모든 이벤트를 봅니다. 
4. 오류 및 경고를 확인합니다.

Powershell 명령에서 제공하는 Active Directory 또는 Azure Active Directory 자격 증명 중 하나에 사용 권한 문제가 있는 경우 다음과 같은 오류가 표시됩니다. 
    
![이벤트 로그](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>4부: 서비스 시작
1-3부를 완료했으면 Azure Portal에서 프로비전 서비스를 다시 시작할 수 있습니다.

1.  **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 그러면 초기 동기화가 시작되며, Workday에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다.

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그는 Workday에서 어떤 사용자를 읽은 후 Active Directory에 추가 또는 업데이트되는지와 같은 프로비전 서비스에서 수행한 모든 개별 동기화 이벤트를 나열합니다. **[감사 로그를 읽는 방법에 대한 자세한 지침은 프로비전 보고 가이드 참조](active-directory-saas-provisioning-reporting.md)**

5.  새로운 오류나 경고에 대해 에이전트를 호스팅하는 Windows Server 컴퓨터에서 [Windows 이벤트 로그](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)를 확인합니다. 이러한 이벤트는 서버에서 **Eventvwr.msc**를 시작하고 **Windows 로그 > 응용 프로그램**을 선택하여 볼 수 있습니다. 모든 프로비전 관련 메시지는 원본 **AADSyncAgent** 아래에서 로깅됩니다. 
    

6. 작업이 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

![Azure portal](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Azure Active Directory로 사용자 프로비전 구성
Azure Active Directory로 프로비전을 구성하는 방법은 프로비전 요구 사항에 따라 달라지며, 아래 표에 자세히 설명되어 있습니다.

| 시나리오 | 해결 방법 |
| -------- | -------- |
| **사용자를 Active Directory 및 Azure AD에 프로비전 해야 함** | **[AAD Connect](connect/active-directory-aadconnect.md)** 사용 |
| **사용자를 Active Directory에만 프로비전 해야 함** | **[AAD Connect](connect/active-directory-aadconnect.md)** 사용 |
| **사용자를 Azure AD에만(클라우드에만) 프로비전 해야 함** | 앱 갤러리의 **Workday에서 Azure Active Directory로 프로비전** 앱 사용 |

Azure AD Connect 설정에 대한 자세한 지침은 [Azure AD Connect 설명서](connect/active-directory-aadconnect.md)를 참조하세요.

다음 섹션에서는 클라우드 전용 사용자를 프로비전하기 위해 Workday와 Azure AD 간 연결을 설정합니다.

> [!IMPORTANT]
> Azure AD에만 프로비전해야 하고 온-프레미스 Active Directory에는 프로비전하면 안 되는 클라우드 전용 사용자가 있는 경우 아래 절차만 수행합니다.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1부: Azure AD 프로비전 커넥터 앱을 추가하고 Workday에 대한 연결 만들기

**클라우드 전용 사용자에 대한 Workday-Azure Active Directory 프로비전을 구성하려면:**

1.  <https://portal.azure.com>로 이동합니다.

2.  왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3.  **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램**을 차례로 선택합니다.

4.  **응용 프로그램 추가**를 선택한 후 **모두** 범주를 선택합니다.

5.  **Workday-Azure AD 프로비전**을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6.  앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7.  **프로비전** **모드**를 **자동**으로 변경합니다.

8.  다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. 다음과 같은 형태여야 합니다. username@contoso4

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 끝점에 대한 URL을 입력합니다. https://wd3-impl-services1.workday.com/ccx/service/contoso4와 같은 형태여야 하며, 여기서 contoso4를 올바른 테넌트 이름으로 바꾸고 wd3-impl을 올바른 환경 문자열로 바꾸면 됩니다. 이 URL을 알 수 없는 경우 Workday 통합 파트너와 협력하거나 지원 담당자에게 문의하여 사용할 올바른 URL을 확인하세요.

   * **알림 이메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다.

   * 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday URL 및 자격 증명이 유효한지 다시 확인합니다.


### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성 

이 섹션에서는 클라우드 전용 사용자의 사용자 데이터가 Workday에서 Azure Active Directory로 흐르는 방식을 구성하겠습니다.

1.  **매핑** 아래의 프로비전 탭에서 **Workday 작업자를 Azure AD와 동기화**를 클릭합니다.

2.   **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의하여 Azure AD 프로비전 범위에 포함할 Workday 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Workday의 모든 사용자"입니다. 예제 필터:

   * 예: 작업자 ID가 1000000-2000000 사이인 사용자를 범위에 포함

      * 특성: WorkerID

      * 연산자: REGEX Match

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예: 비정규직 직원만 포함하고 정규직 직원은 포함하지 않음

      * 특성: ContingentID

      * 연산자: IS NOT NULL

3.  **대상 개체 작업** 필드에서 어떤 작업을 Azure AD에서 수행할 수 있도록 허용할 것인지 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트**가 가장 일반적입니다.

4.  **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

5. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

   * **매핑 유형**

      * **직접** – Workday 특성 값을 변경하지 않고 AD 특성에 씁니다.

      * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

      * **식** – 하나 이상의 Workday 특성에 따라 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **원본 특성** - Workday의 사용자 특성입니다. 원하는 특성이 없는 경우 [Workday 사용자 특성 목록 사용자 지정](#customizing-the-list-of-workday-user-attributes)을 참조하세요.

   * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

   * **대상 특성** – Azure AD의 사용자 특성입니다.

   * **이 특성을 사용하여 개체 일치** – Workday와 Azure AD 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 Workday의 작업자 ID 필드에서 설정하며, 대개 Azure AD의 직원 ID 특성(신규) 또는 확장 특성에 매핑됩니다.

   * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

   * **이 매핑 적용**

     * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

     * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

6. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

### <a name="part-3-start-the-service"></a>3부: 서비스 시작
1-2부를 완료했으면 프로비전 서비스를 시작할 수 있습니다.

1.  **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 그러면 초기 동기화가 시작되며, Workday에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다.

4. 개별 동기화 이벤트는 **감사 로그** 탭에서 볼 수 있습니다. **[감사 로그를 읽는 방법에 대한 자세한 지침은 프로비전 보고 가이드 참조](active-directory-saas-provisioning-reporting.md)**

5. 작업이 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Workday로 이메일 주소 쓰기 저장 구성
다음 지침에 따라 Azure Active Directory에서 Workday로 사용자 이메일 주소 쓰기 저장을 구성합니다.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1부: 프로비전 커넥터 앱을 추가하고 Workday에 대한 연결 만들기

**Workday에서 Active Directory로의 프로비전을 구성하려면:**

1.  <https://portal.azure.com>으로 이동합니다.

2.  왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3.  **엔터프라이즈 응용 프로그램**, **모든 응용 프로그램**을 차례로 선택합니다.

4.  **응용 프로그램 추가**를 선택한 후 **모두** 범주를 선택합니다.

5.  **Workday 쓰기 저장**을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6.  앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7.  **프로비전** **모드**를 **자동**으로 변경합니다.

8.  다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. 다음과 같은 형태여야 합니다. username@contoso4

   * **관리자 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **테넌트 URL –** 해당 테넌트의 Workday 웹 서비스 끝점에 대한 URL을 입력합니다. https://wd3-impl-services1.workday.com/ccx/service/contoso4와 같은 형태여야 하며, 여기서 contoso4를 올바른 테넌트 이름으로 바꾸고 wd3-impl을 올바른 환경 문자열로 바꾸면 됩니다(필요한 경우).

   * **알림 이메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday URL 및 자격 증명이 유효한지 다시 확인합니다.


### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성 


이 섹션에서는 사용자 데이터가 Workday에서 Active Directory로 흐르는 방식을 구성하겠습니다.

1.  **매핑** 아래의 프로비전 탭에서 **Azure AD 사용자를 Workday와 동기화**를 클릭합니다.

2.  선택 사항으로 **원본 개체 범위** 필드에서 Azure Active Directory의 사용자 집합 중 Workday에 이메일 주소를 기록할 사용자 집합을 필터링 할 수 있습니다. 기본 범위는 "Azure AD의 모든 사용자"입니다. 

3.  **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다. 기본적으로 이메일 주소에 대한 매핑이 있습니다. 그러나 Azure AD의 사용자가 Workday의 해당 항목과 일치하도록 일치 ID를 업데이트해야 합니다. Workday 작업자 ID 또는 직원 ID를 Azure AD의 extensionAttribute1-15와 동기화한 후 Azure AD에서 이 특성을 사용하여 Workday에서 사용자를 다시 일치시키는 방법이 주로 사용됩니다.

4.  매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

### <a name="part-3-start-the-service"></a>3부: 서비스 시작
1-2부를 완료했으면 프로비전 서비스를 시작할 수 있습니다.

1.  **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 그러면 초기 동기화가 시작되며, Workday에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다.

4. 개별 동기화 이벤트는 **감사 로그** 탭에서 볼 수 있습니다. **[감사 로그를 읽는 방법에 대한 자세한 지침은 프로비전 보고 가이드 참조](active-directory-saas-provisioning-reporting.md)**

5. 작업이 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.


## <a name="customizing-the-list-of-workday-user-attributes"></a>Workday 사용자 특성 목록 사용자 지정
Active Directory 및 Azure AD 둘 다에 대한 Workday 프로비저닝 앱에는 선택할 수 있는 Workday사용자 특성의 기본 목록이 포함되어 있습니다. 그러나 이러한 목록은 포괄적이지 않습니다. Workday는 Workday 테넌트에 고유할 수 있는 수백 개의 가능한 사용자 특성을 지원합니다. 

Azure AD 프로비저닝 서비스는 인사 API의[Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 작업에 노출되는 특성을 포함하도록 목록 또는 Workday 특성을 사용자 지정하는 기능을 지원합니다.

이를 수행하려면 [Workday Studio](https://community.workday.com/studio-download)를 사용하여 사용하려는 특성을 나타내는 XPath 식을 추출한 다음, Azure Portal에서 고급 특성 편집기를 사용하여 프로비저닝 구성에 추가해야 합니다.

**Workday 사용자 특성에 대한 XPath 식을 검색하려면**

1. [Workday Studio](https://community.workday.com/studio-download)를 다운로드하고 설치합니다. 설치 관리자에 액세스하려면 Workday 커뮤니티 계정이 필요합니다.

2. 이 URL에서 Workday Human_Resources WDSL 파일을 다운로드합니다. https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Workday Studio를 시작합니다.

4. 명령 모음에서 **Workday > 테스터에서 웹 서비스 테스트** 옵션을 선택합니다.

5. **외부**를 선택하고 2단계에서 다운로드한 Human_Resources WSDL 파일을 선택합니다.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio1.PNG)

6. "IMPL-CC"를 실제 인스턴스 유형으로, "TENANT"를 실제 테넌트 이름으로 바꾸어 **위치** 필드를 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`로 설정합니다.

7. **작업**을 **Get_Workers**로 설정합니다.

8.  요청/응답 창 아래의 작은 **구성** 링크를 클릭하여 Workday 자격 증명을 설정합니다. **인증**을 선택하고 Workday 통합 시스템 계정의 사용자 이름 및 암호를 입력합니다. 사용자 이름 형식을 name@tenant로 지정하고 **WS-Security UsernameToken** 옵션을 선택된 상태로 둡니다.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio2.PNG)

9. **확인**을 선택합니다.

10. **요청** 창에서 아래의 XML을 붙여 넣고 **Employee_ID**를 Workday 테넌트에 있는 실제 사용자의 직원 ID로 설정합니다. 추출하려는 특성으로 채워진 사용자를 선택합니다.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. **요청 보내기**(녹색 화살표)를 클릭하여 명령을 실행합니다. 성공한 경우, **응답** 창에 응답이 표시됩니다. 응답에서 입력한 사용자 ID의 데이터를 확인하여 오류가 없는지 검토합니다.

12. 성공한 경우 **응답** 창의 XML을 복사한 후 XML 파일로 저장합니다.

13. Workday Studio의 명령 모음에서 **파일 > 파일 열기...** 를 선택하고 방금 저장한 XML 파일을 엽니다. 그러면 Workday Studio XML 편집기에서 열립니다.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio3.PNG)

14. 파일 트리에서 **/env:Envelope > env:Body > wd:Get_Workers_Response > wd:Response_Data > wd:Worker**로 이동하여 사용자의 데이터를 찾습니다. 

15. **wd:Worker** 아래에서 추가하려는 특성을 찾은 후 선택합니다.

16. **문서 경로** 필드에서 선택한 특성에 대한 XPath 식을 복사합니다.

17. 복사한 식에서 **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** 접두사를 제거합니다. 

18. 복사한 식의 마지막 항목이 노드(예: "/wd:Birth_Date")인 경우 식 끝에 **/text()** 를 붙입니다. 마지막 항목이 특성(예: "/@wd:type")인 경우에는 이 작업이 필요하지 않습니다.

19. 결과는 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`와 같아야 합니다. 이 내용을 Azure Portal에 복사해야 합니다.


**프로비저닝 구성에 사용자 지정 Workday 사용자 특성을 추가하려면**

1. [Azure Portal](https://portal.azure.com)을 시작하고 이 자습서 앞부분에 설명된 것처럼 Workday 프로비저닝 응용 프로그램의 프로비저닝 섹션으로 이동합니다.

2. **프로비전 상태**를 **해제**로 설정하고 **저장**을 선택합니다. 이렇게 하면 준비가 되었을 때만 변경 내용을 적용할 수 있게 됩니다.

3. **매핑**에서 **온-프레미스에 관리자 동기화**(또는 **Azure AD에 작업자 동기화**)를 선택합니다.

4. 다음 화면 아래로 스크롤하여 **고급 옵션 표시**를 선택합니다.

5. **Workday에 대한 특성 목록 편집**을 선택합니다.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. 특성 목록에서 입력 필드가 있는 맨 아래로 스크롤합니다.

7. **이름**으로 해당 특성의 표시 이름을 입력합니다.

8. **형식**에 대해 특성에 해당하는 형식(**문자열**이 가장 일반적임)을 선택합니다.

9. **API 식**에 대해 Workday Studio에서 복사한 XPath 식을 입력합니다. 예제: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **특성 추가**를 선택합니다.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. 위의 **저장**을 선택하고 대화 상자에서 **예**를 선택합니다. 특성 매핑 화면이 계속 열려 있으면 닫습니다.

12. 주 **프로비전** 탭으로 돌아가 **온-프레미스에 작업자 동기화**(또는 **Azure AD에 작업자 동기화**)를 선택합니다.

13. **새 매핑 추가**를 선택합니다.

14. 이제 새 사용자 특성이 **원본 특성** 목록에 표시됩니다.

15. 필요에 따라 새 사용자 특성에 대한 매핑을 추가합니다.

16. 완료되면 **프로비전 상태**를 다시 **설정**으로 지정하고 저장합니다.


## <a name="known-issues"></a>알려진 문제

* **Add-ADSyncAgentAzureActiveDirectoryConfiguration** Powershell 명령을 실행할 때 현재 사용자 지정 도메인을 사용하는 경우 작동하지 않는 전역 관리자 자격 증명으로 알려진 문제가 있습니다(예: admin@contoso.com). 해결 방법으로 Azure AD에서 onmicrosoft.com 도메인으로 전역 관리자 계정을 만들고 사용합니다(예: admin@contoso.onmicrosoft.com).

* 유럽 연합에 있는 Azure AD 테넌트에 표시되지 않는 감사 로그와 관련된 이전 문제는 해결되었습니다. 그러나 EU의 Azure AD 테넌트에 추가 에이전트가 구성이 필요합니다. 자세한 내용은 [3부: 온-프레미스 동기화 에이전트 구성](#Part 3: Configure the on-premises synchronization agent)을 참조하세요.

## <a name="gdpr-information"></a>GDPR 정보

[GDPR(일반 데이터 보호 규정)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm)은 EU(유럽 연합)의 데이터 보호 및 프라이버시 법률입니다. GDPR은 회사, 정부 기관, 비영리 단체 및 기타 조직에 대해 상품 및 서비스를 제공하거나 EU 거주자와 연결된 데이터를 수집하고 분석하는 규칙을 수립합니다. 

Microsoft의 GDPR 스토리에 대한 자세한 내용은 [서비스 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)을 참조하세요.

Active Directory에 대한 Workday 프로비전 솔루션에는 도메인에 가입된 서버에 동기화 에이전트를 설치해야 하며 이 에이전트는 개인 식별 정보를 포함할 수 있는 로그를 **Windows 이벤트 로그**에 만듭니다.

Windows 이벤트 로그에 대한 데이터 보존을 구성하는 방법에 대한 내용은 [이벤트 로그에 대한 설정](https://technet.microsoft.com/library/cc952132.aspx)을 참조하세요. Windows 이벤트 로그에 대한 일반적인 정보는 [이 문서](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* [프로비전 활동에 대한 로그를 검토하고 보고서를 받아 보는 방법을 살펴봅니다](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
* [Workday와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.](active-directory-saas-workday-tutorial.md)
* [Azure Active Directory와 다른 SaaS 응용 프로그램을 통합하는 방법을 알아봅니다.](active-directory-saas-tutorial-list.md)
