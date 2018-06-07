---
title: 앱 서비스에서 Azure 스택 업데이트 1 릴리스 정보 | Microsoft Docs
description: Azure 스택 앱 서비스에 대 한 업데이트 하나에 포함 된 내용에 대 한 자세한 내용은 알려진된 문제 및 업데이트를 다운로드 하는 위치입니다.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 80bd865b7a08d9488c0fb6a1a5b60445b9c6eaaa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Azure 스택 업데이트 1 릴리스 정보에 대 한 앱 서비스

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이러한 릴리스 정보에 개선 사항 및 Azure 앱 서비스의 수정 Azure 스택 업데이트 1과 관련 된 알려진된 문제에 설명 합니다. 알려진된 문제를 직접 배포, 업데이트 프로세스 및 문제 (설치 후) 빌드와 관련 된 문제 나뉩니다.

> [!IMPORTANT]
> Azure 스택 통합 시스템 1802 업데이트를 적용 하거나 Azure 앱 서비스를 배포 하기 전에 최신 Azure 스택 개발 키트를 배포 합니다.
>
>

## <a name="build-reference"></a>빌드 참조

Azure 스택 업데이트 1 빌드 번호에 앱 서비스는 **69.0.13698.9**

### <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> Azure 스택 앱 서비스를 Azure의 새 배포 이제 요구는 [3 주체 와일드 카드 인증서](azure-stack-app-service-before-you-get-started.md#get-certificates) 처리 하는 방식에는 SSO에 대 한 Kudu 이제 Azure 앱 서비스에서의 향상으로 인해 합니다. 새 제목은  **\*. sso.appservice.\< 지역\>.\< domainname\>.\< 확장\>**
>
>

참조는 [시작 하기 전에 설명서](azure-stack-app-service-before-you-get-started.md) 배포가 시작 되기 전에 합니다.

### <a name="new-features-and-fixes"></a>새 기능 및 수정

Azure 스택 업데이트 1에서 azure 앱 서비스는 다음과 같은 향상 된 기능 및 수정 합니다.

- **높은 가용성의 Azure 앱 서비스** -The Azure 스택 1802 사용 하도록 설정 하는 업데이트 작업에 배포할 오류 도메인입니다. 따라서 응용 프로그램 서비스 인프라는 오류 도메인 배포 될 내결함성이 있는 될 수 있습니다. 하지만 기본적으로 Azure 앱 서비스의 모든 새 배포에이 기능이 Azure 스택 1802 하기 전에 완료 하는 배포에 적용 되 고 업데이트를 참조는 [응용 프로그램 서비스 오류 도메인 설명서](azure-stack-app-service-fault-domain-update.md)

