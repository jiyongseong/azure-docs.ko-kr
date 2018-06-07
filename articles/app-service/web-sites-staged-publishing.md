---
title: Azure App Service에서 웹앱에 대한 스테이징 환경 설정| Microsoft Docs
description: Azure App Service에서 웹앱에 대한 준비된 개시를 사용하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: 2fabf0d61ffd2f526fab49816eab36a86497a358
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service에서 스테이징 환경 설정
<a name="Overview"></a>

웹앱, Linux의 웹앱, 모바일 백 엔드 및 API 앱을 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포할 때 **표준** 또는 **프리미엄** App Service 계획 계층에서 실행하면 기본 프로덕션 슬롯 대신 별도의 배포 슬롯에 배포할 수 있습니다. 배포 슬롯은 실제로 고유한 호스트 이름이 있는 라이브 앱입니다. 앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 응용 프로그램을 배포 슬롯에 배포하면 다음과 같은 이점이 있습니다.

* 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 앱 변경 사항의 유효성을 검사할 수 있습니다.
* 먼저 슬롯으로 앱을 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴스가 준비되어 있는 상태입니다. 따라서 앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 교환 유효성 검사가 필요하지 않은 경우 [자동 교환](#Auto-Swap) 을 구성하여 이 전체 워크플로를 자동화할 수 있습니다.
* 교환 후에는 이전의 준비된 앱이 들어 있던 슬롯 안에 이전의 프로덕션 앱이 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 상태"로 돌아갈 수 있습니다.

각 App Service 계획 계층은 다양한 수의 배포 슬롯을 지원합니다. 앱 계층이 지원하는 슬롯의 수를 알아보려면 [App Service 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)을 참조하세요. 다른 계층으로 앱의 크기를 조정하려면 대상 계층은 앱에서 이미 사용하는 슬롯 수를 지원해야 합니다. 예를 들어 앱에 5개 이상의 슬롯이 있는 경우 **표준** 계층은 5개의 배포 슬롯만을 지원하므로 **표준** 계층으로 축소할 수 없습니다.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>배포 슬롯 추가
여러 배포 슬롯을 사용하려면 앱이 **표준** 또는 **프리미엄** 계층에서 실행 중이어야 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 앱의 [리소스 블레이드](../azure-resource-manager/resource-group-portal.md#manage-resources)를 엽니다.
2. **배포 슬롯** 옵션을 선택한 후 **슬롯 추가**를 클릭합니다.
   
    ![새 배포 슬롯 추가][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > 앱이 **표준** 또는 **프리미엄** 계층이 아닌 경우 준비된 게시를 사용하려면 지원 계층을 나타내는 메시지를 받게 됩니다. 이때 **업그레이드**를 선택할 수 있는 옵션이 제공되고 계속하기 전에 앱의 **크기 조정** 탭으로 이동합니다.
   > 
   > 
3. **슬롯 추가** 블레이드에서 슬롯에 이름을 지정하고, 다른 기존 배포 슬롯으로부터 앱 구성을 복제할 것인지 여부를 선택합니다. 확인 표시를 클릭하여 계속합니다.
   
    ![구성 원본][ConfigurationSource1]
   
    슬롯을 처음 추가할 때는 두 가지 선택만 가능합니다. 프로덕션의 기본 슬롯으로부터 구성을 복제하거나, 구성을 아예 복제하지 않는 것입니다.
    여러 개의 슬롯을 만든 후에는 프로덕션이 아닌 슬롯으로부터 구성을 복제할 수 있습니다.
   
    ![구성 원본][MultipleConfigurationSources]
4. 앱의 리소스 블레이드에서 **배포 슬롯**을 클릭한 다음 배포 슬롯을 클릭하여 해당 슬롯의 리소스 블레이드를 엽니다. 그러면 다른 앱과 마찬가지로 메트릭 집합 및 구성이 표시됩니다. 배포 슬롯을 보고 있다는 사실을 상기시키기 위해 블레이드 상단에 슬롯 이름이 표시됩니다.
   
    ![배포 슬롯 제목][StagingTitle]
5. 슬롯의 블레이드에서 앱 URL을 클릭합니다. 배포 슬롯은 고유의 호스트 이름을 가지고 있고 Live App이기도 합니다. 배포 슬롯에 대한 공용 액세스를 제한하려면 [App Service 웹앱 – 비 프로덕션 배포 슬롯에 대한 웹 액세스 차단](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)을 참조하세요.

배포 슬롯을 만든 후 콘텐츠가 없습니다. 다른 리포지토리 분기 또는 아예 다른 리포지토리로부터 슬롯에 배포할 수 있습니다. 슬롯의 구성을 변경할 수도 있습니다. 게시 프로필을 사용하거나 콘텐츠 업데이트를 위해 배포 슬롯에 연결된 배포 자격 증명을 사용합니다.  예를 들어 [git를 사용하여 이 슬롯에 게시](app-service-deploy-local-git.md)할 수 있습니다.

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>어떤 설정이 교환되나요?
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 또한, 교환 후(특정 슬롯) 다른 구성 요소는 동일한 슬롯에 남아 있지만 일부 구성 요소는 교환(특정 슬롯 아님)에 따라 콘텐츠를 따릅니다. 다음 목록은 슬롯을 교환할 때 변경되는 설정을 보여줍니다.

**교환된 설정**:

* 프레임워크 버전, 32/64비트, 웹 소켓과 같은 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 모니터링 및 진단 설정
* WebJob 콘텐츠

**교환되지 않은 설정**:

* 게시 끝점
* 사용자 지정 도메인 이름
* SSL 인증서 및 바인딩
* 크기 조정 설정
* WebJob 스케줄러

슬롯에 맞도록(교환되지 않음) 앱 설정 또는 연결 문자열을 구성하려면 특정 슬롯에 대해 **응용 프로그램 설정** 블레이드에 액세스한 다음 슬롯에 맞아야 하는 구성 요소에 대한 **슬롯 설정** 상자를 선택합니다. 특정 슬롯으로 구성 요소를 표시하면 앱과 연결된 모든 배포 슬롯에서 교환할 수 없도록 요소를 설정하는 효과가 있습니다.

![슬롯 설정][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>배포 슬롯 교환 
앱 리소스 블레이드의 **개요** 또는 **배포 슬롯** 보기에서 배포 슬롯을 교환할 수 있습니다.

> [!IMPORTANT]
> 배포 슬롯에서 프로덕션으로 앱을 교환하기 전에 특정 슬롯이 아닌 모든 설정이 해당 교환 대상에서 원하는 대로 정확히 구성되어야 합니다.
> 
> 

1. 배포 슬롯을 교환하려면 앱의 명령 모음 또는 배포 슬롯의 명령 모음에서 **교환** 단추를 클릭합니다.
   
    ![교환 단추][SwapButtonBar]

2. 교환 원본 및 교환 대상이 제대로 설정되어야 합니다. 일반적으로 교환 대상은 프로덕션 슬롯입니다. **확인** 을 클릭하여 작업을 완료합니다. 작업을 마치면 배포 슬롯이 교환됩니다.

    ![전체 교환](./media/web-sites-staged-publishing/SwapImmediately.png)

    **미리 보기가 있는 교환** 교환 유형의 경우 [미리 보기가 있는 교환(다단계 교환)](#Multi-Phase)을 참조하세요.  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>미리 보기가 있는 교환(다단계 교환)

미리 보기가 있는 교환 또는 다단계 교환은 연결 문자열과 같은 슬롯 관련 구성 요소의 유효성 검사를 간소화합니다.
업무상 중요한 워크로드의 경우 프로덕션 슬롯의 구성이 적용될 때 앱이 예상대로 동작하는지 확인하려고 할 것입니다. 이러한 유효성 검사는 앱이 프로덕션으로 교환되기 *전에* 수행해야 합니다. 미리 보기가 있는 교환이 필요합니다.

> [!NOTE]
> 미리 보기가 있는 교환은 Linux의 웹앱에서 지원되지 않습니다.

**미리 보기가 있는 교환** 옵션을 사용할 경우([배포 슬롯 교환](#Swap)) App Service에서 다음을 수행합니다.

- 대상 슬롯(예: 프로덕션)의 기존 워크로드가 영향을 받지 않도록 해당 슬롯을 변경되지 않은 상태로 유지합니다.
- 슬롯별 연결 문자열 및 앱 설정을 포함하는 대상 슬롯의 구성 요소를 원본 슬롯에 적용합니다.
- 앞에서 언급한 이러한 구성 요소를 사용하여 원본 슬롯에서 작업자 프로세스를 다시 시작합니다.
- 교환을 완료한 경우: 준비되기 전 원본 슬롯을 대상 슬롯으로 이동합니다. 대상 슬롯은 수동 교환에서와 같이 원본 슬롯으로 이동됩니다.
- 교환을 취소한 경우: 원본 슬롯의 구성 요소를 원본 슬롯에 다시 적용합니다.

앱이 대상 슬롯의 구성에서 동작하는 방식을 정확하게 미리 볼 수 있습니다. 유효성 검사를 완료하면 별도 단계에서 교환을 완료합니다. 이 단계는 원본 슬롯이 이미 원하는 구성으로 준비되고 클라이언트에서 가동 중지 시간이 발생하지 않는다는 추가적인 이점을 제공합니다.  

다단계 교환에 사용 가능한 Azure PowerShell cmdlet 샘플은 배포 슬롯 섹션에 대한 Azure PowerShell cmdlet에 포함되어 있습니다.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>자동 교환 구성
자동 교환은 앱의 최종 사용자를 위해 중단 시간 및 콜드 부팅이 발생하지 않는 앱을 지속적으로 배포하려는 DevOps 시나리오를 간소화합니다. 배포 슬롯이 자동 교환에 대해 프로덕션에 구성될 때, 해당 슬롯에 이미 준비된 후에 해당 슬롯에 코드 업데이트를 푸시할 때마다 App Service가 앱을 프로덕션으로 자동 교환합니다.

> [!IMPORTANT]
> 슬롯에 대해 자동 교환을 사용할 때 슬롯 구성은 정확히 대상 슬롯(일반적으로 프로덕션 슬롯)에 의도한 구성이어야 합니다.
> 
> 

> [!NOTE]
> 자동 교환은 Linux의 웹앱에서 지원되지 않습니다.

슬롯에 대한 자동 교환 구성은 쉽습니다. 다음 단계를 수행하세요.

1. **배포 슬롯**에서 비프로덕션 슬롯을 선택하고 해당 슬롯의 리소스 블레이드에서 **응용 프로그램 설정**을 선택합니다.  
   
    ![][Autoswap1]
2. **자동 교환**은 **켜기**로 선택하고 **자동 교환 슬롯**에서 원하는 대상 슬롯을 선택한 다음 명령 모음에서 **저장**을 클릭합니다. 슬롯에 대한 구성은 정확히 대상 슬롯에 의도한 구성이어야 합니다.
   
    작업이 완료되면 **알림** 탭에 **성공**이 녹색으로 깜박입니다.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > 앱에 대한 자동 교환을 테스트하려면 먼저 **자동 교환 슬롯** 에서 비프로덕션 대상 슬롯을 선택하여 기능에 익숙해져야 합니다.  
   > 
   > 
3. 해당 배포 슬롯에 코드 푸시를 실행합니다. 자동 교환은 짧은 시간 후에 발생하며 업데이트는 대상 슬롯의 URL에 반영됩니다.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>교환 후 프로덕션 앱 롤백
슬롯 교환 후 프로덕션에서 오류가 발견되면 같은 두 슬롯을 즉시 교환하여 슬롯을 교환 전 상태로 롤백하세요.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>교환하기 전에 사용자 지정 준비
일부 앱에는 사용자 지정 준비 작업이 필요할 수 있습니다. web.config의 `applicationInitialization` 구성 요소를 사용하면 요청을 받기 전에 수행할 사용자 지정 초기화 작업을 지정할 수 있습니다. 교환 작업은 이 사용자 지정 준비가 완료될 때까지 대기합니다. 샘플 web.config 조각은 다음과 같습니다.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

## <a name="monitor-swap-progress"></a>교환 진행률 모니터링

경우에 따라 교환 작업은 교환된 앱이 긴 준비 시간을 갖는 것처럼 완료하는 데 다소 시간이 걸립니다. [Azure Portal](https://portal.azure.com)의 [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에서 교환 작업에 대한 자세한 정보를 얻을 수 있습니다.

포털의 앱 페이지에서 왼쪽 탐색의 **활동 로그**를 선택합니다.

교환 작업은 `Slotsswap`으로 로그 쿼리에 표시됩니다. 이를 확장하고 하위 작업 또는 오류 중 하나를 선택하여 세부 정보를 볼 수 있습니다.

![슬롯 교환에 대한 활동 로그](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>배포 슬롯 삭제
배포 슬롯의 블레이드에서 배포 슬롯의 블레이드를 열고 **개요**(기본 페이지)를 클릭한 후 명령 모음에서 **삭제**를 클릭합니다.  

![배포 슬롯 삭제][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Azure PowerShell을 사용하여 자동화

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈로, Azure App Service에서 배포 슬롯을 관리하는 기능도 지원합니다.

* Azure PowerShell을 설치 및 구성하는 방법과 Azure 구독에 Azure PowerShell을 인증하는 방법에 대한 자세한 내용은 [Microsoft Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.  

- - -
### <a name="create-a-web-app"></a>웹앱 만들기
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>배포 슬롯 만들기
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>미리 보기가 있는 교환(다단계 교환) 시작 및 대상 슬롯 구성을 원본 슬롯에 적용
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>보류 중인 교환(검토가 있는 교환) 취소 및 원본 슬롯 구성 복원
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>배포 슬롯 교환
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>활동 로그에서 교환 이벤트 모니터링
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>배포 슬롯 삭제
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Azure CLI를 사용하여 자동화

배포 슬롯에 대한 [Azure CLI](https://github.com/Azure/azure-cli) 명령은 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure App Service 웹앱 – 비 프로덕션 배포 슬롯에 대한 웹 액세스 차단](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Linux의 App Service 소개](../app-service/containers/app-service-linux-intro.md)  
[Microsoft Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

