---
title: "PowerShell을 사용하여 웹앱 복제"
description: "PowerShell을 사용하여 Web Apps를 새 Web Apps에 복제하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>PowerShell을 사용하여 Azure App Service 앱 복제
Microsoft Azure PowerShell 버전 1.1.0 릴리스에서는 기존 웹앱을 다른 지역이나 동일한 지역에서 새로 만든 앱에 복제할 수 있도록 하는 새 옵션이 `New-AzureRMWebApp`에 추가되었습니다. 이 옵션을 사용하여 고객은 수많은 앱을 다른 지역에 배포할 수 있습니다.

앱 복제는 현재 프리미엄 계층의 앱 서비스 계획에만 지원됩니다. 새로운 기능은 Web Apps Backup 기능과 동일한 제한 사항을 사용합니다. [Azure App Service에서 Web Apps Backup](web-sites-backup.md)을 참조하세요.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>기존 앱 복제
시나리오: 미국 중남부 지역에 기존 웹앱이 있고, 콘텐츠를 미국 중북부 지역의 새 웹앱으로 복제하려고 합니다. 이 작업은 `-SourceWebApp` 옵션으로 새 웹앱을 만들기 위해 PowerShell cmdlet의 Azure Resource Manager 버전을 사용하여 수행할 수 있습니다.

원본 웹앱을 포함하는 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 원본 웹앱의 정보를 가져올 수 있습니다(이 경우 이름은 `source-webapp`임).

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

새 App Service 계획을 만들려면 다음 예제처럼 `New-AzureRmAppServicePlan` 명령을 사용할 수 있습니다.

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

`New-AzureRmWebApp` 명령을 사용하여 미국 중북부 지역에 새 웹앱을 만든 후 기존 프리미엄 계층인 App Service 계획에 연결할 수 있습니다. 그뿐 아니라 원본 웹앱과 동일한 리소스 그룹을 사용하거나 다음 명령에 표시된 것처럼 새 리소스 그룹을 정의할 수 있습니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

연결된 모든 배포 슬롯을 포함하는 기존 웹앱을 복제하려면 `IncludeSourceWebAppSlots` 매개 변수를 사용해야 합니다. 다음 PowerShell 명령은 `New-AzureRmWebApp` 명령에서 해당 매개 변수를 사용하는 방법을 보여 줍니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

동일한 지역 내에서 기존 웹앱을 복제하려면 사용자는 새 리소스 그룹 및 동일한 지역의 새 앱 서비스 계획을 만들고 다음 PowerShell 명령을 사용하여 웹앱을 복제해야 합니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>기존 앱을 App Service Environment로 복제
시나리오: 미국 중남부 지역에 기존 웹앱이 있고, 기존 ASE(App Service Environment)에 있는 새 웹앱으로 콘텐츠를 복제하려고 합니다.

원본 웹앱을 포함하는 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 원본 웹앱의 정보를 가져올 수 있습니다(이 경우 이름은 `source-webapp`임).

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

ASE의 이름 및 ASE가 속한 리소스 그룹 이름을 알고 있으면 다음 명령과 같이 기존 ASE에 새 웹앱을 만들 수 있습니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` 매개 변수는 레거시 이유로 인해 필요하지만 ASE에 앱을 만들 때는 무시됩니다. 

## <a name="cloning-an-existing-app-slot"></a>기존 앱 슬롯 복제
시나리오: 기존 웹앱 슬롯을 새 웹앱이나 새 웹앱 슬롯에 복제하려고 합니다. 새 웹앱은 원래 웹앱 슬롯과 동일한 지역이나 다른 지역에 있을 수 있습니다.

원본 웹앱을 포함하는 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 웹앱 `source-webapp`에 연결된 원본 웹앱 슬롯의 정보를 가져올 수 있습니다(이 경우 이름은 `source-webappslot`임).

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

다음 명령에서는 새 웹앱에 원본 웹앱의 클론을 만드는 방법을 보여 줍니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>앱을 복제하는 동한 Traffic Manager 구성
다중 지역 웹앱을 만들고 이러한 모든 웹앱으로 트래픽을 라우팅하도록 Azure Traffic Manager를 구성하는 것은 고객 앱의 고가용성을 유지하기 위한 중요한 시나리오입니다. 기존 웹앱을 복제하는 경우 두 웹앱을 새로운 Traffic Manager 프로필 또는 기존 프로필에 연결할 수 있는 옵션이 제공됩니다. Azure Resource Manager 버전의 Traffic Manager만 지원됩니다.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>앱을 복제하는 동안 새 Traffic Manager 프로필 만들기
시나리오: 두 웹앱을 모두 포함하는 Azure Resource Manager Traffic Manager 프로필을 구성하는 동안 다른 지역에 웹앱을 복제하려고 합니다. 다음 명령에서는 새 Traffic Manager 프로필을 구성하는 동안 새 웹앱으로 원본 웹앱의 클론을 만드는 방법을 보여 줍니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>기존 Traffic Manager 프로필에 복제된 새 Web App 추가
시나리오: Azure Resource Manager Traffic Manager 프로필이 이미 있으며 두 웹앱을 끝점으로 추가하려고 합니다. 이렇게 하려면 먼저 기존 Traffic Manager 프로필 ID를 조합해야 합니다. 구독 ID, 리소스 그룹 이름 및 기존 Traffic Manager 프로필 이름이 필요합니다.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Traffic Manager ID를 알게 된 후 다음 명령에서는 원본 웹앱과 새 웹앱을 기존 Traffic Manager 프로필에 추가하는 동안 원본 웹앱의 클론을 새 웹앱으로 만드는 방법을 보여 줍니다.

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>현재 제한 사항
이 기능은 현재 미리 보기로 제공되며, 시간에 따라 새 기능이 추가됩니다. 현재 버전의 앱 복제에 대한 알려진 제한은 다음과 같습니다.

* 자동 크기 설정은 복제되지 않습니다.
* Backup 일정 설정은 복제되지 않습니다.
* VNET 설정은 복제되지 않습니다.
* App Insights는 대상 웹앱에서 자동으로 설치되지 않습니다.
* 간편한 인증 설정은 복제되지 않습니다.
* Kudu 확장은 복제되지 않습니다.
* TiP 규칙은 복제되지 않습니다.
* 데이터베이스 내용이 복제되지 않습니다.
* 다른 배율 단위로 복제하는 경우 아웃바운드 IP 주소가 변경됩니다.

### <a name="references"></a>참조
* [웹앱 복제](app-service-web-app-cloning.md)
* [Azure App Service에서 웹앱 백업](web-sites-backup.md)
* [Azure Traffic Manager에 대한 Azure Resource Manager 지원 미리 보기](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service Environment 소개](environment/intro.md)
* [Azure 리소스 관리자로 Azure PowerShell 사용](../azure-resource-manager/powershell-azure-resource-manager.md)

