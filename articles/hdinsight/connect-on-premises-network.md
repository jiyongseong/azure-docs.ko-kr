---
title: 온-프레미스 네트워크에 HDInsight 연결 - Azure HDInsight | Microsoft Docs
description: Azure Virtual Network에서 HDInsight 클러스터를 만들고 온-프레미스 네트워크에 연결하는 방법을 알아봅니다. 사용자 지정 DNS 서버를 사용하여 HDInsight와 온-프레미스 네트워크 간에 이름 확인을 구성하는 방법을 알아봅니다.
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: 51307e1bdb31d902636787790d1c4f1248f3886b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361923"
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>온-프레미스 네트워크에 HDInsight 연결

Azure Virtual Networks와 VPN Gateway를 사용하여 HDInsight를 온-프레미스 네트워크에 연결하는 방법을 알아봅니다. 이 문서는 다음에 대한 계획 정보를 제공합니다.

* 온-프레미스 네트워크에 연결되는 Azure Virtual Network에서 HDInsight 사용.

* 가상 네트워크와 온-프레미스 네트워크 간에 DNS 이름 확인 구성.

* HDInsight에 대한 인터넷 액세스를 제한하도록 네트워크 보안 그룹 구성.

* 가상 네트워크의 HDInsight에서 제공하는 포트.

## <a name="create-the-virtual-network-configuration"></a>가상 네트워크 구성 만들기

다음 문서를 사용하여 온-프레미스 네트워크에 연결된 Azure Virtual Network를 만드는 방법을 알아봅니다.
    
