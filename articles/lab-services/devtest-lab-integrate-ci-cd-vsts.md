---
title: VSTS 지속적인 통합 및 배달 파이프라인에 Azure DevTest Labs 통합 | Microsoft Docs
description: VSTS 지속적인 통합 및 배달 파이프라인에 Azure DevTest Labs를 통합하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1af195e644fe93e0c59f5e4402dd8942f5fe1aba
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>VSTS 지속적인 통합 및 배달 파이프라인에 Azure DevTest Labs 통합
VSTS(Visual Studio Team Services)에 설치된 *Azure DevTest Labs 작업* 확장을 사용하여 CI/CD 빌드 및 릴리스 파이프라인을 Azure DevTest Labs와 손쉽게 통합할 수 있습니다. 확장은 다음과 같은 세 가지 작업을 설치합니다. 
* VM 만들기
* VM에서 사용자 지정 이미지 만들기
* VM 삭제 

이 프로세스를 통해 특정 테스트 작업에 “골든 이미지”를 신속하게 배포한 다음, 테스트가 완료되면 삭제하는 등의 작업이 쉬워집니다.

이 문서에서는 VM을 만들어 배포하고, 사용자 지정 이미지를 만든 다음, VM을 삭제하는 방법을 하나의 완전한 파이프라인으로 보여 줍니다. 일반적으로 사용자 고유의 사용자 지정 빌드-테스트-배포 파이프라인에서는 개별적으로 각 태스크를 수행합니다.

## <a name="before-you-begin"></a>시작하기 전에
CI/CD 파이프라인을 Azure DevTest Labs와 통합하기 전에 Visual Studio Marketplace에서 확장을 설치해야 합니다.
1. [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)으로 이동합니다.
1. **설치**를 선택합니다.
1. 마법사를 완료합니다.

## <a name="create-a-resource-manager-template"></a>Resource Manager 템플릿 만들기
이 섹션에서는 필요에 따라 Azure 가상 머신을 만드는 데 사용하는 Azure Resource Manager 템플릿을 만드는 방법을 설명합니다.

1. 구독에서 Resource Manager 템플릿을 만들려면 [Resource Manager 템플릿 사용](devtest-lab-use-resource-manager-template.md)에 나온 절차를 완료합니다.
1. Resource Manager 템플릿을 생성하기 전에, VM 만들기의 일부로 [WinRM 아티팩트](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)를 추가합니다.

   *Azure 파일 복사* 및 *대상 컴퓨터의 PowerShell*과 같은 배포 작업을 사용하려면 WinRM 액세스가 필요합니다.

   > [!NOTE]
   > WinRM을 공유 IP 주소와 함께 사용하는 경우 NAT 규칙을 추가하여 외부 포트를 WinRM 포트에 매핑해야 합니다. 공용 IP 주소로 VM을 만들 경우에는 이 단계가 필요하지 않습니다.
   >
   >

1. 사용자 컴퓨터에 있는 파일로 템플릿을 저장합니다. 파일의 이름을 **CreateVMTemplate.json**으로 지정합니다.
1. 소스 제어 시스템에서 템플릿을 검사합니다.
1. 텍스트 편집기를 열고 다음 스크립트를 붙여넣습니다.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. 소스 제어 시스템에서 스크립트를 검사합니다. 이름을 **GetLabVMParams.ps1**과 같이 지정합니다.

   릴리스 정의의 일부로 에이전트에서 이 스크립트를 실행할 때, *Azure 파일 복사* 또는 *대상 컴퓨터의 PowerShell*과 같은 작업 단계를 사용하는 경우 스크립트는 앱을 VM에 배포하는 데 필요한 값을 수집합니다. 일반적으로 이러한 작업을 사용하여 Azure VM에 앱을 배포합니다. VM 리소스 그룹 이름, IP 주소 및 FDQN(정규화된 도메인 이름)과 같은 값을 요구하는 작업입니다.

## <a name="create-a-release-definition-in-release-management"></a>Release Management에서 릴리스 정의 만들기
릴리스 정의를 만들려면 다음을 수행합니다.

