---
title: "Azure Logic Apps용 온-프레미스 데이터 원본에 액세스 | Microsoft Docs"
description: "논리 앱에서 온-프레미스 데이터 원본에 액세스할 수 있도록 온-프레미스 데이터 게이트웨이를 설정합니다."
keywords: "데이터에 액세스, 온-프레미스, 데이터 전송, 암호화, 데이터 원본"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 5160913b2f08a04f0a985d8ddadd6641a664b258
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이를 사용하여 논리 앱에서 온-프레미스 데이터 원본에 연결

논리 앱에서 온-프레미스 데이터 원본에 액세스하려면 지원되는 커넥터와 함께 논리 앱을 사용할 수 있는 온-프레미스 데이터 게이트웨이를 설정합니다. 게이트웨이는 온-프레미스 데이터 원본과 논리 앱 간에 빠른 데이터 전송 및 암호화를 제공하는 브리지 역할을 합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널의 온-프레미스 원본에서 데이터를 릴레이합니다. 보안으로 시작하는 모든 트래픽은 게이트웨이 에이전트에서 트래픽을 아웃바운드합니다. [데이터 게이트웨이 작동 원리](logic-apps-gateway-install.md#gateway-cloud-service)에 대해 자세히 알아봅니다. 

게이트웨이는 다음과 같이 온-프레미스 데이터 원본에 대한 연결을 지원합니다.

*   BizTalk Server 2016
*   DB2  
*   파일 시스템
*   Informix
*   MQ
*   MySQL
*   Oracle 데이터베이스
*   PostgreSQL
*   SAP 응용 프로그램 서버 
*   SAP 메시지 서버
*   SharePoint
*   SQL Server
*   Teradata

이러한 단계에서는 논리 앱에서 작동하도록 온-프레미스 데이터 게이트웨이를 설정하는 방법을 보여줍니다. 지원되는 커넥터에 대한 자세한 내용은 [Azure Logic Apps용 커넥터](../connectors/apis-list.md)를 참조하세요. 

다른 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

*   [Microsoft Power BI 온-프레미스 데이터 게이트웨이](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow 온-프레미스 데이터 게이트웨이](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps 온-프레미스 데이터 게이트웨이](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>요구 사항

* [데이터 게이트웨이가 로컬 컴퓨터에 설치](logic-apps-gateway-install.md)되어 있어야 합니다.

* Azure Portal에 로그인할 때 [온-프레미스 데이터 게이트웨이를 설치](logic-apps-gateway-install.md#requirements)하는 데 사용된 것과 동일한 회사 또는 학교 계정을 사용해야 합니다. 또한 Azure Portal에서 게이트웨이 설치를 위해 게이트웨이 리소스를 만들 때 사용할 Azure 구독이 로그인 계정에 있어야 합니다.

* 게이트웨이 설치는 Azure 게이트웨이 리소스에서 이미 요구할 수 없습니다. 하나의 게이트웨이 리소스에만 게이트웨이 설치를 연결할 수 있습니다. 클레임은 게이트웨이 리소스를 만들 때 발생하므로 다른 리소스에서 설치할 수 없습니다.

* 온-프레미스 데이터 게이트웨이는 Windows 서비스로 실행되고 Windows 서비스 로그인 자격 증명에 `NT SERVICE\PBIEgwService`를 사용하도록 설정됩니다. Azure Portal에서 게이트웨이 리소스를 만들고 유지 관리하려면 [Windows 서비스 계정](../logic-apps/logic-apps-gateway-install.md)에 적어도 **참가자** 권한이 있어야 합니다. 

  > [!NOTE]
  > Windows 서비스 계정은 온-프레미스 데이터 원본에 연결하는 데 사용되는 동일한 계정 및 클라우드 서비스에 로그인하는 데 사용되는 Azure 회사 또는 학교 계정과 다릅니다.

## <a name="install-the-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이 설치

아직 수행하지 않은 경우 [온-프레미스 데이터 게이트웨이를 설치하는 단계](logic-apps-gateway-install.md)에 따르세요. 다른 단계를 계속하기 전에 로컬 컴퓨터에 데이터 게이트웨이를 설치했는지 확인합니다.

<a name="create-gateway-resource"></a>

## <a name="create-an-azure-resource-for-the-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이에 Azure 리소스 만들기

로컬 컴퓨터에 게이트웨이를 설치한 후에 Azure에서 리소스인 데이터 게이트웨이를 만들어야 합니다. 또한 이 단계는 Azure 구독과 게이트웨이 리소스를 연결합니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다. 게이트웨이를 설치하는 데 동일한 Azure 회사 또는 학교 이메일 주소를 사용해야 합니다.

2. 기본 Azure 메뉴에서 **리소스 만들기** > **엔터프라이즈 통합** > **온-프레미스 데이터 게이트웨이**를 선택합니다.

   !["온-프레미스 데이터 게이트웨이" 찾기](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. **연결 게이트웨이 만들기** 페이지에서 데이터 게이트웨이 리소스를 만들기 위해 이러한 세부 정보를 제공합니다.

    * **이름**: 게이트웨이 리소스의 이름을 입력합니다. 

    * **구독**: 게이트웨이 리소스와 연결할 Azure 구독을 선택합니다. 
    이 구독은 논리 앱과 동일한 구독이어야 합니다.
   
      기본 구독은 로그인하는 데 사용한 Azure 계정을 기반으로 합니다.

    * **리소스 그룹**: 게이트웨이 리소스를 배포하기 위해 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 
    리소스 그룹을 통해 관련된 Azure 자산을 컬렉션으로 관리할 수 있습니다.

    * **위치**: Azure에서는 이 위치를 [게이트웨이 설치](logic-apps-gateway-install.md) 중에 게이트웨이 클라우드 서비스에 대해 선택한 동일한 지역으로 제한합니다. 

      > [!NOTE]
      > 게이트웨이 리소스 위치가 게이트웨이 클라우드 서비스 위치와 일치하는지 확인합니다. 그렇지 않은 경우 게이트웨이 설치는 다음 단계에서 선택하도록 설치된 게이트웨이 목록에 나타나지 않을 수 있습니다.
      > 
      > 게이트웨이 리소스 및 논리 앱에 서로 다른 지역을 사용할 수 있습니다.

    * **설치 이름**: 게이트웨이 설치가 선택되어 있지 않으면 이전에 설치한 게이트웨이를 선택합니다. 

    게이트웨이 리소스를 Azure 대시보드에 추가하려면 **대시보드에 고정**을 선택합니다. 
    작업을 완료하면 **만들기**를 선택합니다.

    예: 

    ![온-프레미스 데이터 게이트웨이를 만들기 위해 세부 정보 제공](./media/logic-apps-gateway-connection/createblade.png)

    언제든지 데이터 게이트웨이를 찾거나 확인하려면 기본 Azure 메뉴에서 **모든 서비스**를 선택합니다. 
    검색 상자에 “온-프레미스 데이터 게이트웨이”를 입력한 다음, **온-프레미스 데이터 게이트웨이**를 선택합니다.

    ![“온-프레미스 데이터 게이트웨이” 찾기](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-your-logic-app-to-the-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이에 논리 앱 연결

이제 데이터 게이트웨이 리소스를 만들고 해당 리소스와 Azure 구독을 연결했으므로 논리 앱과 데이터 게이트웨이 간을 연결합니다.

> [!NOTE]
> 게이트웨이 연결 위치는 논리 앱과 동일한 지역에 있어야 하지만 다른 지역에 있는 데이터 게이트웨이를 사용할 수 있습니다.

1. Azure Portal의 논리 앱 디자이너에서 논리 앱을 만들거나 엽니다.

2. 온-프레미스 연결을 지원하는 SQL Server와 같은 커넥터를 추가합니다.

3. 표시된 순서에 따라 **온-프레미스 데이터 게이트웨이를 통한 연결**을 선택하고 고유한 연결 이름 및 필수 정보를 제공하고 사용하려는 데이터 게이트웨이 리소스를 선택합니다. 작업을 완료하면 **만들기**를 선택합니다.

   > [!TIP]
   > 고유한 연결 이름을 통해 나중에, 특히 여러 연결을 만들 때 해당 연결을 쉽게 식별할 수 있습니다. 해당하는 경우 사용자 이름의 정규화된 도메인도 포함됩니다. 

   ![논리 앱과 데이터 게이트웨이 간에 연결 만들기](./media/logic-apps-gateway-connection/blankconnection.png)

축하합니다. 게이트웨이 연결에 논리 앱을 사용할 준비가 되었습니다.

## <a name="edit-your-gateway-connection-settings"></a>게이트웨이 연결 설정 편집

논리 앱에 게이트웨이를 연결한 후에 나중에 해당 특정 연결에 대한 설정을 업데이트하는 것이 좋습니다.

1. 게이트웨이 연결을 찾으려면:

   * 논리 앱 메뉴의 **개발 도구** 아래에서 **API 연결**을 선택합니다. 
   
     **API 연결** 창에서는 게이트웨이 연결을 포함하여 논리 앱과 관련된 모든 API 연결을 보여줍니다.

     ![논리 앱으로 이동, "API 연결" 선택](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * 또는 기본 Azure 메뉴에서 Azure 구독과 연결된 게이트웨이 연결을 비롯한 모든 API 연결에 대해 **추가 서비스** > **웹 + 모바일** > **API 연결**로 이동합니다. 

   * 또는 기본 Azure 메뉴에서 Azure 구독과 연관된 게이트웨이 연결을 비롯한 모든 API 연결에 대해 **모든 리소스**로 이동합니다.

2. **API 연결 편집**을 보거나 편집 및 선택하려는 게이트웨이 연결을 선택합니다.

   > [!TIP]
   > 업데이트가 적용되지 않더라도 [게이트웨이 Windows 서비스를 중지 및 다시 시작](./logic-apps-gateway-install.md#restart-gateway)합니다.

<a name="change-delete-gateway-resource"></a>

## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>온-프레미스 데이터 게이트웨이 리소스 전환 또는 삭제

다른 게이트웨이 리소스를 만들거나 다른 리소스와 게이트웨이를 연결하거나 게이트웨이 리소스를 제거하려면 게이트웨이 설치에 영향을 주지 않고 게이트웨이 리소스를 삭제할 수 있습니다. 

1. 기본 Azure 메뉴에서 **모든 리소스**로 이동합니다. 
2. 데이터 게이트웨이 리소스를 찾아 선택합니다.
3. **온-프레미스 데이터 게이트웨이**를 선택하여 리소스 도구 모음에서 **삭제**를 선택합니다.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>질문과 대답

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>다음 단계

* [논리 앱 보안](./logic-apps-securing-a-logic-app.md)
* [논리 앱에 대한 일반적인 예제 및 시나리오](./logic-apps-examples-and-scenarios.md)