* [Azure Portal 사용](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Azure PowerShell 사용](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Azure CLI 사용](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>이름 확인 구성

HDInsight 및 조인된 네트워크의 리소스를 이름별로 통신하도록 하려면 다음 작업을 수행해야 합니다.

* Azure Virtual Network에서 사용자 지정 DNS 서버 만들기

* 기본 Azure Recursive Resolver 대신 사용자 지정 DNS 서버를 사용하도록 가상 네트워크를 구성합니다.

* 사용자 지정 DNS 서버와 온-프레미스 DNS 서버 간에 전달을 구성합니다.

이 구성은 다음 동작을 사용하도록 설정합니다.

* __가상 네트워크에 대한__ DNS 접미사가 있는 정규화된 도메인 이름의 요청을 사용자 지정 DNS 서버로 전달합니다. 그런 다음 사용자 지정 DNS 서버는 IP 주소를 반환하는 Azure Recursive Resolver에 이러한 요청을 전달합니다.

* 다른 모든 요청을 온-프레미스 DNS 서버에 전달합니다. 이름 확인을 위해 Microsoft.com과 같은 인터넷 리소스에 대한 요청을 온-프레미스 DNS 서버에 전달합니다.

다음 다이어그램에서 녹색 선은 가상 네트워크의 DNS 접미사로 끝나는 리소스에 대한 요청입니다. 파란색 선은 온-프레미스 네트워크 또는 공용 인터넷에 있는 리소스에 대한 요청입니다.

![이 문서에 사용되는 구성에서 DNS 요청을 해결하는 방법의 다이어그램](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>사용자 지정 DNS 서버 만들기

> [!IMPORTANT]
> HDInsight를 가상 네트워크에 설치하기 전에 DNS 서버를 만들고 구성해야 합니다.

[Bind](https://www.isc.org/downloads/bind/) DNS 소프트웨어를 사용하는 Linux VM을 만들려면 다음 단계를 사용합니다.

> [!NOTE]
> 다음 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure Virtual Machine을 만듭니다. 가상 머신을 만드는 다른 방법은 다음 문서를 참조하세요.
>
> * [VM 만들기 - Azure CLI](../virtual-machines/linux/quick-create-cli.md)
> * [VM 만들기 - Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. [Azure Portal](https://portal.azure.com)에서 __+__, __Compute__ 및 __Ubuntu Server 16.04 LTS__를 선택합니다.

    ![Ubuntu 가상 머신 만들기](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. __기본__ 섹션에서 다음 정보를 입력합니다.

    * __이름__: 이 가상 머신을 식별하는 친숙한 이름입니다. 예: __DNSProxy__
    * __사용자 이름__: SSH 계정의 이름입니다.
    * __SSH 공개 키__ 또는 __암호__: SSH 계정에 대한 인증 방법입니다. 보다 안전한 공개 키를 사용하는 것이 좋습니다. 자세한 내용은 [Linux VM에 대한 SSH 키 만들기 및 사용](../virtual-machines/linux/mac-create-ssh-keys.md) 문서를 참조하세요.
    * __리소스 그룹__: __기존 리소스 그룹 사용__을 선택한 다음 앞에서 만든 가상 네트워크를 포함하는 리소스 그룹을 선택합니다.
    * __위치__: 가상 네트워크와 동일한 위치를 선택합니다.

    ![가상 컴퓨터 기본 구성](./media/connect-on-premises-network/vm-basics.png)

    기본 값에 다른 항목을 그대로 두고 __확인__을 선택합니다.

3. __크기 선택__ 섹션에서 VM 크기를 선택합니다. 이 자습서의 경우 가장 적고 낮은 비용 옵션을 선택합니다. 계속하려면 __선택__ 단추를 사용합니다.

4. __설정__ 섹션에서 다음 정보를 입력합니다.

    * __가상 네트워크__: 이전에 만든 가상 네트워크를 선택합니다.

    * __서브넷__: 가상 네트워크에 대한 기본 서브넷을 선택합니다. VPN Gateway에서 사용되는 서브넷을 선택하지 __않습니다__.

    * __진단 저장소 계정__: 기존 저장소 계정을 선택하거나 새 저장소 계정을 만듭니다.

    ![가상 네트워크 설정](./media/connect-on-premises-network/virtual-network-settings.png)

    기본 값에 다른 항목을 그대로 두고 __확인__을 선택하여 계속합니다.

5. __구매__ 섹션에서 __구매__ 단추를 선택하여 가상 컴퓨터를 만듭니다.

6. 가상 머신을 만들면 해당 __개요__ 섹션이 표시됩니다. 왼쪽에 있는 목록에서 __속성__을 선택합니다. __공용 IP 주소__ 및 __개인 IP 주소__ 값을 저장합니다. 이 값은 다음 섹션에서 사용됩니다.

    ![공용 및 개인 IP 주소](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Bind(DNS 소프트웨어) 설치 및 구성

1. SSH를 사용하여 가상 머신의 __공용 IP 주소__에 연결합니다. 다음 예제에서는 40.68.254.142에서 가상 머신에 연결합니다.

    ```bash
    ssh sshuser@40.68.254.142
    ```

    `sshuser`을 클러스터를 만들 때 지정한 SSH 사용자 계정으로 바꿉니다.

    > [!NOTE]
    > 다양한 방법으로 `ssh` 유틸리티를 가져올 수 있습니다. Linux, Unix 및 macOS에서 운영 체제의 일부분으로 제공됩니다. Windows를 사용하는 경우 다음 옵션 중 하나를 고려하세요.
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Windows 10에서 Ubuntu의 Bash](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git(https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH(https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Bind를 설치하려면 SSH세션에서 다음 명령을 사용합니다.

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 온-프레미스 DNS 서버에 이름 확인 요청을 전달하도록 바인딩을 구성하려면 `/etc/bind/named.conf.options` 파일의 내용에 다음과 같은 텍스트를 사용합니다.

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > `goodclients` 섹션의 값을 가상 네트워크와 온-프레미스 네트워크의 IP 주소 범위로 바꿉니다. 이 섹션에서는 이 DNS 서버가 요청을 수락하는 주소를 정의합니다.
    >
    > `forwarders` 섹션에 있는 `192.168.0.1` 항목을 온-프레미스 DNS 서버의 IP 주소로 바꿉니다. 이 항목은 확인을 위해 DNS 요청을 온-프레미스 DNS 서버에 라우팅합니다.

    이 파일을 편집하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    파일을 저장하려면 __Ctrl+X__, __Y__ 및 __Enter__ 키를 사용합니다.

4. SSH 세션에서 다음 명령을 사용합니다.

    ```bash
    hostname -f
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 텍스트는 이 가상 네트워크에 대한 __DNS 접미사__입니다. 나중에 사용하므로 이 값을 저장합니다.

5. 가상 네트워크 내에서 리소스의 DNS 이름을 확인하도록 바인딩을 구성하려면 `/etc/bind/named.conf.local` 파일의 내용에 다음과 같은 텍스트를 사용합니다.

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`을 이전에 검색한 DNS 접미사로 바꿔야 합니다.

    이 파일을 편집하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    파일을 저장하려면 __Ctrl+X__, __Y__ 및 __Enter__ 키를 사용합니다.

6. Bind를 시작하려면 다음 명령을 사용합니다.

    ```bash
    sudo service bind9 restart
    ```

7. Bind가 온-프레미스 네트워크에서 리소스의 이름을 확인할 수 있는지를 확인하려면 다음 명령을 사용합니다.

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > `dns.mynetwork.net`을 온-프레미스 네트워크에 있는 리소스의 FQDN(정규화된 도메인 이름)으로 바꿉니다.
    >
    > `10.0.0.4`를 가상 네트워크에 있는 사용자 지정 DNS 서버의 __내부 IP 주소__로 바꿉니다.

    응답은 다음 텍스트와 유사합니다.

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>사용자 지정 DNS 서버를 사용하도록 가상 네트워크 구성

Azure Recursive Resolver 대신 사용자 지정 DNS 서버를 사용하도록 가상 네트워크를 구성하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 네트워크를 선택한 다음 __DNS 서버__를 선택합니다.

2. __사용자 지정__을 선택하고 사용자 지정 DNS 서버의 __내부 IP 주소__를 입력합니다. 마지막으로 __저장__을 선택합니다.

    ![네트워크에 사용자 지정 DNS 서버 설정](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>온-프레미스 DNS 서버 구성

이전 섹션에서 온-프레미스 DNS 서버에 요청을 전달하도록 사용자 지정 DNS 서버를 구성합니다. 다음으로 사용자 지정 DNS 서버에 요청을 전달하도록 온-프레미스 DNS 서버를 구성해야 합니다.

DNS 서버를 구성하는 방법에 대한 특정 단계는 DNS 서버 소프트웨어에 대한 설명서를 참조하세요. __조건 전달자__를 구성하는 방법에 대한 단계를 확인합니다.

조건부 전달은 특정 DNS 접미사에 대한 요청만을 전달합니다. 이 경우에 가상 네트워크의 DNS 접미사에 대한 전달자를 구성해야 합니다. 사용자 지정 DNS 서버의 IP 주소에 이 접미사에 대한 요청을 전달해야 합니다. 

다음 텍스트는 **Bind** DNS 소프트웨어에 대한 조건 전달자 구성의 예제입니다.

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

**Windows Server 2016**에서 DNS를 사용하는 방법에 대한 내용은 [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) 설명서를 참조하세요...

온-프레미스 DNS 서버를 구성하면 온-프레미스 네트워크에서 `nslookup` 사용하여 가상 네트워크에서 이름을 확인할 수 있는지 확인할 수 있게 됩니다. 다음 예제 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

이 예제에서는 196.168.0.4에서 온-프레미스 DNS 서버를 사용하여 사용자 지정 DNS 서버의 이름을 확인합니다. IP 주소를 온-프레미스 DNS 서버의 IP 주소로 바꿉니다. `dnsproxy` 주소를 사용자 지정 DNS 서버의 정규화된 도메인 이름으로 바꿉니다.

## <a name="optional-control-network-traffic"></a>선택 사항: 네트워크 트래픽 제어

NSG(네트워크 보안 그룹) 또는 UDR(사용자 정의 경로)를 사용하여 네트워크 트래픽을 제어할 수 있습니다. NSG를 사용하면 인바운드 및 아웃바운드 트래픽을 필터링하고 트래픽을 허용하거나 거부할 수 있습니다. UDR을 사용하면 가상 네트워크, 인터넷 및 온-프레미스 네트워크에 있는 리소스 간의 트래픽 흐름 방식을 제어할 수 있습니다.

> [!WARNING]
> HDInsight에는 Azure 클라우드에서 특정 IP 주소의 인바운드 액세스 및 제한없는 아웃바운드 액세스가 필요합니다. NSG 또는 UDR을 사용하여 트래픽을 제어하는 경우 다음 단계를 수행해야 합니다.

1. 가상 네트워크를 포함하는 위치에 대한 IP 주소를 찾습니다. 위치별로 필요한 IP 목록은 [필요한 IP 주소](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)를 참조하세요.

2. 1단계에서 확인된 IP 주소의 경우, 해당 IP에서의 인바운드 트래픽을 허용합니다.

   * __NSG__를 사용하는 경우: IP 주소에 대해 포트 __443__의 __인바운드__ 트래픽을 허용합니다.
   * __UDR__을 사용하는 경우: __다음 홉__ 유형의 경로를 IP 주소에 대해 __인터넷__으로 설정합니다.

Azure PowerShell 또는 Azure CLI를 사용하여 NSG를 만드는 예제는 [Azure Virtual Network에서 HDInsight 확장](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) 문서를 참조하세요.

## <a name="create-the-hdinsight-cluster"></a>HDInsight 클러스터 만들기

> [!WARNING]
> HDInsight를 Virtual Network에 설치하기 전에 사용자 지정 DNS 서버를 구성해야 합니다.

[Azure Portal을 사용하여 HDInsight 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-portal.md) 문서의 단계를 사용하여 HDInsight 클러스터를 만듭니다.

> [!WARNING]
> * 클러스터를 만드는 동안 가상 네트워크를 포함하는 위치를 선택해야 합니다.
>
> * 구성의 __고급 설정__ 파트에서 이전에 만든 가상 네트워크 및 서브넷을 선택해야 합니다.

## <a name="connecting-to-hdinsight"></a>HDInsight에 연결

HDInsight에 대한 대부분의 설명서는 인터넷을 통해 클러스터에 액세스할 수 있다고 가정합니다. 예를 들어 https://CLUSTERNAME.azurehdinsight.net에 있는 클러스터에 연결할 수 있습니다. 이 주소는 인터넷에서 액세스를 제한하는 데 NSG 또는 UDR을 사용한 경우에는 사용할 수 없는 공용 게이트웨이를 사용합니다.

일부 설명서는 SSH 세션에서 클러스터에 연결하는 경우 `headnodehost`도 참조합니다. 이 주소는 클러스터 내의 노드에서만 제공되며 가상 네트워크를 통해 연결된 클라이언트에서 사용할 수 없습니다.

가상 네트워크를 통해 HDInsight에 직접 연결하려면 다음 단계를 사용합니다.

1. HDInsight 클러스터 노드의 내부 정규화된 도메인 이름을 검색하려면 다음 방법 중 하나를 사용합니다.

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. 서비스가 제공되는 포트를 확인하려면 [HDInsight의 Hadoop 서비스에서 사용하는 포트](./hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

    > [!IMPORTANT]
    > 헤드 노드에서 호스팅되는 일부 서비스는 한 번에 한 노드에서만 활성화됩니다. 한 헤드 노드에서 서비스에 액세스하려고 하고 실패하게 되면 다른 헤드 노드로 전환합니다.
    >
    > 예를 들어 Ambari는 한 번에 하나의 헤드 노드에서만 활성화됩니다. 한 헤드 노드에서 Ambari에 액세스하려고 하고 404 오류가 반환되면 다른 헤드 노드에서 실행합니다.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에서 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](./hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

* Azure 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

* 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 참조하세요.

* 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.
