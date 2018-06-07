---
title: Azure 스택 1802 업데이트 | Microsoft Docs
description: Azure 스택 1802 업데이트에 포함 된 내용에 대 한 자세한 내용은 시스템, 알려진된 문제 및 업데이트를 다운로드 위치에 통합 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: af65ffc088c2beadf415b72ec284ef77f3e4f6d4
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757260"
---
# <a name="azure-stack-1802-update"></a>Azure 스택 1802 업데이트

*적용 대상: Azure 스택 시스템 통합*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 릴리스의 경우 및 업데이트를 다운로드 하는 위치에 대 한 1802 업데이트 패키지를 수정 합니다. 알려진된 문제는 직접 업데이트 프로세스와 관련 된 문제 및 문제 빌드와 (설치 후)으로 구분 됩니다.

> [!IMPORTANT]        
> 이 업데이트 패키지는 Azure 스택 통합 시스템에만. Azure 스택 개발 키트를이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조    
Azure 스택 1802 업데이트 빌드 번호는 **20180302.1**합니다.  


## <a name="before-you-begin"></a>시작하기 전에    
> [!IMPORTANT]    
> 이 업데이트의 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md#plan-for-updates)합니다.


### <a name="prerequisites"></a>필수 조건
- Azure 스택 설치 [1712 업데이트](azure-stack-update-1712.md) Azure 스택 1802 업데이트를 적용 하기 전에.    

- 설치 **AzS 핫픽스 – 1.0.180312.1-빌드 20180222.2** Azure 스택 1802 업데이트를 적용 하기 전에. 이 핫픽스는 Windows Defender 업데이트 하 고 Azure 스택에 대 한 업데이트를 다운로드할 때 됩니다.

  에 대 한 일반적인 절차에 따라 핫픽스를 설치 하려면 [Azure 스택에 대 한 업데이트를 설치](azure-stack-apply-updates.md)합니다. 업데이트의 이름으로 표시 **AzS 핫픽스 – 1.0.180312.1**, 다음 파일을 포함 합니다. 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  이러한 파일을 저장소 계정 및 컨테이너에 업로드 한 후 관리 포털에서 업데이트 타일에서 설치를 실행 합니다. 
  
  Azure 스택 업데이트를와 달리이 업데이트를 설치 하는 Azure 스택의 버전을 변경 되지 않습니다.  이 업데이트가 설치를 확인 하려면 목록을 보려면 **설치 된 업데이트**합니다.
 


