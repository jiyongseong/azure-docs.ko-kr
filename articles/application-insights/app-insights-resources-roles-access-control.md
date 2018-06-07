---
title: Azure Application Insights에서 리소스, 역할 및 액세스 제어 | Microsoft Docs
description: 조직 Insights의 소유자, 참여자 및 읽기 권한자입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 5bb6c33c083e1be690b70b63ca087c923d43f3ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights에서 리소스, 역할 및 액세스 제어
[Microsoft Azure의 역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md)를 사용하여 Azure [Application Insights][start]의 데이터에 대한 읽기 및 업데이트 액세스를 제어할 수 있습니다.

> [!IMPORTANT]
> 리소스 자체가 아닌 응용 프로그램 리소스가 속한 **리소스 그룹 또는 구독** 의 사용자에게 액세스 권한을 할당합니다. **Application Insights 구성 요소 참여자** 역할을 할당합니다. 이렇게 하면 응용 프로그램 리소스와 함께 웹 테스트 및 경고에 대한 액세스를 통합적으로 제어할 수 있습니다. [자세히 알아봅니다](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>리소스, 그룹 및 구독
먼저 몇 가지를 정의하겠습니다.

* **리소스** - Microsoft Azure 서비스의 인스턴스입니다. Application Insights는 응용 프로그램에서 보낸 원격 분석 데이터를 수집, 분석 및 표시합니다.  다른 유형의 Azure 리소스로는 웹 앱, 데이터베이스 및 VM이 있습니다.
  
    리소스를 보려면 [Azure Portal][portal]을 열고 로그인한 다음 모든 리소스를 클릭합니다. 리소스를 찾으려면 필터 필드에 리소스 이름의 일부를 입력합니다.
  
    ![Azure 리소스 목록](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**리소스 그룹**][group] - 모든 리소스가 하나의 그룹에 속해 있습니다. 그룹은 특히 액세스 제어에 대한 관련 리소스를 간편하게 관리할 수 있는 방법입니다. 예를 들어 한 리소스 그룹에 Web App, 앱을 모니터링할 Application Insights 리소스, 내보낸 데이터를 보관할 저장소 리소스를 모두 집어넣을 수 있습니다.

    ![찾아보기, 리소스 그룹을 차례로 선택한 다음 그룹을 선택합니다.](./media/app-insights-resources-roles-access-control/11-group.png)

* [**구독**](https://portal.azure.com) - Application Insights 또는 다른 Azure 리소스를 사용하려면 Azure 구독에 로그인합니다. 모든 리소스 그룹은 Azure 구독에 속합니다. 여기에서 패키지를 선택하고, 조직 구독인 경우 멤버와 해당 액세스 권한을 선택합니다.
* [**Microsoft 계정**][account] - Microsoft Azure 구독, XBox Live, Outlook.com 및 기타 Microsoft 서비스에 로그인하는 데 사용하는 사용자 이름 및 암호입니다.

## <a name="access"></a> 리소스 그룹의 액세스 제어
응용 프로그램에 대해 만든 리소스 외에도 경고 및 웹 테스트에 대한 별도의 리소스가 숨겨져 있다는 사실을 이해해야 합니다. 이러한 리소스는 응용 프로그램과 동일한 [리소스 그룹](#resource-group) 에 연결됩니다. 웹 사이트 또는 저장소 같은 다른 Azure 서비스를 여기에 넣었을 수도 있습니다.

![Application Insights의 리소스](./media/app-insights-resources-roles-access-control/00-resources.png)

이러한 리소스에 대한 액세스를 제어하기 위한 권장 사항이 있습니다.

* **리소스 그룹 또는 구독** 수준에서 액세스를 제어합니다.
* 사용자에게 **Application Insights 구성 요소 참여자** 역할을 할당합니다. 그러면 그룹의 다른 서비스에 대한 액세스 권한을 제공하지 않아도 사용자가 웹 테스트, 경고 및 Application Insights 리소스를 편집할 수 있습니다.

## <a name="to-provide-access-to-another-user"></a>다른 사용자에 대한 액세스 권한 제공
구독 또는 리소스 그룹에 대한 소유자 권한이 있어야 합니다.

사용자에게 [Microsoft 계정][account]이 있거나 해당 [조직의 Microsoft 계정](../active-directory/sign-up-organization.md)에 액세스해야 합니다. 개별 사용자에 대한 액세스 권한을 제공할 수 있으며 Azure Active Directory에 정의된 사용자 그룹에 대한 액세스 권한도 제공할 수 있습니다.

#### <a name="navigate-to-the-resource-group"></a>리소스 그룹으로 이동
그곳에서 사용자를 추가합니다.

![응용 프로그램의 리소스 블레이드에서 필수 항목을 열고, 리소스 그룹을 열고, 설정/사용자를 선택합니다. 추가를 클릭합니다.](./media/app-insights-resources-roles-access-control/01-add-user.png)

또는 한 수준 더 올라가서 사용자를 구독에 추가할 수 있습니다.

#### <a name="select-a-role"></a>역할 선택
![새 사용자에 대한 역할을 선택합니다.](./media/app-insights-resources-roles-access-control/03-role.png)

| 역할 | 리소스 그룹에서 할 수 있는 일 |
| --- | --- |
| 소유자 |사용자 액세스를 포함하여 모든 것을 변경할 수 있음 |
| 참가자 |모든 리소스를 포함하여 모든 것을 편집할 수 있음 |
| Application Insights 구성 요소 참여자 |Application Insights 리소스, 웹 테스트 및 경고를 편집할 수 있음 |
| 판독기 |모든 것을 볼 수 있지만 변경할 수는 없음 |

'편집'에는 다음 항목을 만들고, 삭제하고, 업데이트하는 작업이 포함됩니다.

* 리소스
* 웹 테스트
* Alerts
* 연속 내보내기

#### <a name="select-the-user"></a>사용자 선택

원하는 사용자가 디렉터리에 없는 경우 Microsoft 계정이 있는 사람을 초대할 수 있습니다.
Outlook.com, OneDrive, Windows Phone 또는 XBox Live를 사용하는 사람은 Microsoft 계정을 갖고 있습니다.

## <a name="related-content"></a>관련 콘텐츠

* [Azure의 역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
