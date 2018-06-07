---
title: Azure Log Analytics의 IT Service Management Connector와의 연결 지원 | Microsoft Docs
description: 이 문서에서는 ITSM 제품/서비스를 OMS Log Analytics의 ITSMC(IT 서비스 관리 커넥터)와 연결하여 ITSM 작업 항목을 중앙에서 모니터링하고 관리하는 방법에 대한 정보를 제공합니다.
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: fdf3c7d1a6621437c414e2c1fe0628f2f585d98a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM 제품/서비스를 IT Service Management Connector에 연결
이 문서에서는 ITSM 제품/서비스와 Log Analytics의 ITSMC(IT 서비스 관리 커넥터) 사이 연결을 구성하여 사용자의 작업 항목을 중앙에서 관리하는 방법에 대한 정보를 제공합니다. ITSMC에 대한 자세한 내용은 [개요](log-analytics-itsmc-overview.md)를 참조하세요.

다음 ITSM 제품/서비스는 지원되지 않습니다. 제품을 ITSMC에 연결하는 방법에 대한 자세한 정보를 보려면 제품을 선택합니다.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> ITSM 커넥터는 클라우드 기반 ServiceNow 인스턴스에만 연결할 수 있습니다. 온-프레미스 ServiceNow 인스턴스는 현재 지원되지 않습니다.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>System Center Service Manager를 Azure의 IT 서비스 관리 커넥터에 연결

다음 섹션에서는 System Center Service Manager 제품을 Azure의 ITSMC에 연결하는 방법을 자세히 설명합니다.

### <a name="prerequisites"></a>필수 조건

다음 필수 조건이 충족되는지 확인합니다.