### <a name="post-update-steps"></a>업데이트 후 단계
1802 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 보려면 뿐 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다. 
- Azure 스택 핫픽스 **1.0.180302.4**합니다. [KB 4131152-기존 가상 컴퓨터 크기 집합을 사용할 수 없게 될 수 있습니다.]( https://support.microsoft.com/help/4131152) 

  이 문제를이 수정에 자세히 설명 하는 문제 해결 [KB 4103348-Azure 스택 업데이트를 설치 하려고 할 때 네트워크 컨트롤러 API 서비스 장애와](https://support.microsoft.com/help/4103348)합니다.


### <a name="new-features-and-fixes"></a>새 기능 및 수정
이 업데이트 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.

- **다음과 같은 Azure 저장소 서비스 API 버전에 대 한 지원이 추가 됩니다**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015 년 12 월 11 
    - 2015-07-08 
    
    자세한 내용은 참조 [Azure 스택 저장소: 차이점과 고려 사항을](/azure/azure-stack/user/azure-stack-acs-differences)합니다.

- **에 대 한 지원 큰 [블록 Blob](azure-stack-acs-differences.md)**:
    - 허용 가능한 최대 블록 크기는 100MB 4MB에서 증가 합니다.
    - 최대 blob 크기는 4.75 t B 195 GB에서 증가 합니다.  

- **인프라 백업** 이제 리소스 공급자 타일에 표시 되 고 백업 사용에 대 한 경고입니다. 인프라 백업 서비스에 대 한 자세한 내용은 참조 [인프라 Backup 서비스에서 Azure 스택에 대 한 백업 및 데이터 복구](azure-stack-backup-infrastructure-backup.md)합니다.

- **업데이트는 *테스트 AzureStack* cmdlet** 저장소에 대 한 진단 개선 하기 위해 합니다. 이 cmdlet에 대 한 자세한 내용은 참조 하십시오. [Azure 스택에 대 한 유효성 검사](azure-stack-diagnostic-test.md)합니다.

- **역할 기반 액세스 제어 (RBAC) 개선 사항** -위임 하려면 유니버설 사용자 그룹에 AD FS와 Azure 스택을 배포할 때 이제 RBAC를 사용할 수 있습니다. RBAC에 대 한 자세한 참조 [RBAC 관리](azure-stack-manage-permissions.md)합니다.

- **여러 오류 도메인에 대 한 지원이 추가 됩니다**합니다.  자세한 내용은 참조 [Azure 스택에 대 한 고가용성](azure-stack-key-features.md#high-availability-for-azure-stack)합니다.

- **실제 메모리 업그레이드를 지원** -초기 배포 후 이제 Azure 스택 통합 시스템의 메모리 용량을 확장할 수 있습니다. 자세한 내용은 참조 [Azure 스택에 대 한 실제 메모리 용량 관리](azure-stack-manage-storage-physical-memory-capacity.md)합니다.

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한 합니다.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제    
*1802 업데이트의 설치에 대 한 알려진된 문제가 없는지 합니다.*


### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)
빌드에 대 한 설치 후 알려진된 문제는 다음과 같은 **20180302.1**

#### <a name="portal"></a>포털
- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  해결 방법:이 업데이트를 설치한 후이 문제를 해결 하려면 사용에서 3 단계는 [트리거를 구성 하는 자동화 클레임 공급자 트러스트 Azure 스택의](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) 프로시저는 구독 소유자의 기본 공급자를 다시 설정 합니다.   

- 기능 [새 지원 요청을 드롭다운 목록에서 열려는](azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 통합된 시스템을 사용 하 여 https://aka.ms/newsupportrequest합니다.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 될 성공으로를 업데이트 하는 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 한 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 업데이트 타일 업데이트에 대 한 정보를 표시 하는 실패 합니다. 이 문제를 해결 하려면 타일 새로 고침을 클릭 합니다.

-   관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 알림이 표시 될 수 있습니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- 관리 포털 및 사용자 포털 모두에서 개요 블레이드에 이전 API 버전을 사용 하 여 만든 저장소 계정에 대 한 개요 블레이드를 선택 하면 로드에 실패 (예: 2015-06-15). 와 같은 시스템 저장소 계정이 여기에 **updateadminaccount** 패치 및 업데이트 하는 동안 사용 되는 합니다. 

  이 문제를 해결 PowerShell 실행을 사용 하는 **시작 ResourceSynchronization.ps1** 저장소 계정 세부 정보에 대 한 액세스를 복원 하는 스크립트입니다. [이 스크립트는 GitHub에서 사용할 수 있는]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), 권한 있는 끝점에 서비스 관리자 자격 증명으로 실행 해야 하 고 있습니다. 

- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

  #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  


#### <a name="marketplace"></a>Marketplace
- 사용자가 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.

#### <a name="compute"></a>컴퓨팅
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1802. This is due to the change in support for using availability sets with virtual machine scale sets. This support was added with version 1802.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message *Provisioning state failed*. 

  이 문제는 1803 버전에서 해결 되었습니다. 1802 버전에 대 한이 문제를 해결 하려면 Azure 스택 핫픽스 설치 **1.0.180302.4**합니다. 자세한 내용은 참조 [KB 4131152: 기존 가상 컴퓨터 크기 집합을 사용할 수 없게 될 수 있습니다]( https://support.microsoft.com/help/4131152)합니다. 

- Azure 스택 사용 하는 고정된 형식 Vhd만 지원 합니다. Azure 스택 마켓플레이스를 통해 제공 되는 일부 이미지 동적 Vhd를 사용 하지만 제거 된 것입니다. 가상 컴퓨터 (VM)에 연결 된 동적 디스크 크기 조정을 VM 실패 한 상태로 둡니다.

  이 문제를 해결 하려면 VM의 디스크를 저장소 계정에 VHD blob을 삭제 하지 않고 VM을 삭제 합니다. 동적 디스크에서 VHD를 고정 디스크로으로 변환 하 고 가상 컴퓨터를 다시 만듭니다.

- 가용성으로 이동 하 여 포털에서 집합을 만들 때 **새로** > **계산** > **가용성 집합**를 만들 수 있습니다는 가용성 장애 도메인과 업데이트 도메인 1으로 설정합니다. 해결 방법으로, 새 가상 컴퓨터를 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만듭니다 포털입니다.

- Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

-  VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>네트워킹
- VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.

- 부하 분산 ILB (내부) 부적절 하 게 처리 MAC 주소 백 엔드 Vm에 대 한 백 엔드 네트워크에 Linux 인스턴스를 사용 하는 경우 중단 하려면 ILB에 이르게 합니다.  ILB 백 엔드 네트워크에 Windows 인스턴스와 제대로 작동합니다.

-   그러나 포털에 IP 전달 기능을 표시할지, IP 전달을 사용 하도록 설정 해도 효과가 없습니다. 이 기능은 아직 지원 되지 않습니다.

- 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.

- Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    응용 프로그램 서비스에 대 한 해결 방법: PowerShell 사용한 네트워크 보안 그룹 내에서 보안 규칙을 수정 컨트롤러 인스턴스를 원격 데스크톱을 해야 하는 경우.  다음은 하는 방법의 예 *허용*, 다음 구성을 사용 하 여 복원 *거부*:  
    
    - *허용:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *거부:*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL 및 MySQL
 - 계속 하기 전에 중요 정보 검토 [시작 하기 전에](#before-you-begin) 앞부분의 이러한 릴리스 정보입니다.
- 사용자가 새 SQL 또는 MySQL 배포 데이터베이스를 만들 수 전에 최대 1 시간이 걸릴 수 있습니다.

- 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자가 생성 되지 않은 호스트 서버에 만든 항목 일치 하지 않는 상태가 될 수 있습니다.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

> [!NOTE]  
> Azure 스택 1802를 업데이트 한 후 이전에 배포 하는 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스는 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. Azure 스택과 마찬가지로 SQL 및 MySQL 리소스 공급자에 업데이트를 순서 대로 적용 합니다.  예를 들어 버전 1710을 사용 하는 경우 먼저 1711, 다음 1712, 버전을 적용 하 고 1802로 업데이트 합니다.      
>   
> 1802 업데이트의 설치는 현재 사용자가 SQL 또는 MySQL 리소스 공급자를 사용 하는 영향을 주지 않습니다.
> 사용 리소스 공급자의 버전에 관계 없이 데이터베이스에 사용자가 데이터 연결, 아니며에 계속 액세스할 수 합니다.    


#### <a name="app-service"></a>App Service
- 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` TLSv1.2 GitHub 리포지토리에에서 다운로드할 때 사용할 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.


## <a name="download-the-update"></a>업데이트 다운로드
Azure 스택 1802 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="more-information"></a>자세한 정보
Microsoft는 모니터링 하 고는 권한 있는 끝점 (PEP) 업데이트 1710와 함께 설치를 사용 하 여 업데이트를 다시 시작 하는 방법을 제공 합니다.

- 참조는 [업데이트 권한 있는 끝점 설명서를 사용 하 여 Azure 스택의 모니터링](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)합니다.

## <a name="see-also"></a>참고 항목

- Azure 스택에서 업데이트 관리의 개요를 참조 하십시오. [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md)합니다.
- Azure 스택을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택에서 업데이트 적용](azure-stack-apply-updates.md)합니다.