1. **빌드 및 릴리스** 허브의 **릴리스** 탭에서 더하기 기호(+) 단추를 선택합니다.
2. **릴리스 정의 만들기** 창에서 **빈** 템플릿을 선택한 후, **다음**을 선택합니다.
3. **나중에 선택**을 선택한 후 **만들기**를 선택하여 하나의 기본 환경이 있고 연결된 아티팩트가 없는 새 릴리스 정의를 만듭니다.
4. 바로 가기 메뉴를 열려면 새 릴리스 정의에서 환경 이름 옆에 있는 줄임표(...)를 선택한 다음 **변수 구성**을 선택합니다. 
5. **구성 - 환경** 창에서 릴리스 정의 작업에 사용하는 변수에 다음 값을 입력합니다.

   a. **vmName**의 경우 Azure Portal에서 Resource Manager 템플릿을 만들 때 VM에 할당한 이름을 입력합니다.

   나. **userName**의 경우 Azure Portal에서 Resource Manager 템플릿을 만들 때 VM에 할당한 사용자 이름을 입력합니다.

   다. **password**의 경우 Azure Portal에서 Resource Manager 템플릿을 만들 때 VM에 할당한 암호를 입력합니다. 암호를 숨기고 보호하려면 “자물쇠” 아이콘을 사용합니다.

### <a name="create-a-vm"></a>VM 만들기

배포의 다음 단계는 후속 배포를 위한 “골든 이미지”로 사용할 VM을 만드는 것입니다. 이 목적을 위해 특별히 개발된 작업을 사용하여 Azure DevTest Lab 인스턴스 내에서 VM을 만듭니다. 

1. 릴리스 정의에서 **작업 추가**를 선택합니다.
2. **배포** 탭에서 *Azure DevTest Labs VM 만들기* 작업을 추가합니다. 아래와 같이 작업을 구성합니다.

   > [!NOTE]
   > 후속 배포에 사용할 VM을 만들려면 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 참조하세요.

   a. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)를 참조하세요.

   나. **랩 이름**의 경우 앞서 만든 인스턴스의 이름을 선택합니다.

   다. **템플릿 이름**의 경우 소스 코드 리포지토리에 저장한 템플릿 파일의 전체 경로 및 이름을 입력합니다. 다음 예와 같이 Release Management의 기본 제공 속성을 사용하여 경로를 간소화할 수 있습니다.

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. **템플릿 매개 변수**의 경우 템플릿에 정의된 변수에 대한 매개 변수를 입력합니다. 다음 예와 같이 환경에서 정의한 변수의 이름을 사용합니다.

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. **출력 변수 - 랩 VM ID**의 경우 후속 단계를 위해 새로 만든 VM의 ID가 필요합니다. **출력 변수** 섹션의 이 ID로 자동으로 채워지는 환경 변수의 기본 이름을 설정합니다. 필요에 따라 변수를 편집할 수 있지만 후속 작업에서 올바른 이름을 사용하도록 기억해야 합니다. 랩 VM ID는 다음과 같은 형식으로 기록됩니다.

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. 이전에 만든 스크립트를 실행하여 DevTest Labs VM의 세부 정보를 수집합니다. 
4. 릴리스 정의에서 **작업 추가**를 선택한 후 **배포** 탭에서 *Azure PowerShell* 작업을 추가합니다. 아래와 같이 작업을 구성합니다.

   > [!NOTE]
   > DevTest Labs VM의 세부 정보를 수집하려면 [배포: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell)을 참조하여 스크립트를 실행합니다.

   a. **Azure 연결 형식**의 경우 **Azure Resource Manager**를 선택합니다.

   나. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)를 참조하세요.

   다. **스크립트 형식**의 경우 **스크립트 파일**을 선택합니다.
 
   d. **스크립트 경로**의 경우 소스 코드 리포지토리에 저장한 스크립트의 전체 경로 및 이름을 입력합니다. 다음 예와 같이 Release Management의 기본 제공 속성을 사용하여 경로를 간소화할 수 있습니다.
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. **스크립트 인수**의 경우 다음 예와 같이 이전 작업에서 랩 VM의 ID로 자동으로 채워진 환경 변수의 이름을 입력합니다. 
      ```
      -labVmId '$(labVMId)'
      ```
    스크립트는 필수 값을 수집하여 이후 단계에서 쉽게 참조할 수 있도록 릴리스 정의 내 환경 변수에 이를 저장합니다.

