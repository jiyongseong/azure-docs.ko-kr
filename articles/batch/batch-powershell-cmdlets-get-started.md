---
title: Azure Batch용 PowerShell 시작 | Microsoft Docs
description: Batch 리소스를 관리하는 데 사용할 수 있는 Azure PowerShell cmdlet에 대한 간략한 소개입니다.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d263dbb89b10876c3a1deb330f969c598addd6a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>PowerShell cmdlet을 사용한 Batch 리소스 관리

Azure Batch PowerShell cmdlet을 사용하여 Batch API, Azure Portal, Azure CLI(명령줄 인터페이스)에서 실행한 많은 동일한 작업을 수행하고 스크립트를 작성할 수 있습니다. Batch 계정을 관리하고 풀, 작업, 태스크 등의 Batch 리소스 작업에 사용할 수 있는 cmdlet에 대해 간략히 소개합니다.

Batch cmdlet의 전체 목록과 상세 cmdlet 구문은 [Azure Batch cmdlet 참조](/powershell/module/azurerm.batch/#batch)에서 확인하세요.

이 문서는 Azure PowerShell 버전 3.0.0의 cmdlet를 기반으로 합니다. 서비스 업데이트 및 향상을 최대한 활용하기 위해서 Azure PowerShell을 자주 업데이트하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건
Batch 리소스를 관리하도록 Azure PowerShell을 사용하여 다음 작업을 수행합니다.

* [Azure PowerShell 설치 및 구성](/powershell/azure/overview)
* **Connect-AzureRmAccount** cmdlet을 실행하여 구독에 연결(Azure Batch cmdlet은 Azure Resource Manager 모듈에 탑재됨):
  
    `Connect-AzureRmAccount`
* **Batch 공급자 네임 스페이스를 등록**합니다. 이 작업은 **구독당 한 번**만 수행하면 됩니다.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Batch 계정 및 키 관리
### <a name="create-a-batch-account"></a>Batch 계정 만들기
**New-AzureRmBatchAccount**는 지정된 리소스 그룹에서 배치 계정을 만듭니다. 아직 리소스 그룹이 없는 경우 [새 AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet을 실행하 여 만듭니다. **위치** 매개 변수에서 "미국 중부"와 같이 Azure 지역 중 하나를 지정합니다. 예: 

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

그런 다음, 리소스 그룹에 Batch 계정을 만들어, <*account_name*>의 계정 이름과 리소스 그룹의 위치와 이름을 지정합니다. Batch 계정을 만드는 데 다소 시간이 걸릴 수 있습니다. 예: 

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Batch 계정 이름은 리소스 그룹에 대해 Azure 지역에 고유해야 하며, 3자에서 24자 사이의 문자를 포함하고, 소문자와 숫자만 사용해야 합니다.
> 
> 

### <a name="get-account-access-keys"></a>계정 액세스 키 가져오기
**Get-AzureRmBatchAccountKey** 는 Azure 배치 계정과 연결된 액세스 키를 표시합니다. 예를 들어, 사용자가 만든 계정의 기본 및 보조 키를 가져오려면 다음을 실행합니다.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>새 액세스 키 생성
**New-AzureRmBatchAccountKey** 는 Azure 배치 계정에 대해 기본 또는 보조 계정 키를 새로 생성합니다. 예를 들어, Batch 계정의 새 기본 키를 생성하려면 다음과 같이 입력합니다.

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> 새 보조 키를 생성하려면 **KeyType** 매개 변수에 "Secondary"를 지정합니다. 기본 및 보조 키를 개별적으로 다시 생성해야 합니다.
> 
> 

### <a name="delete-a-batch-account"></a>Batch 계정 삭제
**Remove-AzureRmBatchAccount** 는 배치 계정을 삭제합니다. 예: 

    Remove-AzureRmBatchAccount -AccountName <account_name>

메시지가 나타나면 계정을 제거할 것인지 확인합니다. 계정을 제거하는 데는 시간이 걸릴 수 있습니다.

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext 개체 만들기
배치 풀, 작업, 태스크, 기타 리소스를 만들고 관리할 때 배치 PowerShell cmdlet을 사용하여 인증하려면, 우선 계정 이름과 키를 저장할 BatchAccountContext 개체를 만듭니다.

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

BatchAccountContext 개체를 **BatchContext** 매개 변수를 사용하는 cmdlet에 전달합니다.

> [!NOTE]
> 기본적으로 계정의 기본 키는 인증에 사용되지만 BatchAccountContext 개체의 **KeyInUse** 속성을 변경하면 사용할 키를 명시적으로 선택할 수 있습니다. `$context.KeyInUse = "Secondary"`
> 
> 

## <a name="create-and-modify-batch-resources"></a>Batch 리소스 만들기 및 수정
**New-AzureBatchPool**, **New-AzureBatchJob** 및 **New-AzureBatchTask** 등의 cmdlet을 사용하여 배치 계정 아래 리소스를 만듭니다. 기존 리소스 속성을 업데이트하는 해당 **Get-** 및 **Set-** cmdlet과, Batch 계정에서 리소스를 제거하는 **Remove-** cmdlet이 있습니다.

이러한 많은 cmdlet을 사용하는 경우 BatchContext 개체를 전달 하는 것 외에도, 다음 예제와 같이 상세한 리소스 설정을 포함하는 개체를 만들거나 전달해야 합니다. 추가 예제를 보려면 각 cmdlet에 대한 자세한 도움말을 참조하세요.

### <a name="create-a-batch-pool"></a>Batch 풀 만들기
Batch 풀을 만들거나 업데이트할 때 계산 노드의 운영 체제에 대해 클라우드 서비스 구성 또는 가상 머신 구성을 선택합니다( [배치 기능 개요](batch-api-basics.md#pool) 참조). 클라우드 서비스 구성을 지정하면 계산 노드가 [Azure 게스트 OS 릴리스](../cloud-services/cloud-services-guestos-update-matrix.md#releases) 중 하나로 이미지가 만들어집니다. 가상 컴퓨터 구성을 지정하는 경우 [Azure Virtual Machines Marketplace][vm_marketplace]에 나열된 지원되는 Linux 또는 Windows VM 이미지 중 하나를 지정하거나 미리 준비한 사용자 지정 이미지를 제공할 수 있습니다.

**New-AzureBatchPool**을 실행하는 경우, PSCloudServiceConfiguration 또는 PSVirtualMachineConfiguration 개체의 운영 체제 설정을 전달합니다. 예를 들어, 다음 cmdlet는 제품군 3(Windows Server 2012)의 최신 운영 체제 버전을 통해 이미지를 만든 클라우드 서비스 구성에서 소규모 계산 노드로 새 Batch 풀을 만듭니다. 여기서 **CloudServiceConfiguration** 매개 변수는 *$configuration* 변수를 PSCloudServiceConfiguration 개체로 지정합니다. **BatchContext** 매개 변수는 이전에 정의한 *$context* 변수를 BatchAccountContext 개체로 지정합니다.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

새 풀의 계산 노드 대상 수는 자동 크기 조정 수식에 의해 결정됩니다. 이 경우 공식은 단순히 **$TargetDedicated=4**이며 풀의 계산 노드 수는 최대 4개입니다.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>풀, 작업, 태스크 및 기타 상세 정보 쿼리
**Get-AzureBatchPool**, **Get-AzureBatchJob** 및 **Get-AzureBatchTask** 등의 cmdlet을 사용하여 배치 계정 아래에 만든 엔터티를 쿼리합니다.

### <a name="query-for-data"></a>데이터에 대한 쿼리
예제와 같이 **Get AzureBatchPools**을 사용하여 풀을 찾습니다. 이 작업은 기본적으로 사용자 계정 아래의 모든 풀을 쿼리합니다. 이때 *$context*에는 이미 BatchAccountContext 개체가 저장되어 있다고 가정합니다.

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>OData 필터 사용
**Filter** 매개 변수를 사용하여 OData 필터를 제공하면 사용자와 관계가 있는 개체만 찾을 수 있습니다. 예를 들어, "myPool"로 시작하는 ID를 갖는 모든 풀을 찾을 수 있습니다.

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

이 방법은 로컬 파이프라인에서 "Where-Object"를 사용하는 것만큼 유연하지는 않습니다. 그러나 쿼리가 Batch 서비스에 직접 전송되므로 서버에서 모든 필터링이 수행되어 인터넷 대역폭이 절약됩니다.

### <a name="use-the-id-parameter"></a>ID 매개 변수 사용
OData 필터의 대안은 **ID** 매개 변수를 사용하는 것입니다. ID가 "myPool"인 특정 풀을 쿼리하려면

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

**ID** 매개 변수는 전체 ID 검색만 지원하며 와일드카드 또는 OData 스타일 필터를 지원하지 않습니다.

### <a name="use-the-maxcount-parameter"></a>MaxCount 매개 변수 사용
기본적으로 각 cmdlet은 최대 1000개의 개체를 반환합니다. 이 제한에 도달하면 더 적은 수의 개체를 반환하도록 필터를 조정하거나 **MaxCount** 매개 변수를 사용하여 최대값을 명시적으로 설정합니다. 예: 

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

상한값을 제거하려면 **MaxCount** 를 0 이하로 설정합니다.

### <a name="use-the-powershell-pipeline"></a>PowerShell 파이프라인 사용
Batch cmdlet은 PowerShell 파이프라인을 활용하여 cmdlet 간에 데이터를 전송할 수 있습니다. 이 방식은 매개 변수를 지정하는 것과 같은 효과가 있지만 보다 쉽게 여러 엔터티로 작업할 수 있습니다.

예를 들어, 다음과 같이 사용자 계정 아래의 모든 작업을 찾아서 표시할 수 있습니다.

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

풀의 모든 계산 노드 다시 시작(다시 부팅):

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>응용 프로그램 패키지 관리
응용 프로그램 패키지는 풀에서 계산 노드에 응용 프로그램을 배포하는 간단한 방법을 제공합니다. Batch PowerShell cmdlet으로 Batch 계정에 응용 프로그램 패키지를 업로드하여 관리하고 노드를 계산하는 패키지 버전을 배포할 수 있습니다.

**만듭니다** .

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**추가합니다** .

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

응용 프로그램의 **기본 버전** 설정:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

응용 프로그램의 패키지 **열거**

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

응용 프로그램 패키지 **삭제**

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

응용 프로그램 **삭제**

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> 응용 프로그램을 삭제하기 전에 모든 응용 프로그램의 패키지 버전을 삭제해야 합니다. 현재 응용 프로그램 패키지를 보유하는 응용 프로그램을 삭제하려고 하면 '충돌' 오류 메시지를 받게 됩니다.
> 
> 

### <a name="deploy-an-application-package"></a>응용 프로그램 패키지 배포
새 풀을 만들 때 배포에 하나 이상의 응용 프로그램 패키지를 지정할 수 있습니다. 풀을 만들 때 패키지를 지정하는 경우 노드가 풀에 조인하면 각 노드에 배포됩니다. 패키지는 노드를 다시 부팅하거나 이미지로 다시 설치하는 경우에 배포됩니다.

풀에 참가하면서 풀의 노드에 응용 프로그램 패키지를 배포하도록 풀을 만들 때 `-ApplicationPackageReference` 옵션을 지정합니다. 먼저 **PSApplicationPackageReference** 개체를 만들고 이 개체를 풀의 계산 노드에 배포하려는 응용 프로그램 ID 및 패키지 버전으로 구성:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

이제 풀을 만들고 패키지 참조 개체를 `ApplicationPackageReferences` 옵션에 대한 인수로 지정:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

[Batch 응용 프로그램 패키지를 사용하여 계산 노드에 응용 프로그램 배포](batch-application-packages.md)에서 응용 프로그램 패키지에 대한 자세한 정보를 찾을 수 있습니다.

> [!IMPORTANT]
> 응용 프로그램 패키지를 사용하려면 [Azure Storage 계정](#linked-storage-account-autostorage) 을 Batch 계정에 연결해야 합니다.
> 
> 

### <a name="update-a-pools-application-packages"></a>풀의 응용 프로그램 패키지 업데이트
기존 풀에 할당된 응용 프로그램을 업데이트하려면 먼저 원하는 속성(응용 프로그램 ID 및 패키지 버전)의 PSApplicationPackageReference 개체를 만듭니다.

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

다음으로, Batch에서 풀을 가져오고 기존 패키지를 모두 지우고 우리의 새 패키지 참조를 추가하고 새 풀 설정을 사용하여 Batch 서비스를 업데이트합니다.

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

이제 Batch 서비스에서 풀의 속성을 업데이트했습니다. 실제로 풀의 계산 노드에 새로운 응용 프로그램 패키지를 배포하려면, 해당 노드를 다시 시작하거나 이미지로 다시 설치해야 합니다. 이 명령을 사용하여 풀의 모든 노드를 다시 시작할 수 있습니다.

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> 여러 응용 프로그램 패키지를 풀의 계산 노드에 배포할 수 있습니다. 현재 배포된 패키지를 교체하는 대신 응용 프로그램 패키지를 *추가*하고자 하는 경우 위의 `$pool.ApplicationPackageReferences.Clear()` 줄을 생략합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* 자세한 cmdlet 구문 및 예제는 [Azure Batch cmdlet 참조](/powershell/module/azurerm.batch/#batch)를 참조하세요.
* Batch의 응용 프로그램과 응용 프로그램 패키지에 대한 자세한 내용은 [Batch 응용 프로그램 패키지를 사용하여 계산 노드에 응용 프로그램 배포](batch-application-packages.md)를 참조하세요.

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
