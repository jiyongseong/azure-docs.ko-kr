---
title: Azure SQL Database 관리되는 인스턴스 사용자 지정 DNS | Microsoft Docs
description: 이 항목에서는 Azure SQL Database 관리되는 인스턴스를 사용하는 사용자 지정 DNS의 구성 옵션에 대해 설명합니다.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 3175b99c0e41cedf313115043b09608496adfdca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database 관리되는 인스턴스에 대한 사용자 지정 DNS 구성

Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에서 Azure SQL Database 관리되는 인스턴스(미리 보기)를 배포해야 합니다. 개인 호스트 이름이 관리되는 인스턴스에서 해결되어야 하는 클라우드 또는 하이브리드 환경의 다른 SQL 인스턴스에 연결된 서버에 대한 몇 가지 시나리오가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 관리되는 인스턴스가 내부 작업에서 동일한 DNS를 사용하므로 가상 네트워크 DNS 구성은 관리되는 인스턴스와 호환되어야 합니다. 

사용자 지정 DNS 구성이 관리되는 인스턴스와 호환되도록 하려면 다음 단계를 완료해야 합니다. 
- Azure DNS에 요청을 전달하도록 사용자 지정 DNS 구성 
- VNet에서 사용자 지정 DNS를 주 데이터베이스로 설정 및 Azure DNS를 보조 데이터베이스로 설정 
- 사용자 지정 DNS를 주 데이터베이스로 등록 및 Azure DNS를 보조 데이터베이스로 등록

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Azure DNS에 요청을 전달하도록 사용자 지정 DNS 구성 

Windows Server 2016에서 DNS 전달을 구성하려면 다음과 같은 단계를 사용합니다. 

1. **서버 관리자**에서 **도구**를 클릭한 다음, **DNS**를 클릭합니다. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. **전달자**를 두 번 클릭합니다.

   ![전달자](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. **편집**을 클릭합니다. 

   ![전달자 목록](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Azure의 재귀 해결자 IP 주소(예: 168.63.129.16)를 입력합니다.

   ![재귀 해결자 IP 주소](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>사용자 지정 DNS를 주 데이터베이스로 설정 및 Azure DNS를 보조 데이터베이스로 설정 
 
Azure VNet의 DNS 구성에서는 IP 주소를 입력해야 하므로 다음 단계를 사용하여 고정 IP 주소를 갖는 DNS 서버를 호스팅하는 Azure VM을 구성합니다. 

1. Azure Portal에서 사용자 지정 DNS VM 네트워크 인터페이스를 엽니다.

   ![network-interface](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. IP 구성 섹션에서 IP 구성을 선택합니다. 

   ![IP 구성](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. 개인 IP 주소를 고정으로 설정합니다. IP 주소를 적어둡니다(이 스크린샷의 경우 10.0.1.5). 

   ![정적](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>사용자 지정 DNS를 주 데이터베이스로 등록 및 Azure DNS를 보조 데이터베이스로 등록 

1. Azure Portal에서 VNet에 대한 사용자 지정 DNS 옵션을 찾습니다.

   ![사용자 지정 DNS 옵션](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. 사용자 지정으로 전환하고 Azure의 재귀 해결자 IP 주소뿐만 아니라 사용자 지정 DNS 서버 IP 주소를 입력합니다(예: 168.63.129.16). 

   ![사용자 지정 DNS 옵션](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > DNS 목록에서 Azure 재귀 해결자를 설정하지 않으면 관리되는 인스턴스가 잘못된 상태로 전환됩니다. 해당 상태에서 복구하려면 네트워킹 준수 정책을 사용하여 VNet에서 새 인스턴스를 만들고, 인스턴스 수준 데이터를 만들고, 데이터베이스를 복원해야 합니다. [VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 개요는 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- 관리되는 인스턴스를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)를 참조하세요.
- 관리되는 인스턴스에 대해 VNet을 구성하는 방법에 대한 내용은 [관리되는 인스턴스의 VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.
