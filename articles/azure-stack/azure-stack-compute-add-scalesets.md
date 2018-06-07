---
title: Azure 스택에서 사용할 수 있는 설정 하는 가상 컴퓨터 크기 확인 | Microsoft Docs
description: 클라우드 운영자 추가 방법은 가상 컴퓨터 크기를 Azure 스택 Marketplace로
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 12425ab53ca16bb985a0a8658b5058998565b01a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>가상 컴퓨터 크기 집합에서에서 사용할 수 있도록 Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

가상 컴퓨터 크기 집합에는 Azure 스택 계산 리소스가 됩니다. 배포 하 고 동일한 가상 컴퓨터의 집합을 관리할 사용할 수 있습니다. 모든 가상 컴퓨터와 동일 하 게 구성, 크기 집합의 가상 컴퓨터 프로 비전 미리 필요 하지 않습니다. 큰 계산, 빅 데이터 및 컨테이너 화 된 작업을 대상으로 하는 대규모 서비스를 빌드하는 것이 쉽습니다.

이 문서에서는 Azure 스택 Marketplace에 크기 집합을 사용할 수 있도록 하는 프로세스를 안내 합니다. 이 절차를 완료 한 후 사용자는 해당 구독에 가상 컴퓨터 크기 집합을 추가할 수 있습니다.

Azure 스택에 가상 컴퓨터 크기 집합은 Azure에서 가상 컴퓨터 크기 집합 같습니다. 자세한 내용은 다음 동영상을 참조 하십시오.
* [Mark Russinovich의 Azure 확장 집합 설명](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman과 Virtual Machine Scale Sets](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure 스택, 가상 컴퓨터 크기 집합에는 자동 크기 조정 지원 하지 않습니다. Azure 스택 포털, 리소스 관리자 템플릿 또는 PowerShell을 사용 하 여 설정 눈금에 더 많은 인스턴스를 추가할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* **Powershell 및 도구**

   설치 하 고 Azure 스택에 대해 구성 된 PowerShell 및 Azure 스택 도구. 참조 [스택에서 Azure PowerShell을 시작 하 고 실행](azure-stack-powershell-configure-quickstart.md)합니다.

   Azure 스택 도구를 설치한 후 다음 PowerShell 모듈을 가져올 수 있는지 확인 (기준으로 경로. AzureStack 도구 마스터 폴더에 \ComputeAdmin 폴더):
  ````PowerShell
        Import-Module .\AzureStack.ComputeAdmin.psm1
  ````

* **운영 체제 이미지**

   Azure 스택 마켓플레이스로 운영 체제 이미지를 추가 하지 않았다면, 참조 [Azure 스택 시장에 Windows Server 2016 VM 이미지를 추가](azure-stack-add-default-image.md)합니다.

   Linux 지원 Ubuntu Server 16.04를 다운로드 하 고 사용 하 여 추가 ```Add-AzsPlatformImage``` 는 다음 매개 변수: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```합니다.


## <a name="add-the-virtual-machine-scale-set"></a>가상 컴퓨터 크기 집합 추가

사용자 환경에 대해 다음 PowerShell 스크립트를 편집 하 고 가상 컴퓨터 크기 집합 Azure 스택 마켓플레이스로 추가 하도록를 실행 하십시오. 

``$User`` 관리자 포털을 연결 하는 데 사용할 계정이입니다. 예: serviceadmin@contoso.onmicrosoft.com

````PowerShell  
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
````

## <a name="update-images-in-a-virtual-machine-scale-set"></a>가상 컴퓨터 크기 집합의 이미지 업데이트 
가상 컴퓨터 크기 집합을 만든 후 사용자가 크기 집합 다시 생성 하지 않고 설정 규모에 맞게 이미지를 업데이트할 수 있습니다. 이미지를 업데이트 하는 프로세스는 다음과 같은 시나리오에 따라 달라 집니다.

1. 가상 컴퓨터 크기 집합 배포 템플릿을 **최신 지정** 에 대 한 *버전*:  

   경우는 *버전* 로 설정 되어 **최신** 에 *imageReference* 눈금에 대 한 서식 파일의 섹션 설정, 눈금 집합 사용에 대 한 작업 가장 최신 버전을 사용할 수 있는 확장 눈금에 대 한 이미지의 인스턴스를 설정합니다. 스케일 업 완료 되 면 이전 가상 컴퓨터 크기 조정 설정 인스턴스를 삭제할 수 있습니다.  (값에 대 한 *게시자*, *제공*, 및 *sku* 변경 되지 않습니다). 

   다음은 지정 하는 예로 *최신*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   수직 확장 새 이미지를 사용 하려면 먼저 해당 새 이미지를 다운로드 해야 합니다.  

   - 크기 집합의 이미지 보다 최신 버전이 때 마켓플레이스에 이미지: 이전 이미지를 대체 하는 새 이미지를 다운로드 합니다. 이미지를 교체한 후 사용자 배율을 조정할 수 있습니다. 

   - 마켓플레이스에 이미지 버전 경우 이미지 크기 집합의와 동일: 크기 집합의 사용 중인 이미지를 삭제 한 후 새 이미지를 다운로드 합니다. 원본 이미지를 제거 하 고 새 이미지를 다운로드 하는 사이의 시간 동안 수직 수 없습니다. 
      
     이 프로세스는 필요한 resyndicate 하려면 하는 이미지 1803 버전에 도입 된 스파스 파일 형식의 사용 합니다. 
 

2. 가상 컴퓨터 크기 집합 배포 템플릿을 **최신 버전을 지정 하지 않습니다** 에 대 한 *버전* 대신 버전 번호를 지정 합니다.  

     최신 버전 (사용 가능한 버전 변경)를 사용 하 여 이미지를 다운로드 하는 경우 크기 집합 확장할 수 없습니다. 이 의도적인 눈금 집합 서식 파일에 지정 된 이미지 버전을 사용할 수 있어야 합니다.  

자세한 내용은 참조 [운영 체제 디스크 및 이미지](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images)합니다.  


## <a name="remove-a-virtual-machine-scale-set"></a>가상 컴퓨터 크기 집합 제거

가상 컴퓨터를 제거 하려면 set 갤러리 항목의 크기를 조정, 다음 PowerShell 명령을 실행 합니다.

```PowerShell  
    Remove-AzsVMSSGalleryItem
````

> [!NOTE]
> 갤러리 항목은 즉시 제거할 수 없습니다. 야를 새로 고쳐야 포털 여러 번 전에 시장에서 제거할 항목을 보여 줍니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택 질문과 대답](azure-stack-faq.md)