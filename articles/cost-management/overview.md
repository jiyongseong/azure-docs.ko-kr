---
title: Azure Cost Management 개요 | Microsoft Docs
description: Azure Cost Management는 Azure 및 다른 클라우드 리소스를 사용하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: overview
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 05e53688e1350052fdbbc61451df8a51dc3349cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-cost-management"></a>Azure Cost Management란?

Microsoft 자회사인 Cloudyn에서 라이선스를 부여한 Azure Cost Management를 사용하면 Azure 리소스와 AWS 및 Google을 포함한 다른 클라우드 공급자에 대한 클라우드 사용량 및 비용을 추적할 수 있습니다. 이해하기 쉬운 대시보드 보고서는 비용 할당 및 쇼백/환불을 도울 수 있습니다. Cost Management를 통해 관리하고 조정할 수 있는 사용률이 낮은 리소스를 식별하여 클라우드 소비를 최적화할 수 있습니다.

소개 비디오를 시청하려면 [Azure Cost Management 소개](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo)를 참조하세요.

## <a name="monitor-usage-and-spending"></a>사용량 및 소비 모니터링

조직이 시간이 지남에 따라 사용하는 리소스를 지불하기 때문에 사용량 및 소비를 모니터링하는 작업은 클라우드 인프라에 매우 중요합니다. 사용량이 규약 임계값을 초과하는 경우 즉시 예상치 못한 비용 초과분이 발생할 수 있습니다. 몇 가지 중요한 요소로 인해 임시 모니터링이 어려울 수 있습니다. 첫째, 평균 사용량에 따른 비용을 예상하는 작업에서는 주어진 청구 기간 동안 소비가 일관적으로 유지된다고 가정합니다. 둘째, 비용이 예산에 가깝거나 예산을 초과할 때 지출을 조정하라는 알림을 사전에 받는 것이 중요합니다. 또한 클라우드 서비스 공급자는 비용 예상 및 임계값이나 기간 간 비교 보고서를 제공하지 않을 수 있습니다.

보고서를 통해 소비를 모니터링하여 클라우드 사용량, 비용 및 추세를 분석하고 추적할 수 있습니다. 시간에 따른 보고서를 사용하여 일반 추세와 다른 이상을 감지할 수 있습니다. 클라우드 배포의 비효율성은 최적화 보고서에 표시됩니다. 비용 분석 보고서에서 비효율성을 확인할 수도 있습니다.

![시간에 따른 비용 보고서](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>비용 관리

시간에 따라 사용량 및 비용을 분석하여 추세를 식별하는 경우 기록 데이터로 비용을 관리할 수 있습니다. 추세는 향후 소비량을 예측하는 데 사용됩니다. Cost Management에는 유용한 예상 비용 보고서도 포함되어 있습니다.

비용 할당은 태그를 지정한 정책에 따라 비용을 분석하여 비용을 관리합니다. 사용자 지정 계정, 리소스 및 엔터티에 태그를 사용하여 비용을 구체화할 수 있습니다. Category Manager는 추가 거버넌스를 제공할 수 있는 태그를 구성합니다. 또한 쇼백/환불에 비용 할당을 사용하여 소비 행동에 영향을 주거나 테넌트 고객에게 요금을 청구하는 리소스 사용률 및 관련 비용을 표시합니다.

액세스 제어를 통해 사용자 및 팀이 필요한 Cost Management 데이터에만 액세스하도록 하여 비용을 관리할 수 있습니다. 엔터티 구조, 사용자 관리 및 받는 사람 목록을 포함한 예약된 보고서를 사용하여 액세스를 할당합니다.

경고는 비정상적인 지출 또는 과도한 지출이 발생할 때 자동으로 알려주어 비용을 관리하는 데 도움이 됩니다. 또한 경고는 이해 관계자에게 비정상 지출 및 과다 지출에 대한 위험을 자동으로 알릴 수 있습니다. 다양한 보고서는 예산 및 비용 임계값에 따라 경고를 지원합니다. 단, 경고는 현재 CSP 파트너 계정 또는 구독에 지원되지 않습니다.

## <a name="improve-efficiency"></a>효율성 향상

최적의 VM 사용을 확인하고 유휴 VM을 식별하거나 유휴 VM 및 Cost Management와 연결되지 않은 디스크를 제거할 수 있습니다. 크기 조정 최적화 및 비효율성 보고서의 정보를 사용하여 유휴 VM의 크기를 줄이거나 제거하도록 계획할 수 있습니다. 단, 최적화 보고서는 현재 CSP 파트너 계정 또는 구독에 지원되지 않습니다.

![크기 조정 권장 사항](.\media\overview\sizing.png)

AWS 예약 인스턴스를 프로비전한 경우 구매 권장 사항을 보고, 사용하지 않는 예약을 수정하고, 프로비전을 계획할 수 있는 최적화 보고서에서 예약된 인스턴스 사용률을 향상시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Cost Management에 대해 알아보았으므로 다음 단계는 클라우드 환경을 등록하고 데이터를 탐색하기 시작하는 것입니다.

- [개별 Azure 구독 등록](quick-register-azure-sub.md)
