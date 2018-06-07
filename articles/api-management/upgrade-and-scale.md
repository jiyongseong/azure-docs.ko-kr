---
title: "Azure API Management 인스턴스 업그레이드 및 크기 조정 | Microsoft Docs"
description: "이 항목에서는 Azure API Management 인스턴스를 업그레이드하고 크기 조정하는 방법을 설명합니다."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6ae977344101c02222fd9930e26a083bf5e3f800
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>API Management 인스턴스 업그레이드 및 크기 조정 

고객은 단위를 추가하고 제거하여 APIM(API Management) 인스턴스의 크기를 조정할 수 있습니다. **단위**는 전용 Azure 리소스로 구성되며 월당 API 호출 수로 표현되는 특정 로드 베어링 용량을 포함합니다. 이 숫자는 호출 제한이 아닌 대략적인 용량 계획에 허용할 최대 처리량 값을 나타냅니다. 실제 처리량과 대기 시간은 동시 연결 수 및 속도, 구성된 정책의 종류 및 수, 요청 및 응답 크기, 백 엔드 대기 시간 등의 요소에 따라 크게 달라집니다.

단위당 용량 및 가격은 단위가 존재하는 **계층**에 따라 달라집니다. **개발자**, **기본**, **표준**, **프리미엄**의 4계층 중에서 선택할 수 있습니다. 계층 내에서 서비스에 대한 용량을 늘려야 할 경우 단위를 추가해야 합니다. APIM 인스턴스에서 현재 선택된 계층에서 단위 추가가 허용되지 않는 경우 상위 수준 계층으로 업그레이드해야 합니다. 

단위당 가격 및 사용 가능한 기능(예: 다중 지역 배포)은 APIM 인스턴스에 대해 선택한 계층에 따라 달라집니다. [가격 정보](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 문서에서는 단위당 가격 및 각 계층의 기능에 대해 설명합니다. 

>[!NOTE]
>[가격 정보](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 문서에서는 계층별로 대략적인 단위 용량 수를 보여 줍니다. 보다 정확한 수치를 얻기 위해 API에 대해 현실적인 시나리오를 살펴보아야 합니다. 뒤에 나오는 "용량을 계획하는 방법" 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ 활성 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

## <a name="how-to-plan-for-capacity"></a>용량을 계획하는 방법

사용자 트래픽을 처리하기 위해 충분한 단위가 있는지 확인하려면 예상되는 워크로드를 테스트하세요. 

위에서 설명한 것처럼 APIM 단위가 처리할 수 있는 초당 요청 수는 여러 변수에 따라 달라집니다. 예를 들어 연결 패턴, 요청 및 응답의 크기, 각 API에 구성된 정책, 요청을 보내는 클라이언트 수 등이 있습니다.

지정된 시간에 얼마나 많은 용량이 사용되었는지 파악하는 데는 **메트릭**(Azure Monitor 기능 사용)을 사용합니다.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Azure Portal을 사용하여 메트릭 검사 

1. [Azure Portal](https://portal.azure.com/)에서 APIM 인스턴스로 이동합니다.
2. **메트릭**을 선택합니다.
3. **사용 가능한 메트릭**에서 **용량** 메트릭을 선택합니다. 

    용량 메트릭은 테넌트에서 얼마나 많은 계산 용량을 사용할 수 있는지에 대한 정보를 제공합니다. 해당 값은 메모리, CPU, 네트워크 큐 길이 등 테넌트에서 사용하는 계산 리소스에서 파생됩니다. 처리 중인 요청 수를 직접 측정하지 않습니다. 테넌트에서 요청 로드를 증가시키고 최대 부하에 해당하는 용량 메트릭의 값을 모니터링하여 테스트할 수 있습니다. 예상치 못한 상황이 발생할 때 알려주도록 메트릭 경고를 설정할 수 있습니다. 예를 들어 APIM 인스턴스는 10분 넘게 예상된 최대 용량을 초과했습니다.

    >[!TIP]
    > 서비스의 용량 부족을 알리거나 단위를 추가하여 자동으로 크기를 조정하는 논리 앱을 호출하도록 경고를 구성할 수 있습니다.

## <a name="upgrade-and-scale"></a>업그레이드 및 크기 조정 

이전에 설명한 대로 **개발자**, **기본**, **표준**, **프리미엄**의 4계층 중에서 선택할 수 있습니다. **개발자** 계층은 프로덕션 환경이 아닌 서비스를 평가하는 데 사용해야 합니다. **개발자** 계층에는 SLA가 없으며 이 계층의 크기를 조정(추가/제거)할 수 없습니다. 

**기본**, **표준**, **프리미엄**은 SLA를 포함하고 크기를 조정할 수 있는 프로덕션 계층입니다. **기본** 계층은 SLA를 포함하는 가장 저렴한 계층으로, 최대 2개 단위로 크기를 조정할 수 있으며 **표준** 계층은 최대 4개 단위로 크기를 조정할 수 있습니다. **프리미엄** 계층에는 원하는 수의 단위를 추가할 수 있습니다.

**프리미엄** 계층을 사용하면 원하는 여러 Azure 지역 간에 단일 API Management 인스턴스를 배포할 수 있습니다. API Management 서비스를 처음 만들 때 인스턴스는 하나의 단위만 포함하며 이 인스턴스는 단일 Azure 지역에 상주합니다. 초기 지역은 **주** 지역으로 지정됩니다. 추가 지역은 쉽게 추가할 수 있습니다. 지역을 추가할 때 할당할 단위 수를 지정합니다. 예를 들어 **주** 지역에 한 단위를 포함하고 다른 지역에 5개 단위를 포함할 수 있습니다. 각 지역에 있는 트래픽에 대한 단위 수를 조정할 수 있습니다. 자세한 내용은 [여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법](api-management-howto-deploy-multi-region.md)을 참조하세요.

어떤 계층에서든 업그레이드 및 다운그레이드가 가능합니다. 프리미엄 계층에서 표준 또는 기본으로 다운그레이드할 경우 업그레이드 또는 다운그레이드는 VNET 또는 다중 지역 배포와 같은 일부 기능을 제거할 수 있습니다.

>[!NOTE]
>업그레이드 또는 크기 조정 프로세스를 적용하는 데는 15~45분 정도 소요될 수 있습니다. 완료되면 알림이 표시됩니다.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Azure Portal을 사용하여 업그레이드 및 크기 조정

1. [Azure Portal](https://portal.azure.com/)에서 APIM 인스턴스로 이동합니다.
2. **크기 조정 및 가격 책정**을 선택합니다.
3. 원하는 계층을 선택합니다.
4. 추가하고자 하는 **단위** 수를 지정합니다. 슬라이더를 사용하거나 단위 수를 입력합니다.<br/>
    **프리미엄** 계층을 선택하는 경우 먼저 지역을 선택해야 합니다.
5. **저장**을 누릅니다.

## <a name="next-steps"></a>다음 단계

[여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법](api-management-howto-deploy-multi-region.md)

