---
title: Azure DevTest Labs FAQ | Microsoft 문서
description: Azure DevTest Labs에 대한 일반적인 질문에 대한 답을 찾습니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: a295cad2bf1cafce4dc64909174e9417daa7918e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs FAQ
Azure DevTest Labs에 대한 일반적인 질문에 대한 답변을 확인합니다.

**일반**
## <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
찾는 질문이 여기에 없으면 저희에게 알려주세요. 답변을 찾을 수 있도록 도와드리겠습니다.

* 이 FAQ의 끝에 질문을 게시합니다. 이 문서에 대해 Azure Cache 팀 및 다른 커뮤니티 구성원들이 참여합니다.
* 더 광범위한 대상에 도달하기 위해 [Azure DevTest Labs MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)에 질문을 게시하세요. Azure DevTest Labs 팀과 커뮤니티의 다른 구성원들이 참여합니다.
* 기능 요청의 경우 요청 내용과 아이디어를 [Azure DevTest Labs 사용자 의견](https://feedback.azure.com/forums/320373-azure-devtest-labs)으로 제출해 주세요.

## <a name="why-should-i-use-azure-devtest-labs"></a>Azure DevTest Labs를 사용해야 하는 이유는 무엇인가요?
Azure DevTest Labs를 통해 팀 시간과 비용을 절약할 수 있습니다. 개발자는 여러 다른 기준을 사용하여 자신의 고유한 환경을 만들 수 있습니다. 또한 아티팩트를 사용하여 응용 프로그램을 빠르게 배포하고 구성할 수 있습니다. 사용자 지정 이미지 및 수식을 사용하여 템플릿으로 VM(가상 머신)을 저장할 수 있으며 팀 내에서 쉽게 재현할 수 있습니다. 또한 DevTest Labs는 랩 관리자가 낭비를 줄이고 팀의 환경을 관리하도록 사용할 수 있는 몇 가지 구성 가능한 정책을 제공합니다. 이러한 정책에는 자동 종료, 비용 임계값, 사용자당 최대 VM 및 최대 VM 크기가 포함됩니다. DevTest Labs에 대한 자세한 설명은 [개요](devtest-lab-overview.md)를 읽거나 [소개 비디오](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)를 시청하세요.

## <a name="what-does-worry-free-self-service-mean"></a>"걱정할 필요가 없는 셀프 서비스"는 무슨 의미인가요?
걱정할 필요가 없는 셀프 서비스는 개발자와 테스터가 필요에 따라 자신의 고유한 환경을 만드는 것을 의미합니다. 관리자는 DevTest Labs가 낭비를 최소화하고 비용을 제어할 수 있도록 하는 것을 인식하는 보안을 갖습니다. 관리자는 허용되는 VM 크기, VM의 최대 수 및 VM의 시작 및 종료 시기를 지정할 수 있습니다. 또한 랩 리소스가 사용되는 방식을 인식하기 위해 DevTest Labs를 통해 쉽게 비용을 모니터링하고 경고를 설정합니다.

## <a name="how-can-i-use-devtest-labs"></a>DevTest Labs를 어떻게 사용할 수 있나요?
DevTest Labs는 개발 또는 테스트 환경이 필요할 때마다 그리고 신속하게 재현 또는 비용 절감 정책을 사용하여 관리하려고 하는 경우 유용합니다.

고객이 DevTest Labs를 사용하는 몇 가지 시나리오는 다음과 같습니다.

* 한 곳에서 개발 및 테스트 환경을 관리합니다. 정책을 사용하여 비용을 절감하고 사용자 지정 이미지를 만들어 팀 내에서 빌드를 공유합니다.
* 개발 단계에 걸쳐 디스크 상태를 저장하도록 사용자 지정 이미지를 사용하여 응용 프로그램을 개발합니다.
* 진행 상태와 관련하여 비용을 추적합니다.
* 품질 보증 테스트에 대한 대량 테스트 환경을 만듭니다.
* 다양한 환경에서 응용 프로그램을 쉽게 구성하고 재현하도록 아티팩트 및 수식을 사용합니다.
* 해커톤(공동 개발 또는 테스트 작업)에 대해 VM을 배포한 다음 이벤트가 종료될 때 쉽게 프로비전을 해제합니다.

## <a name="how-am-i-billed-for-devtest-labs"></a>DevTest Labs에 대한 요금은 어떻게 청구되나요?
DevTest Labs는 무료 서비스입니다. 즉 DevTest Labs에서 랩을 만들고 정책, 템플릿 및 아티팩트를 구성하는 것이 무료입니다. VM, 저장소 계정 및 가상 네트워크와 같은 랩에서 사용하는 Azure 리소스에 대해서만 비용을 지불합니다. 랩 리소스의 비용에 대한 자세한 내용은 [Azure DevTest Labs 가격 책정](https://azure.microsoft.com/pricing/details/devtest-lab/)을 참조하세요.


**보안**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>DevTest Labs의 다른 보안 수준은 무엇인가요?
보안 액세스는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/built-in-roles.md)를 통해 결정됩니다. 액세스의 작동 방식을 알아보기 위해 RBAC에 의해 정의된 대로 사용 권한, 역할 및 범위 사이의 차이점을 알아보는 데 도움을 줍니다.

* **사용 권한**: 특정 작업에 대해 정의된 액세스입니다. 예로 모든 VM에 대한 읽기 액세스가 있습니다.
* **역할**: 그룹화되고 사용자에게 할당될 수 있는 사용 권한의 집합입니다. 예를 들어 구독 소유자 역할이 있는 사용자는 구독 내의 모든 리소스에 대한 액세스를 보유합니다.
* **범위**: 범위는 Azure 리소스 계층 구조 내의 수준입니다. 예를 들어 범위는 리소스 그룹, 단일 랩이거나 전체 구독일 수 있습니다.

DevTest Labs의 범위 내에 사용자 사용 권한을 정의하는 두 가지 유형의 역할이 있습니다.

* **랩 소유자**: 랩 소유자는 랩에서 모든 리소스에 대한 액세스 권한을 보유합니다. 랩 소유자는 정책을 수정하고 VM을 읽고 쓰고, 가상 네트워크를 변경하는 등의 작업을 할 수 있습니다.
* **랩 사용자**: 랩 사용자는 VM, 정책 및 가상 네트워크와 같은 모든 랩 리소스를 볼 수 있습니다. 그러나 랩 사용자는 정책 또는 다른 사용자가 만든 모든 VM을 수정할 수 없습니다. 

DevTest Labs에서 사용자 지정 역할을 만들 수도 있습니다. DevTest Labs에서 사용자 지정 역할을 만드는 방법을 알아보려면 [특정 랩 정책에 사용자 권한 부여](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)를 참조하세요.

범위는 계층적이므로 사용자가 특정 범위에서 사용 권한을 가진 경우 범위의 모든 하위 수준 범위에서 해당 사용 권한이 자동으로 부여됩니다. 예를 들어 사용자가 구독 소유자의 역할에 할당되면 사용자는 구독의 모든 리소스에 대한 액세스를 갖게 됩니다. 이러한 리소스에는 모든 VM, 모든 가상 네트워크 및 모든 랩이 포함됩니다. 구독 소유자는 자동으로 랩 소유자의 역할을 상속합니다. 그러나 반대의 경우는 적용되지 않습니다. 랩 소유자는 구독 수준보다 낮은 범위인 랩에 대한 액세스를 가집니다. 따라서 랩 소유자는 VM, 가상 네트워크 또는 랩 외부에 있는 다른 모든 리소스를 볼 수 없습니다.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>사용자가 특정 작업을 수행하도록 허용하는 역할을 어떻게 만듭니까?
사용자 지정 역할을 만들고 사용 권한을 역할에 할당하는 방법에 대한 포괄적인 문서는 [특정 랩 정책에 사용자 권한 부여](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)를 참조하세요. 랩의 모든 VM을 시작 및 중지하는 권한이 있는 *DevTest Labs 고급 사용자* 역할을 만드는 스크립트의 예는 다음과 같습니다.

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**CI/CD 통합 및 자동화**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs는 CI/CD 도구 체인과 통합되나요?
Visual Studio Team Services를 사용하는 경우 [DevTest Labs 작업 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 사용하여 DevTest Labs에서 릴리스 파이프라인을 자동화할 수 있습니다. 이 확장으로 수행할 수 있는 작업 중 일부는 다음과 같습니다.

* VM을 만들고 자동으로 배포합니다. Azure File Copy 또는 PowerShell Team Services 작업을 사용하여 최신 빌드로 VM을 구성할 수도 있습니다.
* 추가 조사를 위해 동일한 VM에 대한 버그를 재현하는 테스트 후 VM의 상태를 자동으로 캡처합니다.
* 더 이상 필요 없는 경우 릴리스 파이프라인의 끝에서 VM을 삭제합니다.

다음 블로그 게시물은 Team Services 확장 사용에 대한 지침 및 정보를 제공합니다.

* [DevTest Labs 및 Visual Studio Team Services 확장](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Team Services에서 기존 DevTest Labs 랩에 새 VM 배포](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [DevTest Labs에 대한 연속 배포에 대해 Team Services 릴리스 관리 사용](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

다른 CI(연속 통합)/CD(지속적인 업데이트) 도구 체인의 경우 [Azure Resource Manager 템플릿](https://aka.ms/dtlquickstarttemplate)을 배포하거나 [Azure PowerShell cmdlet](../azure-resource-manager/resource-group-template-deploy.md) 및 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)를 사용하여 동일한 시나리오를 얻을 수 있습니다. 또한 [DevTest Labs용 REST API](http://aka.ms/dtlrestapis)를 사용하여 도구 체인과 통합할 수 있습니다.  


**가상 머신**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Virtual Machines 블레이드에서 DevTest Labs에서 보이는 VM을 볼 수 없는 이유는 무엇인가요?
DevTest Labs에서 VM을 만들 때 해당 VM에 액세스할 수 있는 권한이 제공됩니다. 랩 블레이드 및 **Virtual Machines** 블레이드에서 VM을 볼 수 있습니다. DevTest Labs 랩 사용자 역할에 할당된 사용자는 랩의 **모든 Virtual Machines** 블레이드의 랩에서 만들어진 모든 VM을 볼 수 있습니다. 그러나 DevTest Labs 랩 사용자 역할이 있는 사용자에게는 다른 사용자가 만든 VM 리소스에 대한 읽기 액세스 권한이 자동으로 부여되지 않습니다. 따라서 이러한 VM은 **Virtual Machines** 블레이드에 표시되지 않습니다.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>사용자 지정 이미지와 수식 간의 차이는 무엇입니까?
사용자 지정 이미지는 VHD(가상 하드 디스크)입니다. 수식은 추가 설정을 사용하여 구성한 다음 저장하고 재현할 수 있는 이미지입니다. 사용자 지정 이미지는 동일한 기본, 변경할 수 없는 이미지를 사용하여 여러 환경을 신속하게 만들려는 경우 적합할 수 있습니다. 수식은 최신 비트, 가상 네트워크 또는 서브넷의 일부 또는 특정 크기의 VM으로 구성을 재현하려는 경우 더 나을 수 있습니다. 더 자세한 설명은 [DevTest Labs에서 사용자 지정 이미지와 수식 비교](devtest-lab-comparing-vm-base-image-types.md)를 참조하세요.

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>동일한 템플릿에서 여러 VM을 한 번에 어떻게 만듭니까?
동일한 템플릿에서 여러 VM을 동시에 만들기 위한 두 가지 옵션이 있습니다.
* [Visual Studio Team Services 작업 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 사용할 수 있습니다. 
* VM을 만드는 동안 [Resource Manager 템플릿을 생성](devtest-lab-add-vm.md#save-azure-resource-manager-template)하고 [Windows PowerShell에서 Resource Manager 템플릿을 배포](../azure-resource-manager/resource-group-template-deploy.md)할 수 있습니다.

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>기존 Azure VM을 DevTest Labs 랩으로 어떻게 이동하나요?
기존 VM을 DevTest Labs에 복사하려면:

1. [Windows PowerShell 스크립트](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)를 사용하여 기존 VM의 VHD 파일을 복사합니다.
2. DevTest Labs 랩 내에 [사용자 지정 이미지를 만듭니다](devtest-lab-create-template.md).
3. 사용자 지정 이미지에서 랩에 VM을 만듭니다.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>여러 개의 디스크를 VM에 연결할 수 있습니까?
예, 여러 개의 디스크를 VM에 연결할 수 있습니다.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>테스트에 Windows OS 이미지를 사용하려면 MSDN 구독을 구매해야 하나요?
Azure에서 개발이나 테스트를 위해 Windows 클라이언트 OS 이미지(Windows 7 이상 버전)를 사용하려면 다음 중 하나를 수행해야 합니다.

- [MSDN 구독을 구입하세요](https://www.visualstudio.com/products/how-to-buy-vs).
- 기업 계약이 있는 경우 [Enterprise 개발/테스트 제품](https://azure.microsoft.com/offers/ms-azr-0148p)을 사용하여 Azure 구독을 만드세요.

각 MSDN 제품의 Azure 크레딧에 대한 자세한 내용은 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>사용자 지정 이미지를 만들도록 VHD 파일 업로드 과정을 어떻게 자동화합니까?
사용자 지정 이미지를 만들도록 VHD 파일 업로드를 자동화하기 위해 두 가지 옵션이 있습니다.

* [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage)를 사용하여 랩과 연결된 저장소 계정에 VHD 파일을 복사 또는 업로드할 수 있습니다.
* [Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용합니다. 저장소 탐색기는 Windows, OS X 및 Linux에서 실행되는 독립 실행형 앱입니다.   

랩과 연결된 대상 저장소 계정을 찾으려면:

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
3. 랩과 연결된 리소스 그룹을 찾고 선택합니다.
4. **개요** 아래에서 저장소 계정 중 하나를 선택합니다.
5. **Blob**을 선택합니다.
6. 목록에서 업로드를 찾습니다. 항목이 없으면 4단계로 돌아가서 다른 저장소 계정을 시도합니다.
7. AzCopy 명령에서 대상으로 **URL**을 사용합니다.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>랩에 있는 모든 VM을 삭제하는 프로세스를 어떻게 자동화하나요?
Azure Portal의 랩에서 VM을 삭제할 수 있습니다. 또한 PowerShell 스크립트를 사용하여 랩에서 모든 VM을 삭제할 수 있습니다. 다음 예제의 **변경할 값** 설명 아래에서 매개 변수 값을 수정하세요. Azure Portal의 랩 창에서 `subscriptionId`, `labResourceGroup` 및 `labName` 값을 검색할 수 있습니다.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**아티팩트**
## <a name="what-are-artifacts"></a>아티팩트는 무엇입니까?
아티팩트는 VM에 최신 비트 또는 개발 도구를 배포하는 데 사용할 수 있는 사용자 지정 가능한 요소입니다. VM을 만들 때 VM에 아티팩트를 연결합니다. VM이 프로비전되면 아티팩트는 VM을 배포하고 구성합니다. [공용 GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)에서 다양한 기존 아티팩트를 사용할 수 있습니다. [사용자 고유의 아티팩트를 작성](devtest-lab-artifact-author.md)할 수도 있습니다.


**랩 구성**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Resource Manager 템플릿에서 어떻게 랩을 만듭니까?
있는 그대로 배포하거나 랩에 대한 사용자 지정 템플릿을 만들기 위해 수정할 수 있는 [랩 Azure Resource Manager 템플릿의 GitHub 리포지토리](https://aka.ms/dtlquickstarttemplate)를 제공합니다. 각 템플릿에는 사용자의 Azure 구독에서 랩을 있는 그대로 배포하는 링크가 있습니다. 또는 템플릿을 사용자 지정하고 [PowerShell 또는 Azure CLI를 사용하여 배포](../azure-resource-manager/resource-group-template-deploy.md)할 수 있습니다.

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>서로 다른 리소스 그룹에서 만들어진 VM이 임의의 이름을 갖는 이유는 무엇인가요? 이름을 변경하거나 이러한 리소스 그룹을 수정할 수 있습니까?
리소스 그룹은 DevTest Labs가 사용자 권한 및 VM에 대한 액세스를 관리할 수 있도록 이 방식으로 생성됩니다. 다른 리소스 그룹으로 VM을 이동하고 원하는 이름을 사용할 수 있지만 리소스 그룹을 변경하지 않는 것이 좋습니다. 보다 유동적인 작업이 가능하도록 현재 이 환경을 개선하는 중입니다.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>동일한 구독에서 얼마나 많은 랩을 만들 수 있습니까?
구독당 만들 수 있는 랩의 수에는 특정 제한이 없습니다. 그러나 사용하는 리소스 양은 구독당 제한됩니다. [Azure 구독에 대한 한도 및 할당량](../azure-subscription-service-limits.md) 및 [이러한 한도를 늘리는 방법](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)에 대해 알아볼 수 있습니다.

## <a name="how-many-vms-can-i-create-per-lab"></a>랩당 얼마나 많은 VM을 만들 수 있습니까?
랩당 만들 수 있는 VM의 수에는 특정 제한이 없지만 하지만 사용하는 리소스(VM 코어, 공용 IP 주소 등)는 구독당 제한됩니다. [Azure 구독에 대한 한도 및 할당량](../azure-subscription-service-limits.md) 및 [이러한 한도를 늘리는 방법](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)에 대해 알아볼 수 있습니다.

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>랩에 대한 직접 링크를 공유하려면 어떻게 합니까?

1. Azure Portal에서 랩으로 이동합니다.
2. 브라우저에서 랩 URL을 복사한 다음 랩 사용자와 공유합니다.

> [!NOTE]
> 랩 사용자가 [Microsoft 계정](#what-is-a-microsoft-account)이 있지만 조직의 Active Directory 인스턴스의 구성원이 아닌 외부 사용자인 경우 공유 링크에 액세스하려고 할 때 사용자는 오류 메시지를 볼 수 있습니다. 외부 사용자가 오류 메시지를 확인하는 경우 사용자에게 먼저 Azure Portal의 오른쪽 위 모서리에서 해당 이름을 선택하도록 요청하세요. 그런 다음 메뉴의 **디렉터리** 섹션에서 사용자는 랩이 있는 디렉터리를 선택할 수 있습니다.
>
>

## <a name="what-is-a-microsoft-account"></a>Microsoft 계정이란?
Microsoft 계정이란 Microsoft 장치 및 서비스를 가지고 하는 거의 모든 것에 대해 사용하는 계정입니다. Skype, Outlook.com, OneDrive, Windows phone 및 Xbox Live에 로그인하는 데 사용하는 전자 메일 주소 및 암호입니다. 단일 계정은 파일, 사진, 연락처 및 설정이 모든 장치에서 사용자를 따를 수 있음을 의미합니다.

> [!NOTE]
> Microsoft 계정을 *Windows Live ID*라 한 적이 있습니다.
>
>


**문제 해결**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM 생성 도중 아티팩트가 실패했습니다. 어떻게 해결합니까?
실패한 아티팩트에 대한 로그를 얻는 방법을 알아보려면 [DevTest Labs에서 아티팩트 실패를 진단하는 방법](devtest-lab-troubleshoot-artifact-failure.md)을 참조하세요.

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>기존 가상 네트워크가 제대로 저장되지 않는 이유는 무엇입니까?
한 가지 가능성은 가상 네트워크 이름에 기간이 포함되어 있는 것입니다. 이 경우 기간을 제거하거나 하이픈으로 바꿔보세요. 그런 다음 가상 네트워크 저장을 다시 시도하세요.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>PowerShell에서 VM을 프로비전할 때 "부모 리소스를 찾을 수 없음" 오류가 표시되는 이유는 무엇인가요?
한 리소스가 다른 리소스의 부모이면 부모 리소스는 자식 리소스를 만들기 전에 존재해야 합니다. 부모 리소스가 존재하지 않는 경우 **ParentResourceNotFound** 메시지가 표시됩니다. 부모 리소스에 대한 종속성을 지정하지 않으면 자식 리소스는 부모보다 먼저 배포될 수 있습니다.

리소스 그룹의 랩에서 VM은 자식 리소스입니다. PowerShell을 사용하여 VM을 배포하는 데 Resource Manager 템플릿을 사용할 경우 PowerShell 스크립트에 제공된 리소스 그룹 이름은 랩의 리소스 그룹 이름이어야 합니다. 자세한 내용은 [일반적인 Azure 배포 오류 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound)을 참조하세요.

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>VM 배포에 실패하면 오류 정보를 어디에서 더 찾을 수 있나요?
VM 배포 오류는 활동 로그에 캡처됩니다. 랩 VM 활동 로그는 랩의 VM 블레이드(블레이드는 **내 가상 머신** 목록에서 VM을 선택한 후에 표시됨)에 있는 리소스 메뉴에서 **감사 로그** 또는 **가상 머신 진단** 아래에서 찾을 수 있습니다.

경우에 따라 VM 배포가 시작되기 전에 배포 오류가 발생합니다. 예는 VM을 사용하여 만든 리소스에 대한 구독 제한을 초과하는 경우입니다. 이 경우 오류 세부 정보는 랩 수준 활동 로그에 캡처됩니다. 활동 로그는 **구성 및 정책** 설정의 맨 아래에 있습니다. Azure에서 활동 로그를 사용하는 방법에 대한 자세한 내용은 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