- **기존 가상 네트워크에 배포** -고객은 기존 가상 네트워크 내에서 이제 Azure 스택 앱 서비스를 배포할 수 있습니다. 기존 가상 네트워크에 배포 고객을 SQL Server 및 개인 포트를 통해 Azure 앱 서비스에 필요한 파일 서버에 연결할 수 있습니다. 그러나 기존 가상 네트워크에 배포 하도록 선택할 수 고객 배포 하는 동안 [앱 서비스에서 사용 하기 위해 서브넷 만들어야](azure-stack-app-service-before-you-get-started.md#virtual-network) 배포 하기 전에.

- 업데이트를 **응용 프로그램 서비스 테 넌 트, 관리자, 함수 포털 및 Kudu 도구**합니다. Azure 스택 포털 SDK 버전와 일치 합니다.

- **다음 응용 프로그램 프레임 워크 및 도구에 대 한 업데이트**:
    - 추가 **.NET 2.0 핵심** 지원
    - 추가 **Node.JS** 버전:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - 추가 **NPM** 버전:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 추가 **PHP** 업데이트:
        - 5.6.32
        - 7.0.26 (x86 및 x64)
        - 7.1.12 (x86 및 x64)
    - 업데이트 **Windows 용 Git** v 2.14.1로
    - 업데이트 **Mercurial** 첫 v4.5.0에

  - 에 대 한 지원을 추가 **HTTPS만** 앱 서비스 테 넌 트 포털의 사용자 지정 도메인 기능 내의 기능입니다. 

  - Azure 함수에 대 한 사용자 지정 저장소 선택에서 저장소 연결의 추가 유효성 검사 

#### <a name="fixes"></a>수정 프로그램

- 고객 액세스 거부 오류 메시지가 응용 프로그램 서비스 설치 관리자에서 폴더를 열 때 더 이상 받지 않습니다 오프 라인 배포 패키지를 만들 때

- 앱 서비스 테 넌 트 포털의 사용자 지정 도메인 기능에서 작업할 때 문제를 해결 합니다.

- 설치 하는 동안 예약 된 관리자 이름을 사용 하는 고객을 방지

- 앱 서비스 배포를 사용 하도록 설정 **도메인에 가입** 파일 서버

- Azure 스택 루트가의 향상 된 검색 스크립트에서 인증서 및 응용 프로그램 서비스 설치 관리자에서 루트 인증서 유효성을 검사 합니다.

- Microsoft.Web 네임 스페이스에 포함 된 해당 리소스를 삭제 하는 경우 구독을 Azure 리소스 관리자에 반환 되는 고정된 잘못 된 상태.

### <a name="known-issues-with-the-deployment-process"></a>배포 프로세스의 알려진된 문제

- 인증서 유효성 검사 오류

일부 고객 설치 관리자에서 지나치게 제한적인 유효성 검사로 인해 통합된 된 시스템에 배포 하는 경우 앱 서비스 설치 관리자에 인증서를 제공 하는 경우 문제가 발생 했습니다. 앱 서비스 설치 관리자를 다시 릴리스 되었습니다, 고객 해야 [업데이트 된 설치 관리자 다운로드](https://aka.ms/appsvconmasinstaller)합니다. 계속 업데이트 된 설치 관리자를 사용 하 여 인증서 유효성 검사 문제를 발생 하는 경우에 지원에 문의 합니다.

- 통합된 시스템에서 Azure 스택 루트 인증서를 검색 하는 문제입니다.

Get AzureStackRootCert.ps1의 오류로 인해 고객 루트 인증서가 설치 되지 않은 컴퓨터에서 스크립트를 실행할 때 Azure 스택 루트 인증서를 검색 하지 않아야 합니다. 스크립트를 다시 릴리스 요청 고객 및이 문제를 해결 되었습니다 이제 [업데이트 된 도우미 스크립트를 다운로드](https://aka.ms/appsvconmashelpers)합니다. 루트 인증서 업데이트 된 스크립트를 검색 하는 문제가 발생 해도 지원에 문의 합니다.

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제

- Azure 스택 업데이트 1에서 Azure 앱 서비스의 업데이트에 대 한 알려진된 문제가 없는지 합니다.

### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)

- 슬롯 전환 작동 하지 않습니다.

이 릴리스에서 사이트 슬롯 전환을 구분 합니다. 기능을 복원 하려면 다음이 단계를 완료 합니다.

1. ControllersNSG 네트워크 보안 그룹을 수정 **허용** 앱 서비스 컨트롤러 인스턴스에 원격 데스크톱 연결 합니다. AppService.local에서 응용 프로그램 서비스를 배포 하는 리소스 그룹의 이름으로 대체 합니다.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

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

2. 로 이동는 **CN0 VM** Azure 스택 관리자 포털에서 가상 컴퓨터에서 및 **연결 클릭 하 여** 컨트롤러 인스턴스 여 원격 데스크톱 세션을 엽니다. 앱 서비스 배포 중 지정 된 자격 증명을 사용 합니다.
3. 시작 **관리자 권한으로 PowerShell** 다음 스크립트를 실행 하 고

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. 원격 데스크톱 세션을 닫습니다.
5. ControllersNSG 네트워크 보안 그룹을 되돌리기 **Deny** 앱 서비스 컨트롤러 인스턴스에 원격 데스크톱 연결 합니다. AppService.local에서 응용 프로그램 서비스를 배포 하는 리소스 그룹의 이름으로 대체 합니다.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

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
- 작업자는 응용 프로그램 서비스는 기존 가상 네트워크에 배포 하 고 파일 서버 에서만 사용 가능 개인 네트워크에 파일 서버에 연결할 수 없습니다.
 
기존 가상 네트워크와 파일 서버에 연결 하는 데 내부 IP 주소에 배포 하려는 경우 추가 해야는 아웃 바운드 보안 규칙 작업자 서브넷과 파일 서버 간에 SMB 트래픽을 사용 하도록 설정 합니다. 이 작업을 수행 하려면 관리 포털에서 WorkersNsg으로 이동한 다음 속성으로 아웃 바운드 보안 규칙 추가:
 * 원본: 모든
 * 원본 포트 범위: *
 * 대상: IP 주소
 * 대상 IP 주소 범위: Ip 범위를 파일 서버에 대 한
 * 대상 포트 범위: 445
 * 프로토콜: TCP
 * 동작: 허용
 * 우선 순위: 700
 * 이름: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 Azure 운영 클라우드 관리자에 대 한 알려진된 문제

설명서에서 참조는 [Azure 스택 1802 릴리스 정보](azure-stack-update-1802.md)

## <a name="next-steps"></a>다음 단계

- Azure 앱 서비스의 개요를 참조 하십시오. [Azure 앱 서비스에 대 한 Azure 스택 개요](azure-stack-app-service-overview.md)합니다.
- Azure 스택 앱 서비스 배포를 준비 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택 앱 서비스를 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)합니다.
