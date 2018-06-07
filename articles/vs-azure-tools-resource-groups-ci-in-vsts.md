---
title: Azure 리소스 그룹 프로젝트를 사용하여 VS Team Services에서 연속 통합 | Microsoft Docs
description: Visual Studio에서 Azure 리소스 그룹 배포 프로젝트를 사용하여 Visual Studio Team Services에서의 연속 통합을 설정하는 방법을 설명합니다.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: fc5a45c899cd72c051dd08f7db039565a57381a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Azure 리소스 그룹 배포 프로젝트를 사용하여 Visual Studio Team Services에서 연속 통합
Azure 템플릿을 배포하려면 빌드, 테스트, Azure에 복사("준비"라고도 함), 템플릿 배포 등 다양한 단계에서 태스크를 수행합니다. VS Team Services(Visual Studio Team Services)에 템플릿을 배포하는 방법에는 두 가지가 있습니다. 두 방법 모두 결과는 같으므로 사용자의 워크플로에 가장 적합한 방법을 선택하면 됩니다.

1. Azure 리소스 그룹 배포 프로젝트(Deploy-AzureResourceGroup.ps1)에 포함된 PowerShell 스크립트를 실행하는 한 단계를 빌드 정의에 추가합니다. 이 스크립트는 아티팩트를 복사한 뒤 템플릿을 배포합니다.
2. 각각 단계 작업을 수행하는 여러 VS Team Services 빌드 단계를 추가합니다.

이 문서에서는 두 옵션 모두를 보여 줍니다. 첫 번째 옵션은 Visual Studio에서 개발자가 사용하는 것과 동일한 스크립트를 사용하고 전체 수명 주기 동안 일관성을 제공한다는 이점이 있습니다. 두 번째 옵션은 기본 제공 스크립트보다 편리한 대안을 제공합니다. 두 절차에서는 이미 VS Team Services에 Visual Studio 배포 프로젝트를 체크인했다고 가정합니다.

## <a name="copy-artifacts-to-azure"></a>아티팩트를 Azure로 복사
시나리오에 관계없이 템플릿 배포에 필요한 아티팩트가 있을 경우 해당 항목에 대한 액세스 권한을 Azure Resource Manager에 부여해야 합니다. 이러한 아티팩트에는 다음과 같은 파일이 포함될 수 있습니다.

* 중첩된 템플릿
* 구성 스크립트 및 DSC 스크립트 
* 응용 프로그램 이진 파일