- ITSMC가 설치되어 있습니다. 자세한 내용은 [IT Service Management Connector 솔루션 추가](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)를 참조하세요.
- Service Manager 웹 응용 프로그램(웹앱)이 배포 및 구성되어 있습니다. 웹앱에 대한 정보는 [여기](#create-and-deploy-service-manager-web-app-service)를 참조하세요.
- 하이브리드 연결이 생성 및 구성되어 있습니다. 추가 정보: [하이브리드 연결 구성](#configure-the-hybrid-connection)
- 지원되는 Service Manager 버전: 2012 R2 또는 2016
- 사용자 역할: [고급 운영자](https://technet.microsoft.com/library/ff461054.aspx)

### <a name="connection-procedure"></a>연결 절차

System Center Service Manager 인스턴스를 ITSMC에 연결하려면 다음 절차를 사용합니다.

1. Azure Portal에서 **모든 리소스**로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2.  **작업 영역 데이터 원본**에서 **ITSM 연결**을 클릭합니다.

    ![새 연결](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. 왼쪽 창의 맨 위에 있는 **추가**를 클릭합니다.

4. 다음 표에 설명된 대로 정보를 제공하고 **확인**을 클릭하여 연결을 만듭니다.

> [!NOTE]

> 이러한 모든 매개 변수는 필수입니다.

| **필드** | **설명** |
| --- | --- |
| **연결 이름**   | ITSMC에 연결하려는 System Center Service Manager 인스턴스의 이름을 입력합니다.  이 이름은 나중에 이 인스턴스/보기의 자세한 로그 분석에서 작업 항목을 구성할 때 사용합니다. |
| **파트너 유형**   | **System Center Service Manager**를 선택합니다. |
| **서버 URL**   | Service Manager 웹앱의 URL을 입력합니다. Service Manager 웹앱에 대한 자세한 내용은 [여기](#create-and-deploy-service-manager-web-app-service)에 나와 있습니다.
| **클라이언트 ID**   | 웹앱을 인증하기 위해 생성한 클라이언트 ID를 입력합니다(자동 스크립트 사용). 자동화된 스크립트에 대한 자세한 내용은 [여기](log-analytics-itsmc-service-manager-script.md)에 나와 있습니다.|
| **클라이언트 암호**   | 이 ID에 대해 생성된 클라이언트 암호를 입력합니다.   |
| **데이터 동기화 범위**   | ITSMC를 통해 동기화할 Service Manager 작업 항목을 선택합니다.  이러한 작업 항목을 Log Analytics로 가져옵니다. **옵션:** 인시던트, 변경 요청|
| **데이터 동기화** | 데이터를 원하는 이전 일 수를 입력합니다. **최대 제한**: 120일 |
| **ITSM 솔루션에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 OMS는 지원되는 ITSM 시스템에서 영향을 받는 CI(존재하지 않는 CI)를 구성 항목으로 만듭니다. **기본**: 사용하지 않도록 설정됩니다. |

![서비스 관리자 연결](./media/log-analytics-itsmc/service-manager-connection.png)

**성공적으로 연결 및 동기화된 경우**:

- Service Manager에서 선택한 작업 항목을 Azure **Log Analytics**로 가져옵니다. IT Service Management Connector 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드에서, 또는 이 Service Manager 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.


추가 정보: [Log Analytics 경고에 대한 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Log Analytics 로그에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) 및 [Azure Alerts에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager 웹앱 서비스 만들기 및 배포

Azure에서 ITSMC에 온-프레미스 Service Manager를 연결하기 위해 Microsoft는 GitHub에 Service Manager 웹앱을 만들었습니다.

Service Manager에 대해 ITSM 웹앱을 설정하려면 다음을 수행합니다.

- **웹앱 배포** – 웹앱을 배포하고, 속성을 설정하고, Azure AD를 사용하여 인증합니다. Microsoft에서 제공한 [자동화 스크립트](log-analytics-itsmc-service-manager-script.md)를 사용하여 웹앱을 배포할 수 있습니다.
- **하이브리드 연결 구성** - 수동으로 [이 연결을 구성](#configure-the-hybrid-connection)합니다.

#### <a name="deploy-the-web-app"></a>웹앱 배포
자동화 [스크립트](log-analytics-itsmc-service-manager-script.md)를 사용하여 웹앱을 배포하고, 속성을 설정하고, Azure AD를 사용하여 인증합니다.

다음 필수 정보를 제공하여 스크립트를 실행합니다.

- Azure 구독 정보
- 리소스 그룹 이름
- 위치
- Service Manager 서버 세부 정보(서버 이름, 도메인, 사용자 이름 및 암호)
- 웹앱에 대한 사이트 이름 접두사
- ServiceBus 네임스페이스.

이 스크립트는 사용자가 지정한 이름(및 웹앱을 고유하게 만드는 몇 가지 추가 설정)을 사용하여 웹앱을 만듭니다. **웹앱 URL**, **클라이언트 ID** 및 **클라이언트 암호**를 생성합니다.

값을 저장한 다음, ITSMC와의 연결을 만들 때 사용합니다.

**웹앱 설치 확인**

1. **Azure Portal** > **리소스**로 이동합니다.
2. 웹앱을 선택하고 **설정** > **응용 프로그램 설정**을 클릭합니다.
3. 스크립트를 통해 앱을 배포할 때 제공한 Service Manager 인스턴스에 대한 정보를 확인합니다.

### <a name="configure-the-hybrid-connection"></a>하이브리드 연결 구성

다음 절차에 따라 Service Manager 인스턴스를 Azure의 ITSMC에 연결하는 하이브리드 연결을 구성합니다.

1. **Azure 리소스** 아래에서 Service Manager 웹앱을 찾습니다.
2. **설정** > **네트워킹**을 클릭합니다.
3. **하이브리드 연결**에서 **하이브리드 연결 끝점 구성**을 클릭합니다.

    ![하이브리드 연결 네트워킹](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. **하이브리드 연결** 블레이드에서 **하이브리드 연결 추가**를 클릭합니다.

    ![하이브리드 연결 추가](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. **하이브리드 연결 추가** 블레이드에서 **새 하이브리드 연결 만들기**를 클릭합니다.

    ![새 하이브리드 연결](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. 다음 값을 입력합니다.

    - **끝점 이름**: 새 하이브리드 연결의 이름을 지정합니다.
    -  **끝점 호스트**: Service Manager 관리 서버의 FQDN입니다.
    - **끝점 포트**: 5724를 입력합니다.
    - **Servicebus 네임스페이스**: 기존 Servicebus 네임스페이스를 사용하거나 새로 만듭니다.
    - **위치**: 위치를 선택합니다.
    -  **이름**: servicebus를 만드는 경우 이름을 지정합니다.

    ![하이브리드 연결 값](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. **확인**을 클릭하여 **하이브리드 연결 만들기** 블레이드를 닫고 하이브리드 연결을 만듭니다.

    하이브리드 연결이 만들어지면 블레이드 아래에 표시됩니다.

7. 하이브리드 연결을 만든 후 연결을 선택하고 **선택한 하이브리드 연결 추가**를 클릭합니다.

    ![새 하이브리드 연결](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>수신기 설정 구성

다음 절차에 따라 하이브리드 연결에 대한 수신기 설정을 구성합니다.

1. **하이브리드 연결** 블레이드에서 **연결 관리자 다운로드**를 클릭한 후 System Center Service Manager 인스턴스가 실행되고 있는 컴퓨터에 설치합니다.

    설치가 완료되면 **시작** 메뉴 아래에서 **하이브리드 연결 관리자 UI** 옵션을 사용할 수 있습니다.

2. **하이브리드 연결 관리자 UI**를 클릭하면 Azure 자격 증명을 지정하라는 메시지가 표시됩니다.

3. Azure 자격 증명으로 로그인하고 하이브리드 연결이 만들어진 구독을 선택합니다.

4. **저장**을 클릭합니다.

하이브리드 연결이 성공적으로 설정됩니다.

![하이브리드 연결 성공](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> 하이브리드 연결이 만들어진 후 배포된 Service Manager 웹앱을 방문하여 연결을 확인한 후 테스트합니다. Azure에서 ITSMC에 연결을 시도하기 전에 연결이 성공적으로 설정되었는지 확인합니다.

다음 샘플 이미지는 성공적으로 설정된 연결의 세부 정보를 보여줍니다.

![하이브리드 연결 테스트](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>ServiceNow를 Azure의 IT 서비스 관리 커넥터에 연결

다음 섹션에서는 ServiceNow 제품을 Azure의 ITSMC에 연결하는 방법을 자세히 설명합니다.

### <a name="prerequisites"></a>필수 조건
다음 필수 조건이 충족되는지 확인합니다.
- ITSMC가 설치되어 있습니다. 자세한 내용은 [IT Service Management Connector 솔루션 추가](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)를 참조하세요.
- ServiceNow 지원 버전: Kingston, Jakarta, Istanbul, Helsinki, Geneva

**ServiceNow 관리자는 ServiceNow 인스턴스에서 다음을 수행해야 합니다.**
- ServiceNow 제품에 대한 클라이언트 ID 및 클라이언트 암호를 생성합니다. 클라이언트 ID와 비밀을 생성하는 방법에 대한 자세한 내용은 필요에 따라 다음을 참조하세요.

    - [Kingston에 대해 OAuth 설정](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/concept/OAuth-setup.html)
    - [Jakarta에 대해 OAuth 설정](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Istanbul에 대해 OAuth 설정](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Helsinki에 대해 OAuth 설정](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Geneva에 대해 OAuth 설정](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Microsoft OMS 통합용 사용자 앱(ServiceNow 앱)을 설치합니다. [자세히 알아보기](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- 설치된 사용자 앱에 대한 통합 사용자 역할을 만듭니다. 통합 사용자 역할을 만드는 방법에 대한 자세한 내용은 [여기](#create-integration-user-role-in-servicenow-app)에 나와 있습니다.

### <a name="connection-procedure"></a>**연결 절차**
다음 절차에 따라 ServiceNow 연결을 만듭니다.


1. Azure Portal에서 **모든 리소스**로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2.  **작업 영역 데이터 원본**에서 **ITSM 연결**을 클릭합니다.
    ![새 연결](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. 왼쪽 창의 맨 위에 있는 **추가**를 클릭합니다.

4. 다음 표에 설명된 대로 정보를 제공하고 **확인**을 클릭하여 연결을 만듭니다.


> [!NOTE]
> 이러한 모든 매개 변수는 필수입니다.

| **필드** | **설명** |
| --- | --- |
| **연결 이름**   | ITSMC에 연결하려는 ServiceNow 인스턴스의 이름을 입력합니다.  이 이름은 나중에 이 ITSM/보기의 자세한 로그 분석에서 작업 항목을 구성할 때 OMS에서 사용합니다. |
| **파트너 유형**   | **ServiceNow**를 선택합니다. |
| **사용자 이름**   | ITSMC에 대한 연결을 지원하기 위해 ServiceNow 앱에서 만든 통합 사용자 이름을 입력합니다. 추가 정보: [ServiceNow 앱 사용자 역할 만들기](#create-integration-user-role-in-servicenow-app)|
| **암호**   | 이 사용자 이름과 관련된 암호를 입력합니다. **참고**: 사용자 이름 및 암호는 인증 토큰 생성에만 사용되며 ITSMC 서비스에는 저장되지 않습니다.  |
| **서버 URL**   | ITSMC에 연결하려는 ServiceNow 인스턴스의 URL을 입력합니다. |
| **클라이언트 ID**   | 이전에 생성한 OAuth2 인증에 사용하려는 클라이언트 ID를 입력합니다.  클라이언트 ID 및 암호 생성에 대한 추가 정보: [OAuth 설정](http://wiki.servicenow.com/index.php?title=OAuth_Setup) |
| **클라이언트 암호**   | 이 ID에 대해 생성된 클라이언트 암호를 입력합니다.   |
| **데이터 동기화 범위**   | ITSMC를 통해 Azure Log Analytics와 동기화할 ServiceNow 작업 항목을 선택합니다.  선택한 값을 Log Analytics로 가져옵니다.   **옵션:** 인시던트 및 변경 요청|
| **데이터 동기화** | 데이터를 원하는 이전 일 수를 입력합니다. **최대 제한**: 120일 |
| **ITSM 솔루션에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 ITSMC는 지원되는 ITSM 시스템에서 영향을 받는 CI를 구성 항목으로 만듭니다(존재하지 않는 CI의 경우). **기본**: 사용하지 않도록 설정됩니다. |

![ServiceNow 연결](./media/log-analytics-itsmc/itsm-connection-servicenow-connection-latest.png)

**성공적으로 연결 및 동기화된 경우**:

- ServiceNow 인스턴스에서 선택한 작업 항목을 Azure **Log Analytics**로 가져옵니다. IT Service Management Connector 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드에서, 또는 이 ServiceNow 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

추가 정보: [Log Analytics 경고에 대한 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Log Analytics 로그에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) 및 [Azure Alerts에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

### <a name="create-integration-user-role-in-servicenow-app"></a>ServiceNow 앱에서 통합 사용자 역할 만들기

다음 절차를 수행합니다.

1.  [ServiceNow 스토어](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)를 방문하고 **ServiceNow 및 Microsoft OMS 통합용 사용자 앱**을 ServiceNow 인스턴스에 설치합니다.
2.  설치 후 ServiceNow 인스턴스의 왼쪽 탐색 모음으로 가서 Microsoft OMS 통합기를 검색한 후 선택합니다.  
3.  **설치 검사 목록**을 클릭합니다.

    사용자 역할을 아직 생성해야 할 경우 상태가 **완료되지 않음**으로 표시됩니다.

4.  **통합 사용자 만들기** 옆의 텍스트 상자에 Azure의 ITSMC에 연결할 수 있는 사용자의 사용자 이름을 입력합니다.
5.  이 사용자에 대한 암호를 입력하고 **확인**을 클릭합니다.  

>[!NOTE]

> 이러한 자격 증명을 사용하여 Azure에서 ServiceNow 연결을 만듭니다.

새로 만든 사용자는 기본 역할이 할당된 상태로 표시됩니다.

**기본 역할**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

사용자가 성공적으로 만들어지면 **설치 검사 목록 확인**의 상태가 완료됨으로 바뀌고 해당 앱에 대해 만들어진 사용자의 역할 세부 정보가 표시됩니다.

> [!NOTE]

> ITSM 커넥터는 ServiceNow 인스턴스에 설치된 다른 모듈 없이 ServiceNow에 인시던트를 보낼 수 있습니다. ServiceNow 인스턴스에서 EventManagement 모듈을 사용하고 커넥터를 사용하여 ServiceNow에서 이벤트 또는 경고를 만들려는 경우 통합 사용자에게 다음 역할을 추가합니다.

>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Province를 Azure의 IT 서비스 관리 커넥터에 연결

다음 섹션에서는 Provance 제품을 Azure의 ITSMC에 연결하는 방법을 자세히 설명합니다.


### <a name="prerequisites"></a>필수 조건

다음 필수 조건이 충족되는지 확인합니다.


- ITSMC가 설치되어 있습니다. 자세한 내용은 [IT Service Management Connector 솔루션 추가](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)를 참조하세요.
- Provance 앱을 Azure AD에 등록해야 합니다. 그래야 클라이언트 ID를 사용할 수 있게 됩니다. 자세한 내용은 [Active Directory 인증을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요.

- 사용자 역할: 관리자

### <a name="connection-procedure"></a>연결 절차

다음 절차에 따라 Provance 연결을 만듭니다.

1. Azure Portal에서 **모든 리소스**로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2.  **작업 영역 데이터 원본**에서 **ITSM 연결**을 클릭합니다.
    ![새 연결](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. 왼쪽 창의 맨 위에 있는 **추가**를 클릭합니다.

4. 다음 표에 설명된 대로 정보를 제공하고 **확인**을 클릭하여 연결을 만듭니다.

> [!NOTE]

> 이러한 모든 매개 변수는 필수입니다.

| **필드** | **설명** |
| --- | --- |
| **연결 이름**   | ITSMC에 연결하려는 Provance 인스턴스의 이름을 입력합니다.  이 이름은 나중에 이 ITSM의 작업 항목을 구성하고 자세한 로그 분석을 확인할 때 사용합니다. |
| **파트너 유형**   | **Provance**를 선택합니다. |
| **사용자 이름**   | ITSMC에 연결할 수 있는 사용자 이름을 입력합니다.    |
| **암호**   | 이 사용자 이름과 관련된 암호를 입력합니다. **참고**: 사용자 이름 및 암호는 인증 토큰 생성에만 사용되며 ITSMC 서비스에는 저장되지 않습니다.|
| **서버 URL**   | ITSMC에 연결하려는 Provance 인스턴스의 URL을 입력합니다. |
| **클라이언트 ID**   | Provance 인스턴스에서 생성한 이 연결을 인증하기 위한 클라이언트 ID를 입력합니다.  클라이언트 ID에 대한 자세한 내용은 [Active Directory 인증을 구성하는 방법](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요. |
| **데이터 동기화 범위**   | ITSMC를 통해 Azure Log Analytics와 동기화할 Provance 작업 항목을 선택합니다.  이러한 작업 항목을 Log Analytics로 가져옵니다.   **옵션:** 인시던트, 변경 요청|
| **데이터 동기화** | 데이터를 원하는 이전 일 수를 입력합니다. **최대 제한**: 120일 |
| **ITSM 솔루션에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 ITSMC는 지원되는 ITSM 시스템에서 영향을 받는 CI를 구성 항목으로 만듭니다(존재하지 않는 CI의 경우). **기본**: 사용하지 않도록 설정됩니다.|

![Provance 연결](./media/log-analytics-itsmc/itsm-connections-provance-latest.png)

**성공적으로 연결 및 동기화된 경우**:

- 이 Provance 인스턴스에서 선택한 작업 항목을 Azure **Log Analytics**로 가져옵니다. IT Service Management Connector 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드에서, 또는 이 Provance 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

추가 정보: [Log Analytics 경고에 대한 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Log Analytics 로그에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) 및 [Azure Alerts에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Cherwell을 Azure의 IT 서비스 관리 커넥터에 연결

다음 섹션에서는 Cherwell 제품을 Azure의 ITSMC에 연결하는 방법을 자세히 설명합니다.

### <a name="prerequisites"></a>필수 조건

다음 필수 조건이 충족되는지 확인합니다.

- ITSMC가 설치되어 있습니다. 자세한 내용은 [IT Service Management Connector 솔루션 추가](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution)를 참조하세요.
- 클라이언트 ID가 생성되었습니다. 추가 정보: [Cherwell용 클라이언트 ID 생성](#generate-client-id-for-cherwell)
- 사용자 역할: 관리자

### <a name="connection-procedure"></a>연결 절차

다음 절차에 따라 Provance 연결을 만듭니다.

1. Azure Portal에서 **모든 리소스**로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2.  **작업 영역 데이터 원본**에서 **ITSM 연결**을 클릭합니다.
    ![새 연결](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. 왼쪽 창의 맨 위에 있는 **추가**를 클릭합니다.

4. 다음 표에 설명된 대로 정보를 제공하고 **확인**을 클릭하여 연결을 만듭니다.

> [!NOTE]

> 이러한 모든 매개 변수는 필수입니다.

| **필드** | **설명** |
| --- | --- |
| **연결 이름**   | ITSMC에 연결하려는 Cherwell 인스턴스의 이름을 입력합니다.  이 이름은 나중에 이 ITSM의 작업 항목을 구성하고 자세한 로그 분석을 확인할 때 사용합니다. |
| **파트너 유형**   | **Cherwell**을 선택합니다. |
| **사용자 이름**   | ITSMC에 연결할 수 있는 Cherwell 사용자 이름을 입력합니다. |
| **암호**   | 이 사용자 이름과 관련된 암호를 입력합니다. **참고**: 사용자 이름 및 암호는 인증 토큰 생성에만 사용되며 ITSMC 서비스에는 저장되지 않습니다.|
| **서버 URL**   | ITSMC에 연결하려는 Cherwell 인스턴스의 URL을 입력합니다. |
| **클라이언트 ID**   | Cherwell 인스턴스에서 생성한 이 연결을 인증하기 위한 클라이언트 ID를 입력합니다.   |
| **데이터 동기화 범위**   | ITSMC를 통해 동기화할 Cherwell 작업 항목을 선택합니다.  이러한 작업 항목을 Log Analytics로 가져옵니다.   **옵션:** 인시던트, 변경 요청 |
| **데이터 동기화** | 데이터를 원하는 이전 일 수를 입력합니다. **최대 제한**: 120일 |
| **ITSM 솔루션에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 ITSMC는 지원되는 ITSM 시스템에서 영향을 받는 CI를 구성 항목으로 만듭니다(존재하지 않는 CI의 경우). **기본**: 사용하지 않도록 설정됩니다. |


![Provance 연결](./media/log-analytics-itsmc/itsm-connections-cherwell-latest.png)

**성공적으로 연결 및 동기화된 경우**:

- 이 Cherwell 인스턴스에서 선택한 작업 항목을 Azure **Log Analytics**로 가져옵니다. IT Service Management Connector 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드에서, 또는 이 Cherwell 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

추가 정보: [Log Analytics 경고에 대한 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [Log Analytics 로그에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) 및 [Azure Alerts에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

### <a name="generate-client-id-for-cherwell"></a>Cherwell용 클라이언트 ID 생성

Cherwell용 클라이언트 ID/키를 생성하려면 다음 절차를 수행합니다.

1. Cherwell 인스턴스에 관리자로 로그인합니다.
2. **보안** > **REST API 클라이언트 설정 편집**을 클릭합니다.
3. **새 클라이언트 만들기** > **클라이언트 암호**를 선택합니다.

    ![Cherwell 사용자 ID](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>다음 단계
 - [Log Analytics 경고에 대한 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts)
 - [Log Analytics 로그 레코드 로그에 대한 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records)
 - [Azure 경고에서 ITSM 작업 항목 만들기](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
