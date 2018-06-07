---
title: "PowerShell을 사용하여 Azure VM에 Always On 가용성 그룹 구성 | Microsoft Docs"
description: "이 자습서에서는 클래식 배포 모델로 생성된 리소스를 사용합니다. PowerShell을 사용하여 Azure에 Always On 가용성 그룹을 만듭니다."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: fe7384baa6740d316fb5a8ec7b1894f337d88080
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>PowerShell을 사용하여 Azure VM에 Always On 가용성 그룹 구성
> [!div class="op_single_selector"]
> * [클래식: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [클래식: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

시작하기 전에 이제 Azure Resource Manager 모델에서 이 작업을 완료할 수 있는지 확인하는 것이 좋습니다. 새 배포에는 Azure Resource Manager 모델을 사용하는 것이 좋습니다. [Azure Virtual Machines의 SQL Server Always On 가용성 그룹](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)을 참조하세요.

> [!IMPORTANT]
> 대부분의 새로운 배포에서는 Azure Resource Manager 모델을 사용하는 것이 좋습니다. Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다.

Azure Virtual Machines(VM)는 데이터베이스 관리자가 고가용성 SQL Server 시스템의 비용을 절감하도록 도와줍니다. 이 자습서에서는 Azure 환경 내에서 종단 간에 SQL Server Always On을 사용하여 가용성 그룹을 구현하는 방법을 보여줍니다. 자습서 마지막에서 Azure의 SQL Server Always On 솔루션은 다음 요소로 구성됩니다.

* 프런트 엔드 및 백 엔드 서브넷을 비롯한 여러 서브넷을 포함하는 가상 네트워크
* Active Directory 도메인을 포함한 도메인 컨트롤러
* 백 엔드 서브넷에 배포되고 Active Directory 도메인에 가입된 SQL Server VM 2개
* 노드 과반수 쿼럼 모델을 포함하는 3-노드 Windows 장애 조치(Failover) 클러스터
* 가용성 데이터베이스의 동기 커밋 복제본 두 개가 포함된 가용성 그룹

이 시나리오의 좋은 점은 비용 효율성이나 다른 요소가 아닌 Azure에서의 간소함입니다. 예를 들어, Azure에서 계산 시간을 줄이기 위해 2-노드 장애 조치(Failover) 클러스터에서 도메인 컨트롤러를 쿼럼 파일 공유 감시로 사용하여 두 개의 복제본 가용성 그룹에 대한 VM 수를 최소화할 수 있습니다. 이 방법을 사용하면 위의 구성에서 하나로 VM 수가 줄어듭니다.

이 자습서는 각 단계를 상세히 설명하지 않고 위에서 설명한 솔루션을 설정하는 데 필요한 단계를 보여주기 위한 것입니다. 따라서 GUI 구성 단계를 제공하는 대신 PowerShell 스크립팅을 사용하여 각 단계를 신속히 진행합니다. 이 자습서에서는 다음을 가정합니다.

* 가상 머신 구독이 포함된 Azure 계정이 이미 있습니다.
* [Azure PowerShell cmdlet](/powershell/azure/overview)이 설치되어 있습니다.
* 온-프레미스 솔루션에 대한 Always On 가용성 그룹을 확실하게 이해하고 있습니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요.

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Azure 구독에 연결하고 가상 네트워크 만들기
1. 로컬 컴퓨터의 PowerShell 창에서 Azure 모듈을 가져오고, 게시 설정 파일을 사용자 컴퓨터에 다운로드한 다음 다운로드한 게시 설정을 가져와서 PowerShell 세션을 Azure 구독에 연결합니다.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get-AzurePublishSettingsFile** 명령은 Azure에서 관리 인증서를 자동으로 생성하여 사용자 컴퓨터에 다운로드합니다. 브라우저가 자동으로 열리고 Azure 구독에 대해 Microsoft 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 다운로드한 **.publishsettings** 파일에는 Azure 구독 관리에 필요한 모든 정보가 들어 있습니다. 이 파일을 로컬 디렉터리에 저장한 후 **Import-AzurePublishSettingsFile** 명령을 사용하여 가져옵니다.

   > [!NOTE]
   > .publishsettings 파일에는 Azure 구독 및 서비스를 관리하는 데 사용되는 자격 증명(인코딩 해제)이 포함되어 있습니다. 이 파일의 보안을 유지하는 가장 좋은 방법은 소스 디렉터리 밖(예: Libraries\Documents 폴더)에 임시로 파일을 저장한 다음 가져오기가 완료되면 삭제하는 것입니다. 악의적인 사용자가 .publishsettings 파일에 액세스할 경우 Azure 서비스를 편집, 생성 및 삭제할 수 있습니다.

2. 클라우드 IT 인프라를 만드는 데 사용할 일련의 변수를 정의합니다.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    명령이 나중에 성공적으로 실행되려면 다음에 주의해야 합니다.

   * **$storageAccountName** 및 **$dcServiceName** 변수는 인터넷에서 각각 클라우드 저장소 계정 및 클라우드 서버를 식별하는 데 사용되므로 고유해야 합니다.
   * **$affinityGroupName** 및 **$virtualNetworkName** 변수에 지정하는 이름은 나중에 사용하게 될 가상 네트워크 구성 문서에서 구성됩니다.
   * **$sqlImageName** 은 SQL Server 2012 Service Pack 1 Enterprise Edition을 포함하는 VM 이미지의 업데이트된 이름을 지정합니다.
   * 간단히 하기 위해 이 자습서에서는 전체적으로 **Contoso!000**을 동일한 암호로 사용합니다.

3. 선호도 그룹을 만듭니다.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. 구성 파일을 가져와서 가상 네트워크를 만듭니다.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    구성 파일은 다음 XML 문서를 포함합니다. 간단히 말해 **ContosoAG**라는 선호도 그룹에 **ContosoNET**이라는 가상 네트워크를 지정합니다. 주소 공간은 **10.10.0.0/16**이고 서브넷 두 개 즉, **10.10.1.0/24** 및**10.10.2.0/24**있으며, 각각 프런트 서브넷 및 백 서브넷입니다. 프런트 서브넷은 Microsoft SharePoint와 같은 클라이언트 응용 프로그램을 배치하는 곳입니다. 백 서브넷은 SQL Server VM을 배치할 곳입니다. **$affinityGroupName** 및 **$virtualNetworkName** 변수를 앞에서 변경할 경우 아래의 해당하는 이름도 함께 변경해야 합니다.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. 만든 선호도 그룹과 연결되는 저장소 계정을 만들고 해당 계정을 구독에서 현재 저장소 계정으로 설정합니다.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. 새 클라우드 서비스와 가용성 집합에 도메인 컨트롤러 서버를 만듭니다.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    이러한 파이프 명령은 다음 작업을 수행합니다.

   * **New-AzureVMConfig** 는 VM 구성을 만듭니다.
   * **Add-AzureProvisioningConfig** 는 독립 실행형 Windows 서버의 구성 매개 변수를 제공합니다.
   * **Add-AzureDataDisk**는 Active Directory 데이터를 저장하는 데 사용할 데이터 디스크를 추가하며 캐싱 옵션은 None으로 설정됩니다.
   * **New-AzureVM** 은 새 클라우드 서비스를 만들고 새 클라우드 서비스에 새 Azure VM을 만듭니다.

7. 새 VM이 완전히 프로비전될 때까지 기다린 다음 작업 디렉터리에 원격 데스크톱 파일을 다운로드합니다. 새 Azure VM은 프로비전에 시간이 오래 걸리므로 새 VM이 사용 준비가 될 때까지 `while` 루프가 새 VM을 지속적으로 폴링합니다.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

이제 도메인 컨트롤러 서버가 성공적으로 프로비전되었습니다. 다음으로 이 도메인 컨트롤러 서버에 Active Directory 도메인을 구성합니다. 로컬 컴퓨터에 PowerShell 창을 열어 둡니다. 이 창은 나중에 두 SQL Server VM을 만들 때 다시 사용합니다.

## <a name="configure-the-domain-controller"></a>도메인 컨트롤러 구성
1. 원격 데스크톱 파일을 실행하여 도메인 컨트롤러 서버에 연결합니다. 새 VM을 만들 때 지정한 컴퓨터 관리자의 사용자 이름 AzureAdmin과 암호 **Contoso! 000**을 사용합니다.
2. 관리자 모드에서 PowerShell 창을 엽니다.
3. 다음 **DCPROMO.EXE** 명령을 실행하여 M 드라이브의 데이터 디렉터리로 **corp.contoso.com** 도메인을 설정합니다.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    명령이 완료되면 VM이 자동으로 다시 시작됩니다.

4. 원격 데스크톱 파일을 실행하여 도메인 컨트롤러 서버에 다시 연결합니다. 이번에는 **CORP\Administrator**로 로그인합니다.
5. 관리자 모드에서 PowerShell 창을 열고 다음 명령을 사용하여 Active Directory PowerShell 모듈을 가져옵니다.

        Import-Module ActiveDirectory

6. 다음 명령을 실행하여 도메인에 3명의 사용자를 추가합니다.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install**은 SQL Server 서비스 인스턴스, 장애 조치 클러스터, 가용성 그룹과 연관된 모든 항목을 구성하는 데 사용합니다. **CORP\SQLSvc1** 및 **CORP\SQLSvc2**는 두 SQL Server VM에 대한 SQL Server 서비스 계정으로 사용됩니다.
7. 이후 다음 명령을 실행하여 도메인에서 컴퓨터 개체를 만들 권한을 **CORP\Install**에 부여합니다.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    위에서 지정한 GUID는 컴퓨터 개체 유형의 GUID입니다. 장애 조치(Failover) 클러스터에서 Active Directory 개체를 만들기 위해 **CORP\Install** 계정에는 **모든 속성 읽기** 및 **컴퓨터 개체 만들기** 권한이 필요합니다. **모든 속성 읽기** 권한은 이미 CORP\Install에 기본적으로 부여되어 있으므로 명시적으로 부여하지 않아도 됩니다. 장애 조치(Failover) 클러스터를 만드는 데 필요한 권한에 대한 자세한 내용은 [장애 조치 클러스터 단계별 가이드: Active Directory의 계정 구성](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)을 참조하세요.

    Active Directory 및 사용자 개체 구성을 완료하면 2개의 SQL Server VM이 생성되고 이 도메인에 연결됩니다.

## <a name="create-the-sql-server-vms"></a>SQL Server VM 만들기
1. 로컬 컴퓨터에 열려 있는 PowerShell 창을 계속 사용합니다. 다음 추가 변수를 정의합니다.

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    일반적으로 Azure Virtual Network의 **10.10.0.0/16** 서브넷에 만든 최초의 VM에 IP 주소 **10.10.0.4**가 할당됩니다. **IPCONFIG**를 실행하여 이것이 도메인 컨트롤러 서버의 주소인지 확인해야 합니다.
2. 다음 파이프 명령을 실행하여 이름이 **ContosoQuorum**인 장애 조치 클러스터의 첫 번째 VM을 만듭니다.

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    위의 명령과 관련하여 다음에 유의합니다.

   * **New-AzureVMConfig** 는 원하는 가용성 집합 이름으로 VM 구성을 만듭니다. 이후의 VM은 동일한 가용성 집합에 가입할 수 있도록 같은 가용성 집합 이름으로 만들어집니다.
   * **Add-AzureProvisioningConfig**는 VM을 사용자가 만든 Active Directory 도메인에 가입시킵니다.
   * **Set-AzureSubnet**은 백 서브넷에 VM을 배치합니다.
   * **New-AzureVM** 은 새 클라우드 서비스를 만들고 새 클라우드 서비스에 새 Azure VM을 만듭니다. **DnsSettings** 매개 변수는 새 클라우드 서비스에서 서버의 DNS 서버가 IP 주소 **10.10.0.4**를 갖도록 지정합니다. 이 주소는 도메인 컨트롤러 서버의 IP 주소입니다. 이 매개 변수는 클라우드 서비스의 새 VM을 성공적으로 Active Directory 도메인에 연결하기 위해 필요합니다. 이 매개 변수가 없으면 VM이 프로비전된 후 도메인 컨트롤러 서버를 기본 DNS 서버로 사용하도록 VM에서 IPv4 설정을 수동으로 설정하고 VM을 Active Directory 도메인에 가입시켜야 합니다.
3. 다음 파이프 명령을 실행하여 이름이 **ContosoSQL1** 및 **ContosoSQL2**인 SQL Server VM을 만듭니다.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    위의 명령과 관련하여 다음에 유의합니다.

   * **New-AzureVMConfig**는 도메인 컨트롤러 서버와 동일한 가용성 집합 이름을 사용하며 가상 머신 갤러리의 SQL Server 2012 서비스 팩 1 Enterprise Edition 이미지를 사용합니다. 또한 운영 체제 디스크를 읽기 캐싱 전용(쓰기 캐싱 없음)으로 설정합니다. VM에 연결한 별도의 데이터 디스크에 데이터베이스 파일을 마이그레이션하고 읽기 또는 쓰기 캐싱 없이 구성하는 것이 좋습니다. 그러나 운영 체제 디스크에서는 읽기 캐싱을 제거할 수 없으므로 운영 체제 디스크에서 쓰기 캐싱을 제거하는 것이 차선책입니다.
   * **Add-AzureProvisioningConfig**는 VM을 사용자가 만든 Active Directory 도메인에 가입시킵니다.
   * **Set-AzureSubnet**은 백 서브넷에 VM을 배치합니다.
   * **Add-AzureEndpoint**는 클라이언트 응용 프로그램이 인터넷의 SQL Server 서비스 인스턴스에 액세스할 수 있도록 액세스 끝점을 추가합니다. ContosoSQL1 및 ContosoSQL2에 다른 포트가 제공됩니다.
   * **New-AzureVM** 은 ContosoQuorum과 동일한 클라우드 서비스에 새 SQL Server VM을 만듭니다. VM을 동일한 가용성 집합에 포함하려면 동일한 클라우드 서비스에 VM을 배치해야 해야 합니다.
4. 각 VM이 완전히 프로비전되고 각 VM이 작업 디렉터리에 원격 데스크톱 파일을 다운로드할 때까지 기다립니다. `for` 루프가 3개의 새 VM을 순환하고 각 VM에 대해 최상위 중괄호 안의 명령을 실행합니다.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    SQL Server VM이 프로비전되어 실행 중이지만 기본 옵션으로 SQL Server가 설치되어 있습니다.

## <a name="initialize-the-failover-cluster-vms"></a>장애 조치(Failover) 클러스터 VM 초기화
이 섹션에서는 장애 조치(Failover) 클러스터 및 SQL Server 설치에 사용할 3개의 서버를 수정해야 합니다. 구체적으로 살펴보면 다음과 같습니다.

* 모든 서버: **장애 조치(Failover) 클러스터링** 기능을 설치해야 합니다.
* 모든 서버: **CORP\Install**을 컴퓨터 **관리자**로 추가해야 합니다.
* ContosoSQL1 및 ContosoSQL2만 해당: 기본 데이터베이스에서 **CORP\Install**을 **sysadmin** 역할로 추가해야 합니다.
* ContosoSQL1 및 ContosoSQL2만 해당: **NT AUTHORITY\System**을 다음 권한이 있는 로그인으로 추가해야 합니다.

  * 가용성 그룹 변경
  * SQL 연결
  * 서버 상태 보기
* ContosoSQL1 및 ContosoSQL2만 해당: **TCP** 프로토콜은 이미 SQL Server VM에서 사용하도록 설정되었습니다. 그래도 SQL Server의 원격 액세스를 위해 방화벽을 열어야 합니다.

이제 시작할 준비가 되었습니다. **ContosoQuorum**부터 다음 단계를 수행합니다.

1. 원격 데스크톱 파일을 실행하여 **ContosoQuorum** 에 연결합니다. VM을 만들 때 지정한 컴퓨터 관리자의 사용자 이름 **AzureAdmin**과 암호 **Contoso!000**을 사용합니다.
2. 컴퓨터가 **corp.contoso.com**에 성공적으로 연결되어 있는지 확인합니다.
3. 계속하기 전에 SQL Server 설치가 자동 초기화 작업 실행을 마칠 때까지 기다립니다.
4. 관리자 모드에서 PowerShell 창을 엽니다.
5. Windows 장애 조치 클러스터링 기능을 설치합니다.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **CORP\Install**을 로컬 관리자로 추가합니다.

        net localgroup administrators "CORP\Install" /Add
7. ContosoQuorum에서 로그아웃합니다. 이제 이 서버에서는 작업을 마쳤습니다.

        logoff.exe

다음으로 **ContosoSQL1** 및 **ContosoSQL2**를 초기화합니다. 아래 단계를 따릅니다. 이 절차는 SQL Server VM 모두에 동일합니다.

1. 원격 데스크톱 파일을 실행하여두 SQL Server VM에 연결합니다. VM을 만들 때 지정한 컴퓨터 관리자의 사용자 이름 **AzureAdmin**과 암호 **Contoso!000**을 사용합니다.
2. 컴퓨터가 **corp.contoso.com**에 성공적으로 연결되어 있는지 확인합니다.
3. 계속하기 전에 SQL Server 설치가 자동 초기화 작업 실행을 마칠 때까지 기다립니다.
4. 관리자 모드에서 PowerShell 창을 엽니다.
5. Windows 장애 조치 클러스터링 기능을 설치합니다.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **CORP\Install**을 로컬 관리자로 추가합니다.

        net localgroup administrators "CORP\Install" /Add
7. SQL Server PowerShell 공급자를 가져옵니다.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. 기본 SQL Server 인스턴스에 sysadmin 역할로 **CORP\Install**을 추가합니다.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. 위에서 설명한 3개의 권한이 있는 로그인으로 **NT AUTHORITY\System**을 추가합니다.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. SQL Server의 원격 액세스를 위해 방화벽을 엽니다.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. 두 VM에서 로그아웃합니다.

         logoff.exe

마지막으로 가용성 그룹을 구성할 준비가 되었습니다. SQL Server PowerShell 공급자를 사용하여 **ContosoSQL1**에서 모든 작업을 수행하게 됩니다.

## <a name="configure-the-availability-group"></a>가용성 그룹 구성
1. 원격 데스크톱 파일을 실행하여 **ContosoSQL1** 에 다시 연결합니다. 컴퓨터 계정을 사용하여 로그인하는 대신 **CORP\Install**로 로그인합니다.
2. 관리자 모드에서 PowerShell 창을 엽니다.
3. 다음 변수를 정의합니다.

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. SQL Server PowerShell 공급자를 가져옵니다.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. ContosoSQL1의 SQL Server 서비스 계정을 CORP\SQLSvc1로 변경합니다.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. ContosoSQL2의 SQL Server 서비스 계정을 CORP\SQLSvc2로 변경합니다.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. **Azure VM에서 Always On 가용성 그룹을 위한 장애 조치 클러스터 만들기** 에서 [CreateAzureFailoverCluster.ps1](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) 을 로컬 작업 디렉터리로 다운로드합니다. 이 스크립트를 사용하면 작동 가능한 장애 조치(Failover) 클러스터를 만들 수 있습니다. Windows 장애 조치(Failover) 클러스터링이 Azure 네트워크와 상호 작용하는 방식에 대한 중요 정보는 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)를 참조하세요.
8. 작업 디렉터리로 변경하고 다운로드한 스크립트로 장애 조치 클러스터를 만듭니다.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. **ContosoSQL1** 및 **ContosoSQL2**의 기본 SQL Server 인스턴스에 대해 Always On 가용성 그룹을 사용하도록 설정합니다.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. 백업 디렉터리를 만들고 SQL Server 서비스 계정에 대한 권한을 부여합니다. 보조 복제본에서 가용성 데이터베이스를 준비할 때 이 디렉터리를 사용하게 됩니다.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. **ContosoSQL1**에 이름이 **MyDB1**인 데이터베이스를 만들고 전체 백업과 로그 백업을 모두 만든 다음 **WITH NORECOVERY** 옵션을 사용하여 해당 백업을 **ContosoSQL2**에 복원합니다.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. SQL Server VM에 가용성 그룹 끝점을 만들고 끝점에 적합한 권한을 설정합니다.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. 가용성 복제본을 만듭니다.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. 마지막으로, 가용성 그룹을 만들고 보조 복제본을 가용성 그룹에 연결합니다.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>다음 단계
이제 Azure에서 가용성 그룹을 만들어 SQL Server Always On을 성공적으로 구현했습니다. 이 가용성 그룹에 대한 수신기를 구성하려면 [Azure에서 Always On 가용성 그룹에 대한 ILB 수신기 구성](../classic/ps-sql-int-listener.md)을 참조하세요.

Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure Virtual Machines의 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.
