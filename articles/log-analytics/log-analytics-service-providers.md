---
title: 서비스 공급자에 대한 Log Analytics 기능 | Microsoft Docs
description: Log Analytics는 MSP(Managed Service Providers), 대기업, ISV(Independent Software Vendor)를 지원하며 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리하고 모니터링할 수 있도록 합니다.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 6934e92df562099122eaede39fd26cf51cf1ee44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="log-analytics-features-for-service-providers"></a>서비스 공급자에 대한 Log Analytics 기능
Log Analytics는 MSP(Managed Service Providers), 대기업, ISV(Independent Software Vendor)를 지원하며 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리하고 모니터링할 수 있도록 합니다. 

대기업은 서비스 공급자와 많은 유사성을 공유하는데, 특히 중앙 IT 팀이 다양한 사업부의 IT 관리를 담당한다는 점이 그렇습니다. 간단한 설명을 위해 이 문서에서는 *서비스 공급자*라는 용어를 사용하지만 기업 및 기타 고객에 대해서도 같은 기능을 사용할 수 있습니다.

## <a name="cloud-solution-provider"></a>클라우드 솔루션 공급자
[CSP(클라우드 솔루션 공급자)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) 프로그램에 참여하는 파트너 및 서비스 공급자에게는 Log Analytics가 [Azure CSP 구독](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)에서 사용할 수 있는 Azure 서비스 중 하나입니다. 

Log Analytics의 경우 다음 기능을 *클라우드 솔루션 공급자* 구독에서 사용할 수 있습니다.

*CSP(클라우드 솔루션 공급자)*는 다음을 수행할 수 있습니다.

* 테넌트(고객) 구독에 Log Analytics 작업 영역을 만듭니다.
* 테넌트에서 만든 작업 영역에 액세스합니다. 
* Azure 사용자 관리를 사용하여 작업 영역에 대한 사용자 액세스를 추가 및 제거합니다. OMS 포털의 테넌트 작업 영역에서 설정의 사용자 관리 페이지를 사용할 수 없는 경우
  * Log Analytics에서는 아직 역할 기반 액세스를 지원하지 않으므로 Azure 포털에서 사용자에게 `reader` 권한을 부여하여 OMS 포털에서 구성을 변경할 수 있습니다.

테넌트의 구독에 로그인하려면 테넌트 식별자를 지정해야 합니다. 테넌트 식별자는 보통 로그인하는 데 사용한 전자 메일 주소의 마지막 부분입니다.

* OMS 포털에서 포털에 대한 URL에 `?tenant=contoso.com`을 추가합니다. 위치(예:`mms.microsoft.com/?tenant=contoso.com`
* PowerShell에서는 `Connect-AzureRmAccount` cmdlet을 사용할 때 `-Tenant contoso.com` 매개 변수를 사용합니다.
* Azure 포털에서 `OMS portal` 링크를 사용하여 선택한 작업 영역에 대한 OMS 포털을 열고 로그인하면 테넌트 식별자가 자동으로 추가됩니다.

CSP(클라우드 솔루션 공급자)의 *고객*은 다음을 수행할 수 있습니다.

* CSP 구독에서 Log Analytics 작업 영역 만들기
* CSP가 만든 작업 영역에 액세스
  * Azure 포털에서 `OMS portal` 링크를 사용하여 선택한 작업 영역에 대한 OMS 포털을 열고 로그인
* OMS 포털의 설정에서 사용자 관리 페이지 보기 및 사용

> [!NOTE]
> Log Analytics에 대해 포함된 Backup 및 Site Recovery 솔루션을 Recovery Services 자격 증명 모음에 연결할 수 없으며 CSP 구독에서 구성할 수 없습니다. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Log Analytics를 사용하여 여러 고객 관리
관리하는 고객마다 Log Analytics 작업 영역을 만드는 것이 좋습니다. Log Analytics 작업 영역이 제공하는 정보:

* 저장할 데이터에 대한 지리적 위치입니다. 
* 대금 청구에 대한 세분성 
* 데이터 격리 
* 고유한 구성

고객당 작업 영역을 만들면 각 고객의 데이터를 별도로 유지하고 각 고객의 사용 현황을 추적할 수도 있습니다.

여러 작업 영역을 만들어야 하는 시기와 이유에 대한 자세한 내용은 [Log Analytics에 대한 액세스 관리](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need)에 설명되어 있습니다.

고객 작업 영역의 생성 및 구성은 [PowerShell](log-analytics-powershell-workspace-configuration.md), [Resource Manager 템플릿](log-analytics-template-workspace-configuration.md), [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/)를 사용하여 자동화할 수 있습니다.

작업 영역 구성을 위해 Resource Manager 템플릿을 사용하면 작업 영역을 만들고 구성하는 데 사용할 수 있는 마스터 구성을 활용할 수 있습니다. 사용자 요구 사항에 따라 자동으로 구성된 작업 영역이 고객에 대해 생성되는 것을 확신할 수 있습니다. 요구 사항을 업데이트하면 템플릿이 업데이트된 후 기존 작업 영역에 다시 적용됩니다. 이 프로세스는 기존 작업 영역이 새로운 표준을 충족하는지도 확인합니다.    

여러 Log Analytics 작업 영역을 관리할 때는 [경고](log-analytics-alerts.md) 기능을 사용하여 각 작업 영역을 기존의 티켓 시스템/운영 콘솔과 통합하는 것이 좋습니다. 기존 시스템과 통합되므로 지원 담당자는 친숙한 프로세스를 계속 진행할 수 있습니다. Log Analytics는 각 작업 영역에서 사용자가 지정한 경고 기준을 정기적으로 확인하고 작업이 필요하면 경고를 생성합니다.

데이터에 대한 개인 설정된 보기는 Azure Portal의 [대시보드](../azure-portal/azure-portal-dashboards.md) 기능을 사용하세요.  

작업 영역 전반의 데이터를 요약하는 경영진 수준 보고서를 얻기 위해서는 Log Analytics와 [PowerBI](log-analytics-powerbi.md)의 통합을 사용하면 됩니다. 다른 보고 시스템과 통합이 필요한 경우 Search API(PowerShell 또는 [REST](log-analytics-log-search-api.md)를 통해)를 사용하여 쿼리를 실행하고 검색 결과를 내보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Resource Manager 템플릿](log-analytics-template-workspace-configuration.md)을 사용하여 작업 영역 생성 및 구성 자동화
* [PowerShell](log-analytics-powershell-workspace-configuration.md)을 사용하여 작업 영역 생성 자동화 
* [경고](log-analytics-alerts.md)를 사용하여 기존 시스템과 통합
* [PowerBI](log-analytics-powerbi.md)를 사용하여 요약 보고서 생성

