---
title: Azure 인스턴스 수준 공용 IP(클래식) 주소 | Microsoft Docs
description: ILPIP(인스턴스 수준 공용 IP) 주소 및 PowerShell을 사용하여 이를 관리하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 4b4350e6b1616450ce45f9e947cc3b639a341ae7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="instance-level-public-ip-classic-overview"></a>인스턴스 수준 공용 Ip(클래식) 개요
ILPIP(인스턴스 수준 공용 IP)는 해당 VM 또는 역할 인스턴스가 상주하는 클라우드 서비스가 아닌 VM 또는 Cloud Services 역할 인스턴스에 직접 할당할 수 있는 공용 IP 주소입니다. ILPIP는 클라우드 서비스에 할당된 VIP(가상 IP)의 위치를 차지하지 않습니다. VM 또는 역할 인스턴스에 직접 연결을 사용할 수 있는 추가 IP 주소입니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. Resource Manager를 통해 VM을 만드는 것이 좋습니다. Azure에서 [IP 주소](virtual-network-ip-addresses-overview-classic.md) 가 어떻게 작동하는지 이해해야 합니다.

![ILPIP 및 VIP 간의 차이](./media/virtual-networks-instance-level-public-ip/Figure1.png)

그림 1에 표시된 것과 같이, 클라우드 서비스는 VIP를 사용하여 액세스하지만 개별 VM은 보통 VIP:&lt;포트 번호&gt;를 사용하여 액세스됩니다. 특정 VM에 ILPIP를 할당하면 해당 VM은 해당 IP 주소를 사용하여 직접 액세스할 수 있습니다.

Azure에서 클라우드 서비스를 만들면 해당 DNS A 레코드가 자동으로 만들어져, 실제 VIP를 사용하지 않고 정규화된 도메인 이름(FQDN)을 통해 서비스에 액세스할 수 있습니다. 동일한 프로세스가 ILPIP에 대해 발생하며 ILPIP 대신 FQDN으로 VM 또는 역할 인스턴스에 액세스할 수 있습니다. 예를 들어, *contosoadservice*라는 클라우드 서비스를 만들고 두 인스턴스가 있는 *contosoweb*이라는 웹 역할을 구성한 경우, Azure는 인스턴스에 대해 다음 A 레코드를 등록합니다.

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> 각 VM 또는 역할 인스턴스에 대해 하나의 ILPIP를 할당할 수 있습니다. 구독당 최대 5개의 ILPIP를 사용할 수 있습니다. 다중 NIC VM에는 ILPIP가 지원되지 않습니다.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>ILPIP를 요청하는 이유
클라우드 서비스 VIP:&lt;포트 번호&gt;를 사용하지 않고 직접 할당된 IP 주소로 VM 또는 역할 인스턴스에 연결할 수 있게 하려면 VM 또는 역할 인스턴스에 대한 ILPIP를 요청합니다.

* **활성 FTP** - VM에 ILPIP를 할당하면 어떤 포트에서도 트래픽을 수신할 수 있습니다. 끝점이 없어도 VM에서 트래픽을 수신할 수 있습니다.  FTP 프로토콜에 대한 자세한 내용은 (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[FTP 프로토콜 개요]를 참조하세요.
* **아웃바운드 IP** - VM에서 발생하는 아웃바운드 트래픽은 원본으로 ILPIP에 매핑되며 ILPIP는 외부 엔터티에 대한 VM을 고유하게 식별합니다.

> [!NOTE]
> 과거에는 ILPIP 주소를 PIP(공용 IP) 주소라고 했습니다.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>VM에 대한 ILPIP 관리
다음 작업을 통해 VM에서 ILPIP를 생성, 할당 및 제거할 수 있습니다.

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>PowerShell을 사용하여 VM 생성 중 ILPIP를 요청하는 방법
다음 PowerShell 스크립트는 *FTPService*라는 클라우드 서비스를 만들고, Azure에서 이미지를 검색하고, 검색된 이미지를 사용하여 *FTPInstance*라는 VM을 만들고, ILPIP를 사용하도록 VM을 설정하고, VM을 새 서비스에 추가합니다.

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>VM에 대한 ILPIP 정보를 검색하는 방법
위의 스크립트를 사용하여 만든 VM에 대한 ILPIP 정보를 보려면, 다음 PowerShell 명령을 실행하고 *PublicIPAddress* 및 *PublicIPName*의 값을 확인합니다.

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

예상 출력:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>VM에서 ILPIP를 제거하는 방법
위의 스크립트에서 VM에 추가된 ILPIP를 제거하려면 다음 PowerShell 명령을 실행합니다.

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>기존 VM에 ILPIP를 추가하는 방법
위의 스크립트를 사용하여 만든 VM에 ILPIP를 추가하려면 다음 명령을 실행합니다.

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Cloud Services 역할 인스턴스에 대한 ILPIP 관리

Cloud Services 역할 인스턴스에 ILPIP를 추가하려면 다음 단계를 완료합니다.

1. [Cloud Services를 구성하는 방법](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) 문서의 단계를 완료하여 클라우드 서비스에 대한 .cscfg 파일을 다운로드합니다.
2. `InstanceAddress` 요소를 추가하여 .cscfg 파일을 업데이트합니다. 다음 샘플에서는 *WebRole1* 역할 인스턴스에 *MyPublicIP*라는 ILPIP를 추가합니다. 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. [Cloud Services를 구성하는 방법](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) 문서의 단계를 완료하여 클라우드 서비스에 대한 .cscfg 파일을 업로드합니다.

## <a name="next-steps"></a>다음 단계
* 클래식 배포 모델에서 [IP 주소 지정](virtual-network-ip-addresses-overview-classic.md) 이 어떻게 작동하는지 이해합니다.
* [예약된 IP](virtual-networks-reserved-public-ip.md)에 대해 자세히 알아봅니다.
