---
title: Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버 관리 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure에 대한 VMware 재해 복구를 위해 기존 구성 서버를 관리하는 방법을 설명합니다.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b5ba316b21e0c31e0ecc99fc2d57f81b0f24c086
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware VM에 대해 구성 서버 관리

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 설정합니다. 구성 서버는 온-프레미스 VMware 및 Azure 간의 통신을 조정하고 데이터 복제를 관리합니다. 이 문서에서는 배포된 후에 구성 서버를 관리하기 위한 일반 태스크를 요약합니다.


## <a name="modify-vmware-settings"></a>VMware 설정 수정

구성 서버가 연결하는 VMware 서버에 대한 설정을 수정합니다.

1. 구성 서버를 실행하는 컴퓨터에 로그인합니다.
2. 바탕 화면 바로 가기에서 Azure Site Recovery 구성 관리자를 시작합니다. 또는 [이 링크](https://configuration-server-name/IP:44315)를 엽니다.
3. **vCenter 서버/vSPhere ESXi 서버**를 선택하고 다음을 수행합니다.

    * 다른 VMware 서버를 구성 서버에 연결하려면 **vCenter 서버/vSphere ESXi 서버 추가**를 선택합니다. 서버 정보를 입력합니다.

    * VMware VM의 자동 검색을 위해 VMware 서버에 연결하는 데 사용되는 자격 증명을 업데이트하려면 **편집**을 선택합니다. 새 자격 증명을 입력하고 **확인**을 선택합니다.

    ![VMware 수정](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Mobility Service 설치에 대한 자격 증명 수정

복제에 대해 사용되는 VMware VM에 Mobility Service를 자동으로 설치하는 데 사용되는 자격 증명을 수정합니다.

1. 구성 서버를 실행하는 컴퓨터에 로그인합니다.
2. 바탕 화면 바로 가기에서 Site Recovery 구성 관리자를 시작합니다. 또는 [이 링크](https://configuration-server-name/IP:44315)를 엽니다.
3. **가상 머신 자격 증명 관리**를 선택하고 새 자격 증명을 입력합니다. 그런 후 **확인**을 선택하여 설정을 업데이트합니다.

    ![Mobility Service 자격 증명 수정](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>프록시 설정 수정

Azure에 대한 인터넷 액세스를 위해 구성 서버 컴퓨터에서 사용하는 프록시 설정을 수정합니다. 구성 서버 컴퓨터에서 실행 중인 기본 프로세스 서버 외에도, 프로세스 서버 컴퓨터가 있는 경우 두 컴퓨터의 설정을 수정합니다.

1. 구성 서버를 실행하는 컴퓨터에 로그인합니다.
2. 바탕 화면 바로 가기에서 Site Recovery 구성 관리자를 시작합니다. 또는 [이 링크](https://configuration-server-name/IP:44315)를 엽니다.
3. **연결 관리**를 선택하고 프록시 값을 업데이트합니다. 그런 후 **저장**을 선택하여 설정을 업데이트합니다.

## <a name="add-a-network-adapter"></a>네트워크 어댑터 추가

OVF(Open Virtualization Format) 템플릿은 단일 네트워크 어댑터를 사용하여 구성 서버 VM을 배포합니다. [VM에 추가 어댑터를 추가](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)할 수 있으나 자격 증명 모음에 구성 서버를 등록하기 전에 추가해야 합니다.

구성 서버를 자격 증명 모음에 등록한 후 어댑터를 추가하려면 VM 속성에서 어댑터를 추가합니다. 그런 후 자격 증명 모음에 서버를 다시 등록합니다.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>동일한 자격 증명 모음에 구성 서버 다시 등록

필요한 경우 동일한 자격 증명 모음에 구성 서버를 다시 등록할 수 있습니다. 구성 서버 컴퓨터에서 실행 중인 기본 프로세스 서버 외에도, 추가 프로세스 서버 컴퓨터가 있는 경우 두 컴퓨터를 다시 등록합니다.


  1. 자격 증명 모음에서 **관리** > **Site Recovery 인프라** > **구성 서버**를 엽니다.
  2. **서버**에서 **등록 키 다운로드**를 선택하여 자격 증명 모음 자격 증명 파일을 다운로드합니다.
  3. 구성 서버 컴퓨터에 로그인합니다.
  4. **%ProgramData%\ASR\home\svagent\bin**에서 **cspsconfigtool.exe**를 엽니다.
  5. **자격 증명 모음 등록** 탭에서 **찾아보기**를 선택하고 다운로드한 자격 증명 모음 자격 증명 파일을 찾습니다.
  6. 필요한 경우 프록시 서버 세부 정보를 제공합니다. 그런 다음 **등록**을 선택합니다.
  7. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>구성 서버 업그레이드

구성 서버를 업데이트하려면 업데이트 롤업을 실행합니다. 업데이트는 N-4 버전까지 적용할 수 있습니다. 예: 

- 9.7, 9.8, 9.9 또는 9.10을 실행 중인 경우 9.11로 바로 업그레이드할 수 있습니다.
- 9.6 이하를 실행 중이고 9.11로 업그레이드하려는 경우 먼저 9.7 버전으로 업그레이드한 후 9.11로 업그레이드해야 합니다.

모든 버전의 구성 서버로 업그레이드할 수 있는 업데이트 롤업 링크가 [wiki 업데이트 페이지](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)에 제공됩니다.

다음과 같이 서버를 업그레이드합니다.

1. 업데이트 설치 관리자 파일을 구성 서버에 다운로드합니다.
2. 두 번 클릭하여 설치 관리자를 실행합니다.
3. 설치 관리자는 컴퓨터에서 실행 중인 현재 버전을 검색합니다.
4. **확인**을 선택하여 확인하고 업그레이드를 실행합니다. 


## <a name="delete-or-unregister-a-configuration-server"></a>구성 서버 삭제 또는 등록 취소

1. 구성 서버의 모든 VM에 대해 [보호 사용 안 함](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)을 사용하지 않도록 설정합니다.
2. 구성 서버에서 모든 복제 정책을 [연결 해제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) 및 [삭제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)합니다.
3. 구성 서버에 연결된 모든 vCenter 서버/vSphere 호스트를 [삭제](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)합니다.
4. 자격 증명 모음에서 **Site Recovery 인프라** > **구성 서버**를 엽니다.
5. 제거하려는 구성 서버를 선택합니다. 그런 다음, **세부 정보** 페이지에서 **삭제**를 선택합니다.

    ![구성 서버 삭제](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Powershell로 관리

필요에 따라 PowerShell을 사용하여 구성 서버를 삭제할 수 있습니다.

1. Azure PowerShell 모듈을 [설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0)합니다.
2. 다음 명령을 사용하여 Azure 계정에 로그인합니다.
    
    `Connect-AzureRmAccount`
3. 자격 증명 모음 구독을 선택합니다.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  자격 증명 모음 컨텍스트를 설정합니다.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 구성 서버를 검색합니다.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 구성 서버를 삭제합니다.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> 구성 서버의 강제 삭제를 위해서는 Remove-AzureRmSiteRecoveryFabric에 **-Force** 옵션을 사용할 수 있습니다.
 


## <a name="renew-ssl-certificates"></a>SSL 인증서 갱신

구성 서버에는 기본 제공 웹 서버가 있습니다. 이 서버는 Mobility Service, 프로세스 서버 및 마스터 대상 서버의 작업을 오케스트레이션합니다. 웹 서버는 SSL 인증서를 사용하여 클라이언트를 인증합니다. 인증서는 3년 후에 만료되며 언제든지 갱신할 수 있습니다.

### <a name="check-expiry"></a>만료 확인

2016년 5월 이전의 구성 서버 배포의 경우, 인증서 만료가 1년으로 설정되었습니다. 인증서가 곧 만료될 예정인 경우 다음이 발생합니다.

- 만료 날짜가 2개월 이내로 남은 경우 서비스는 포털을 통해 및 전자 메일로 알림을 전송하기 시작합니다(Site Recovery 알림을 구독한 경우).
- 알림 배너가 자격 증명 모음 리소스 페이지에 나타납니다. 자세한 내용은 배너를 선택합니다.
- **지금 업그레이드** 단추가 표시되면 작업 환경의 일부 구성 요소가 아직 9.4.xxxx.x 이상 버전으로 업그레이드되지 않았음을 의미합니다. 인증서를 갱신하기 전에 구성 요소를 업그레이드합니다. 이전 버전에서는 갱신할 수 없습니다.

### <a name="renew-the-certificate"></a>인증서 갱신

1. 자격 증명 모음에서 **Site Recovery 인프라** > **구성 서버**를 엽니다. 원하는 구성 서버를 선택합니다.
2. 만료 날짜가 **구성 서버 상태** 아래에 표시됩니다.
3. **인증서 갱신**을 선택합니다. 


## <a name="next-steps"></a>다음 단계

Azure에 대한 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정하기 위한 자습서를 검토하세요.
