---
title: "Azure App Service에 대한 PremiumV2 계층 구성 | Microsoft Docs"
description: "새 PremiumV2 가격 책정 계층으로 확장하여 Azure App Service에서 웹, 모바일 및 API 앱 성능을 향상하는 방법에 대해 알아봅니다."
keywords: "앱 서비스, Azure 앱 서비스, 규모, 확장성, 앱 서비스 계획, 앱 서비스 비용"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 76897173d9fdfffe7139e7c5648ad0efb1c05b97
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Azure App Service에 대한 PremiumV2 계층 구성

새 **PremiumV2** 가격 책정 계층은 프로세서, SSD 저장소 속도를 개선하며 기존 가격 책정 계층의 메모리 대 코어 비율을 두 배로 높입니다. 성능상의 이점 덕분에 더 적은 인스턴스에서 앱을 실행하여 비용을 절감할 수 있습니다. 이 문서에서는 **PremiumV2** 계층에서 앱을 만들거나 **PremiumV2** 계층으로 앱을 강화하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

웹앱을 **PremiumV2**로 강화하려면 Azure App Service에 **PremiumV2**보다 낮은 가격 책정 계층에서 실행되는 웹앱이 있어야 합니다.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 가용성

PremiumV2 계층은 현재 _Windows_의 App Service에서만 사용할 수 있습니다. Linux 컨테이너는 아직 지원되지 않습니다.

PremiumV2는 이미 대부분의 Azure 지역에서 사용할 수 있으며 점점 늘어나고 있습니다. 해당 지역에서 사용할 수 있는지 확인하려면 [Azure Cloud Shell](../cloud-shell/overview.md)에서 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az appservice list-locations --sku P1V2
```

앱을 만들거나 App Service 계획을 만드는 동안 오류가 발생하는 경우에는 선택한 지역에 **PremiumV2**를 사용할 수 없을 가능성이 큽니다.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>PremiumV2 계층에서 앱 만들기

App Service 앱의 가격 책정 계층은 해당 앱이 실행되는 [App Service 계획](azure-web-sites-web-hosting-plans-in-depth-overview.md)에 정의되어 있습니다. App Service 계획은 단독으로 또는 웹앱 만들기의 일부로 만들 수 있습니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 계획을 구성하는 경우 **가격 책정 계층**을 선택합니다. 

**PremiumV2** 옵션 중 하나를 선택하고 **선택**을 클릭합니다.

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> **P1V2**, **P2V2** 및 **P3V2**가 옵션으로 표시되지 않는 경우는 선택한 지역에서 **PremiumV2**를 사용할 수 없거나 **PremiumV2**를 지원하지 않는 Linux App Service 계획을 구성 중인 경우입니다.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>기존 앱을 PremiumV2 계층으로 강화

기존 앱을 **PremiumV2** 계층으로 확장하기 전에 해당 지역에서 **PremiumV2**를 사용할 수 있는지 확인합니다. 자세한 내용은 [PremiumV2 가용성](#availability)을 참조하세요. 해당 계층을 사용할 수 없는 지역의 경우 [지원되지 않는 지역에서 강화](#unsupported)를 참조하세요.

호스팅 환경에 따라 강화하는 데 추가 단계가 필요할 수 있습니다. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 앱 페이지를 엽니다.

App Service 앱 페이지의 왼쪽 탐색 영역에서 **강화(App Service 계획)**를 선택합니다.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**PremiumV2** 크기 중 하나를 선택한 다음 **선택**을 클릭합니다.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

작업이 성공적으로 완료되면 앱의 개요 페이지에 이제 앱이 **PremiumV2** 계층에 있다고 표시됩니다.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>오류가 발생한 경우

일부 App Service 계획은 PremiumV2 계층으로 강화할 수 없습니다. 강화 작업에 오류가 발생하는 경우 앱에 대한 새 App Service 계획이 필요합니다.

기존 App Service 앱과 같은 지역 및 리소스 그룹에서 _Windows_ App Service 계획을 만듭니다. **PremiumV2** 계층으로 설정하려면 [PremiumV2 계층에서 앱 만들기](#create)의 단계를 수행합니다. 원하는 경우 기존 App Service 계획과 같은 확장 구성(인스턴스 수, 자동 크기 조정 등)을 사용합니다.

App Service 앱 페이지를 다시 엽니다. App Service의 왼쪽 탐색에서 **App Service 계획 변경**을 선택합니다.

![](media/app-service-configure-premium-tier/change-plan.png)

본인이 만든 App Service 계획을 선택합니다.

![](media/app-service-configure-premium-tier/select-plan.png)

변경 작업이 완료되면 앱이 **PremiumV2** 계층에서 실행됩니다.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>지원되지 않는 지역에서 강화

앱이 아직 **PremiumV2**를 사용할 수 없는 지역에서 실행되는 경우 앱을 다른 지역으로 이동하여 **PremiumV2**를 활용할 수 있습니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- 새 **PremiumV2** 계획에서 앱을 만든 다음 응용 프로그램 코드를 다시 배포합니다. **PremiumV2** 계층으로 설정하려면 [PremiumV2 계층에서 앱 만들기](#create)의 단계를 수행합니다. 원하는 경우 기존 App Service 계획과 같은 확장 구성(인스턴스 수, 자동 크기 조정 등)을 사용합니다.
- 앱이 이미 **프리미엄** 계층에서 실행되는 경우에는 모든 앱 설정, 연결 문자열 및 배포 구성과 함께 앱을 복제할 수 있습니다.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    **앱 복제** 페이지에서 원하는 지역에 App Service 계획을 만들고 원하는 설정을 지정하여 복제할 수 있습니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/overview)을 통해 스크립트를 사용하여 **PremiumV2** 계층에서 앱 생성을 자동화할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

다음 명령은 _P1V2_에서 App Service 계획을 만듭니다. Cloud Shell에서 실행할 수 있습니다. `--sku`에 대한 옵션은 P1V2, _P2V2_ 및 _P3V2_입니다.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

다음 명령은 _P1V2_에서 App Service 계획을 만듭니다. `-WorkerSize`에 대한 옵션은 _Small_, _Medium_ 및 _Large_입니다.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>추가 리소스

[Azure에서 앱 강화](web-sites-scale.md)  
[수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md)