5. 새 DevTest Labs VM에 앱을 배포합니다. 일반적으로 앱을 배포하는 데 사용하는 작업은 *Azure 파일 복사* 및 *대상 컴퓨터의 PowerShell*입니다.
   이러한 작업의 매개 변수에 필요한 VM에 대한 정보는 릴리스 정의 내 **labVmRgName**, **labVMIpAddress** 및 **labVMFqdn**이라는 세 가지 구성 변수에 저장됩니다. 앱을 배포하지 않고 DevTest Labs VM 및 사용자 지정 이미지를 만들어 시험하려는 경우 이 단계를 건너뛸 수 있습니다.

### <a name="create-an-image"></a>이미지 만들기

다음 단계는 Azure DevTest Labs 인스턴스에 새로 배포된 VM의 이미지를 만드는 것입니다. 그런 다음, 개발 작업을 실행하거나 일부 테스트를 실행하고자 할 때마다 필요에 따라 이미지를 사용하여 VM의 복사본을 만듭니다. 

1. 릴리스 정의에서 **작업 추가**를 선택합니다.
2. **배포** 탭에서 **Azure DevTest Labs 사용자 지정 이미지 만들기** 작업을 추가합니다. 다음과 같이 구성합니다.

   > [!NOTE]
   > 이미지를 만들려면 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 참조하세요.

   a. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)를 참조하세요.

   나. **랩 이름**의 경우 앞서 만든 인스턴스의 이름을 선택합니다.

   다. **사용자 지정 이미지 이름**의 경우 사용자 지정 이미지에 대한 이름을 입력합니다.

   d. (선택 사항) **설명**의 경우 나중에 올바른 이미지를 쉽게 선택할 수 있도록 설명을 입력합니다.

   e. **원본 랩 VM - 원본 랩 VM ID**의 경우 이전 작업에서 랩 VM의 ID로 자동으로 채워진 환경 변수의 기본 이름을 변경했으면 여기에서 편집합니다. 기본값은 **$(labVMId)** 입니다.

   f. **출력 변수 - 사용자 지정 이미지 ID**의 경우 관리하거나 삭제하려고 할 때 새로 만든 이미지의 ID가 필요합니다. 이 ID로 자동으로 채워진 환경 변수의 기본 이름은 **출력 변수** 섹션에 설정됩니다. 필요한 경우 변수를 편집할 수 있습니다.

### <a name="delete-the-vm"></a>VM 삭제

최종 단계는 Azure DevTest Labs 인스턴스에 배포된 VM을 삭제하는 것입니다. 일반적으로 배포된 VM에서 필요한 개발 작업을 실행하거나 테스트를 실행한 후 VM을 삭제합니다. 

1. 릴리스 정의에서 **작업 추가**를 선택한 후 **배포** 탭에서 *Azure DevTest Labs VM 삭제* 작업을 추가합니다. 다음과 같이 구성합니다.

      > [!NOTE]
      > VM을 삭제하려면 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 참조하세요.

   a. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)를 참조하세요.
 
   나. **랩 VM ID**의 경우 이전 작업에서 랩 VM의 ID로 자동으로 채워진 환경 변수의 기본 이름을 변경했으면 여기에서 편집합니다. 기본값은 **$(labVMId)** 입니다.

2. 릴리스 정의 대한 이름을 입력한 다음, 저장합니다.
3. 새 릴리스를 만들고, 최신 빌드를 선택하고, 정의의 단일 환경에 배포합니다.

각 단계에서 Azure Portal의 DevTest Labs 인스턴스 보기를 새로 고치면 생성 중인 VM 및 이미지와 더불어 삭제 중인 VM을 다시 볼 수 있습니다.

이제 사용자 지정 이미지를 사용하여 필요한 경우 VM을 만들 수 있습니다.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
* [Resource Manager 템플릿으로 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md) 방법을 알아봅니다.
* [공용 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 DevTest Labs 자동화를 위한 더 많은 빠른 시작 Resource Manager 템플릿을 살펴봅니다.
* 필요한 경우 [VSTS 문제 해결](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) 페이지를 참조하세요.
 