### <a name="nested-templates-and-configuration-scripts"></a>중첩된 템플릿 및 구성 스크립트
Visual Studio에서 제공하는 템플릿을 사용할 경우(또는 Visual Studio 스니펫으로 빌드한 템플릿) PowerShell 스크립트가 아티팩트를 스테이징할 뿐 아니라, 다른 배포를 위해 리소스에 대한 URI를 매개 변수화합니다. 그런 다음 스크립트는 아티팩트를 Azure의 보안 컨테이너에 복사하고, 해당 컨테이너에 대한 SaS 토큰을 만든 다음 이 정보를 템플릿 배포에 전달합니다. 중첩된 템플릿에 대한 자세한 내용은 [템플릿 배포 만들기](https://msdn.microsoft.com/library/azure/dn790564.aspx) 를 참조하세요.  VS Team Services에서 태스크를 사용하는 경우 템플릿 배포에 적합한 태스크를 선택해야 하며, 필요에 따라 준비 단계의 매개 변수 값을 템플릿 배포에 전달해야 합니다.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>VS Team Services에서 연속 배포 설정
VS Team Services에서 PowerShell 스크립트를 호출하려면 빌드 정의를 업데이트해야 합니다. 이 단계는 요약하면 다음과 같습니다. 

1. 빌드 정의를 편집합니다.
2. VS Team Services에서 Azure 권한을 설정합니다.
3. Azure 리소스 그룹 배포 프로젝트에서 PowerShell 스크립트를 참조하는 Azure PowerShell 빌드 단계를 추가 합니다.
4. VS Team Services에서 빌드한 프로젝트의 작업을 위해 *-ArtifactsStagingDirectory* 매개 변수의 값을 설정합니다.

### <a name="detailed-walkthrough-for-option-1"></a>옵션1에 대한 자세한 연습
다음 절차에서는 프로젝트의 PowerShell 스크립트를 실행하는 단일 태스크를 사용하여 VS Team Services에서 연속 배포를 구성하는 데 필요한 단계를 안내합니다. 

1. VS Team Services 빌드 정의를 편집하고 Azure PowerShell 빌드 단계를 추가합니다. **빌드 정의** 범주 아래에서 빌드 정의를 선택하고 **편집** 링크를 선택합니다.
   
   ![빌드 정의 편집][0]
2. 새 **Azure PowerShell** 빌드 단계를 빌드 정의에 추가한 다음 **빌드 단계 추가...** 단추를 선택합니다. 단추를 선택합니다.
   
   ![빌드 단계 추가][1]
3. **배포 작업** 범주를 선택한 다음 **Azure PowerShell** 작업을 선택하고 해당 항목의 **추가** 버튼을 선택합니다.
   
   ![작업 추가][2]
4. **Azure PowerShell** 빌드 단계를 선택하고 해당 값을 입력합니다.
   
   1. 이미 VS Team Services에 추가된 Azure 서비스 끝점이 있는 경우 **Azure 구독** 드롭다운 목록 상자에서 구독을 선택하고 다음 섹션으로 건너뜁니다. 
      
      VS Team Services에 Azure 서비스 끝점이 없으면 추가해야 합니다. 이 하위 섹션에서는 이 프로세스를 안내합니다. Azure 계정에서 Microsoft 계정(예: Hotmail)을 사용할 경우 다음 단계를 통해 서비스 주체 인증을 가져와야 합니다.
   2. **Azure 구독** 드롭다운 목록 상자 옆에 있는 **관리** 링크를 선택합니다.
      
      ![Azure 구독 관리][3]
   3. **새 서비스 끝점** 드롭다운 목록 상자에서 **Azure**를 선택합니다.
      
      ![새 서비스 끝점][4]
   4. **Azure 구독 추가** 대화 상자에서 **서비스 사용자** 옵션을 선택합니다.
      
      ![서비스 주체 옵션][5]
   5. Azure 구독 정보를 **Azure 구독 추가** 대화 상자에 추가합니다. 다음 정보를 제공해야 합니다.
      
      * 구독 ID
      * 구독 이름
      * 서비스 주체 ID
      * 서비스 주체 키
      * 테넌트 ID
   6. **구독** 이름 상자 옆에 선택의 이름을 추가합니다. 이 값은 나중에 VS Team Services의 **Azure 구독** 드롭다운 목록에 표시됩니다. 
   7. Azure 구독 ID를 모르는 경우 다음 명령 중 하나를 사용하여 확인할 수 있습니다.
      
      PowerShell 스크립트의 경우 
      
      `Get-AzureRmSubscription`
      
      Azure CLI의 경우 
      
      `azure account show`
   8. 서비스 주체 ID, 서비스 주체 키 및 테넌트 ID를 가져오려면 [Active Directory 응용 프로그램 및 서비스 주체 만들기](resource-group-create-service-principal-portal.md) 또는 [Azure Resource Manager를 사용한 서비스 주체 인증](resource-group-authenticate-service-principal.md)의 절차에 따릅니다.
   9. 서비스 주체 ID, 서비스 주체 키 및 테넌트 ID 값을 **Azure 구독 추가** 대화 상자에 추가한 다음 **확인** 단추를 선택합니다.
      
      이제 Azure PowerShell 스크립트를 실행하는 데 사용할 유효한 서비스 주체를 만들었습니다.
5. 빌드 정의를 편집하고 **Azure PowerShell** 빌드 단계를 선택합니다. **Azure 구독** 드롭다운 목록 상자에서 구독을 선택합니다. (구독이 나타나지 않으면 **관리** 링크 옆에 있는 **새로 고침** 단추를 선택합니다.) 
   
   ![Azure PowerShell 빌드 작업 구성][8]
6. Deploy-AzureResourceGroup.ps1 PowerShell 스크립트에 대한 경로를 제공합니다. 이 작업을 수행하려면 **스크립트 경로** 상자 옆의 말줄임표(...) 버튼을 선택하고, 프로젝트의 **Scripts** 폴더에서 Deploy-AzureResourceGroup.ps1 PowerShell 스크립트로 이동한 다음 **확인** 단추를 선택합니다.    
   
   ![스크립트에 대한 경로 선택][9]
7. 스크립트를 선택한 다음 Build.StagingDirectory에서 실행되도록 스크립트 경로를 업데이트합니다( *ArtifactsLocation* 이 설정된 것과 동일한 디렉터리). "$(Build.StagingDirectory)/"를 스크립트 경로 앞에 추가하면 됩니다.
   
    ![스크립트에 대한 경로 편집][10]
8. **스크립트 인수** 상자에 다음 매개 변수를 입력합니다(한 줄로). Visual Studio에서 스크립트를 실행하면 **출력** 창에서 VS가 매개 변수를 어떻게 사용하는지 확인할 수 있습니다. 이 정보를 빌드 단계에서 매개 변수 값을 설정하기 위한 출발점으로 사용할 수 있습니다.
   
   | 매개 변수 | 설명 |
   | --- | --- |
   | -ResourceGroupLocation |리소스 그룹이 위치한 geo-location 값(예: **eastus** 또는 **'East US'**). 이름에 공백이 있을 경우 작은따옴표를 추가합니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |
   | -ResourceGroupName |이 배포에 사용되는 리소스 그룹의 이름입니다. |
   | -UploadArtifacts |이 매개 변수가 있는 경우 로컬 시스템에서 Azure에 업로드해야 하는 아티팩트를 지정합니다. 템플릿 배포에서 PowerShell 스크립트를 사용하여 스테이징하려는 추가 아티팩트가 필요한 경우에만 이 스위치를 설정합니다(예: 구성 스크립트 또는 중첩된 템플릿). |
   | -StorageAccountName |이 배포에 대한 스테이지 아티팩트에 사용되는 저장소 계정 이름. 이 매개 변수는 배포에 대한 아티팩트를 준비하는 경우에만 사용됩니다. 이 매개 변수가 제공되는 경우 이전 배포 동안 스크립트에서 저장소 계정을 만들지 않은 경우 새 저장소 계정이 만들어집니다. 매개 변수를 지정하는 경우 저장소 계정이 이미 있어야 합니다. |
   | -StorageAccountResourceGroupName |저장소 계정과 연결된 리소스 그룹의 이름. 이 매개 변수는 StorageAccountName 매개 변수에 대한 값을 제공하는 경우에만 필요합니다. |
   | -TemplateFile |Azure 리소스 그룹 배포 프로젝트의 템플릿 파일에 대한 경로. 유연성을 증대하려면 이 매개 변수에 절대 경로 대신 PowerShell 스크립트의 위치에 상대적인 경로를 사용합니다. |
   | -TemplateParametersFile |Azure 리소스 그룹 배포 프로젝트의 매개 변수 파일에 대한 경로. 유연성을 증대하려면 이 매개 변수에 절대 경로 대신 PowerShell 스크립트의 위치에 상대적인 경로를 사용합니다. |
   | -ArtifactStagingDirectory |이 매개 변수는 프로젝트의 이진 파일을 복사해 올 폴더를 PowerShell 스크립트에 알려줍니다. 이 값은 PowerShell 스크립트에서 사용하는 기본값보다 우선합니다. VS Team Services 사용을 위해 이 값을 -ArtifactStagingDirectory $(Build.StagingDirectory)로 설정합니다. |
   
   스크립트 인수 예는 다음과 같습니다(읽기 쉽도록 줄 구분).
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   완료되면 **스크립트 인수** 상자가 다음 목록과 유사하게 보입니다.
   
   ![스크립트 인수][11]
9. Azure PowerShell 빌드 단계에 모든 필요한 항목을 추가한 후 **큐** 빌드 버튼을 선택하여 프로젝트를 빌드합니다. **빌드** 화면에 PowerShell 스크립트의 출력이 표시됩니다.

### <a name="detailed-walkthrough-for-option-2"></a>옵션2에 대한 자세한 연습
다음 절차에서는 기본 제공 작업을 사용하여 VS Team Services에서 연속 배포를 구성하는 데 필요한 단계를 안내합니다.

1. VS Team Services 빌드 정의를 편집하여 두 가지 새로운 빌드 단계를 추가합니다. **빌드 정의** 범주 아래에서 빌드 정의를 선택하고 **편집** 링크를 선택합니다.
   
   ![빌드 정의 편집][12]
2. **빌드 단계 추가...** 를 사용하여 빌드 정의에 새 빌드 단계를 추가합니다. 단추를 선택합니다.
   
   ![빌드 단계 추가][13]
3. **배포** 작업 범주를 선택한 다음 **Azure File Copy** 작업을 선택하고 해당 항목의 **추가** 단추를 선택합니다.
   
   ![Azure File Copy 작업 추가][14]
4. **Azure 리소스 그룹 배포** 작업을 선택한 다음 해당 항목의 **추가** 단추를 선택한 후 **작업 카탈로그**를 **닫습니다**.
   
   ![Azure 리소스 그룹 배포 작업 추가][15]
5. **Azure 파일 복사** 작업을 선택하고 해당 값을 입력합니다.
   
   이미 VS Team Services에 추가된 Azure 서비스 끝점이 있는 경우 **Azure 구독** 드롭다운 목록 상자에서 구독을 선택합니다. 구독이 없는 경우 VS Team Services에서 구독을 설정하는 방법에 대한 지침은 [옵션 1](#detailed-walkthrough-for-option-1)을 참조하세요.
   
   * 원본 - **$(Build.StagingDirectory)** 입력
   * Azure 연결 형식 - **Azure Resource Manager** 선택
   * Azure RM 구독 - **Azure 구독** 드롭다운 목록 상자에서 사용하려는 저장소 계정에 대한 구독을 선택합니다. 구독이 나타나지 않으면 **관리** 링크 옆에 있는 **새로 고침** 단추를 선택합니다.
   * 대상 형식 - **Azure Blob** 선택
   * RM Storage 계정 - 아티팩트 준비에 사용하려는 Storage 계정 선택
   * 컨테이너 이름 - 준비에 사용하려는 컨테이너의 이름을 입력합니다. 유효한 모든 컨테이너 이름을 사용할 수 있지만 이 빌드 정의의 전용 컨테이너 이름을 사용합니다.
   
   출력 값:
   
   * Storage 컨테이너 URI - **artifactsLocation** 입력
   * 저장소 컨테이너 SAS 토큰 - **artifactsLocationSasToken** 입력
   
   ![Azure File Copy 작업 구성][16]
6. **Azure 리소스 그룹 배포** 빌드 단계를 선택하고 해당 값을 입력합니다.
   
   * Azure 연결 형식 - **Azure Resource Manager** 선택
   * Azure RM 구독 - **Azure 구독** 드롭다운 목록 상자에서 배포에 대한 구독을 선택합니다. 일반적으로 이전 단계에서 사용한 것과 동일한 구독입니다.
   * 작업 - **리소스 그룹 만들기 또는 업데이트** 선택
   * 리소스 그룹 - 리소스 그룹을 선택하거나 배포에 대한 새 리소스 그룹의 이름 입력
   * 위치 - 리소스 그룹의 위치 선택
   * 템플릿 - 앞에 **$(Build.StagingDirectory)** 를 추가하여 배포될 템플릿의 경로 및 이름 입력(예: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**)
   * 템플릿 매개 변수 - 앞에 **$(Build.StagingDirectory)** 를 추가하여 사용될 매개 변수의 경로 및 이름 입력(예: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**)
   * 템플릿 매개 변수 재정의 - 다음 코드를 입력하거나 복사하여 붙여 넣습니다.
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Azure 리소스 그룹 배포 작업 구성][17]
7. 모든 필요한 항목을 추가한 후 빌드 정의를 저장하고 위쪽에서 **새 빌드 큐 대기**를 선택합니다.

## <a name="next-steps"></a>다음 단계
Azure Resource Manager 및 Azure 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](azure-resource-manager/resource-group-overview.md) 를 참조하세요.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
