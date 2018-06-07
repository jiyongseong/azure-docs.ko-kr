---
title: Azure SQL Database 관리되는 인스턴스 VNet 구성 | Microsoft Docs
description: 이 항목에서는 Azure SQL Database 관리되는 인스턴스를 사용하는 VNet(가상 네트워크)의 구성 옵션에 대해 설명합니다.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: bccc2d52c592101a23338e57791a992441e80047
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Azure SQL Database 관리되는 인스턴스에 대한 VNet 구성

Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에서 Azure SQL Database 관리되는 인스턴스(미리 보기)를 배포해야 합니다. 이 배포를 사용하면 다음과 같은 시나리오를 수행할 수 있습니다. 
- 직접 관리되는 인스턴스에 연결하면 온-프레미스 네트워크를 형성합니다. 
- 연결된 서버 또는 다른 온-프레미스 데이터 저장소에 관리되는 인스턴스 연결 
- Azure 리소스에 관리되는 인스턴스 연결  

## <a name="plan"></a>계획

다음과 같은 질문에 대한 답변을 사용하여 가상 네트워크에서 관리되는 인스턴스를 배포하는 방법을 계획합니다. 
- 관리되는 단일 또는 여러 인스턴스를 배포할 계획인가요? 

  관리되는 인스턴스 수는 관리되는 인스턴스에 할당할 서브넷의 최소 크기를 결정합니다. 자세한 내용은 [관리되는 인스턴스의 서브넷 크기 결정](#create-a-new-virtual-network-for-managed-instances)을 참조하세요. 
- 관리되는 인스턴스를 기존 가상 네트워크에 배포해야 하거나 새 네트워크를 만들려고 하나요? 

   기존 가상 네트워크를 사용하려는 경우 관리되는 인스턴스를 수용하도록 해당 네트워크 구성을 수정해야 합니다. 자세한 내용은 [관리되는 인스턴스의 기존 가상 네트워크 수정](#modify-an-existing-virtual-network-for-managed-instances)을 참조하세요. 

   새 가상 네트워크를 만들려는 경우 [관리되는 인스턴스의 새 가상 네트워크 만들기](#create-a-new-virtual-network-for-managed-instances)를 참조하세요.

## <a name="requirements"></a>요구 사항

관리되는 인스턴스를 만들기 위해 다음 요구 사항을 준수하는 VNet 내에 있는 전용 서브넷이 필요합니다.
- **비워 두기**: 서브넷은 연결된 다른 클라우드 서비스를 포함하지 않아야 하고 게이트웨이 서브넷이 아니어야 합니다. 관리되는 인스턴스 이외의 리소스를 포함하는 서브넷에서 관리되는 인스턴스를 만들거나 나중에 서브넷 내에 다른 리소스를 추가할 수 없습니다.
- **NSG 없음**: 서브넷에는 연결된 네트워크 보안 그룹이 없어야 합니다.
- **특정 경로 표 포함**: 서브넷에는 할당된 유일한 경로로 0.0.0.0/0 다음 홉 인터넷을 사용하는 사용자 경로 테이블(UDR)이 있어야 합니다. 자세한 내용은 [필수 경로 테이블 만들기 및 연결](#create-the-required-route-table-and-associate-it)을 참조하세요.
3. **선택적 사용자 지정 DNS**: VNet에 사용자 지정 DNS을 지정하는 경우 Azure의 재귀 해결자 IP 주소(예: 168.63.129.16)를 목록에 추가해야 합니다. 자세한 내용은 참조 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
4. **서비스 끝점 없음**: 서브넷에는 연결된 서비스 끝점(저장소 또는 SQL)이 없어야 합니다. VNet을 만들 때 서비스 끝점 옵션이 사용 안함 상태인지 확인합니다.
5. **충분한 IP 주소**: 서브넷에는 최소 16개의 IP 주소가 있어야 합니다. 자세한 내용은 [관리되는 인스턴스의 서브넷 크기 결정](#determine-the-size-of-subnet-for-managed-instances)을 참조하세요.

> [!IMPORTANT]
> 대상 서브넷이 앞의 모든 요구 사항과 호환되지 않는 경우 새로운 관리되는 인스턴스를 배포할 수 없습니다. 위반으로 인해 잘못된 상태로 전환되는 인스턴스를 발생시키고 사용할 수 없게 되기 때문에 대상 VNet 및 서브넷은 (배포 이전 및 이후에) 이러한 관리되는 인스턴스 요구 사항을 준수해야 합니다. 해당 상태에서 복구하려면 네트워킹 준수 정책을 사용하여 VNet에서 새 인스턴스를 만들고, 인스턴스 수준 데이터를 만들고, 데이터베이스를 복원해야 합니다. 응용 프로그램에 상당한 가동 중지 시간이 발생합니다.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>관리되는 인스턴스의 서브넷 크기 결정

관리되는 인스턴스를 만들 때 Azure에서는 프로비전하는 동안 선택한 계층 크기에 따라 가상 머신의 수를 할당합니다. 이러한 가상 머신이 서브넷에 연결되어 있기 때문에 IP 주소가 필요합니다. 일반 작업 및 서비스 유지 관리 중에 고가용성을 보장하기 위해 Azure에서는 가상 머신을 추가로 할당할 수 있습니다. 결과적으로 서브넷의 필수 IP 주소 수는 해당 서브넷의 관리되는 인스턴스 수보다 많습니다. 

기본적으로 관리되는 인스턴스는 서브넷에서 최소 16개의 IP 주소가 필요하고 최대 256개의 IP 주소를 사용할 수 있습니다. 결과적으로 서브넷 IP 범위를 정의할 때 /28에서 /24 서브넷 마스크를 사용할 수 있습니다. 

서브넷 내에서 관리되는 여러 인스턴스를 배포하고 서브넷 크기를 최적화해야 하는 경우 다음과 같은 매개 변수를 사용하여 계산을 만듭니다. 

- Azure는 고유한 요구 사항으로 인해 서브넷에서 5개의 IP 주소를 사용합니다. 
- 범용 인스턴스마다 두 개의 주소가 필요합니다. 

**예제**: 8개의 관리되는 인스턴스를 사용할 계획입니다. 즉, 5+8*2=21개의 IP 주소가 필요합니다. IP 범위가 2의 거듭제곱으로 정의되므로 32개(2^5)의 IP 주소라는 IP 범위가 필요합니다. 따라서 /27 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>관리되는 인스턴스의 새로운 가상 네트워크 만들기 

Azure 가상 네트워크를 만드는 작업은 관리되는 인스턴스를 만들기 위한 필수 구성 요소입니다. Azure Portal, [PowerShell](../virtual-network/quick-create-powershell.md) 또는 [Azure CLI](../virtual-network/quick-create-cli.md)를 사용할 수 있습니다. 다음 섹션에서는 Azure Portal을 사용하는 단계를 보여줍니다. 여기서 설명하는 세부 정보는 이러한 각 방법에 적용됩니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. 찾은 다음, **가상 네트워크**를 클릭하고, **Resource Manager**가 배포 모드로 선택되었는지 확인한 다음, **만들기**를 클릭합니다.

   ![가상 네트워크 만들기](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 다음 스크린샷과 같은 방법으로 요청된 정보가 포함된 가상 네트워크 양식을 작성합니다.

   ![가상 네트워크 만들기 양식](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. **만들기**를 클릭합니다.

   주소 공간 및 서브넷은 CIDR 표기법으로 지정됩니다. 

   > [!IMPORTANT]
   > 기본값은 모든 VNet 주소 공간을 사용하는 서브넷을 만듭니다. 이 옵션을 선택하는 경우 관리되는 인스턴스가 아닌 가상 네트워크 내의 다른 모든 리소스를 만들 수 없습니다. 

   권장되는 방법은 다음과 같습니다. 
   - [관리되는 인스턴스에 대한 서브넷 크기 확인](#determine-the-size-of-subnet-for-managed-instances) 섹션에 따라 서브넷 크기를 계산합니다.  
   - VNet의 나머지 부분에 대한 요구 사항을 평가합니다. 
   - 적절한 VNet 서브넷 주소 범위를 입력합니다. 

   서비스 끝점 옵션이 **사용 안함** 상태인지 확인합니다. 

   ![가상 네트워크 만들기 양식](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>필수 경로 테이블 만들기 및 연결

1. Azure Portal에 로그인  
2. 찾은 다음, **경로 테이블**을 클릭하고, 경로 테이블 페이지에서 **만들기**를 클릭합니다.

   ![경로 테이블 만들기 양식](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. 다음 스크린샷과 같은 방식으로 0.0.0.0/0 다음 홉 인터넷 경로를 만듭니다.

   ![경로 테이블 추가](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![경로](./media/sql-database-managed-instance-tutorial/route.png)

4. 다음 스크린샷과 같은 방식으로 이 경로를 관리되는 인스턴스의 서브넷과 연결합니다.

    ![서브넷](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![경로 테이블 설정](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![경로 테이블 설정 저장](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


VNet을 만들면 관리되는 인스턴스를 만들 준비가 되었습니다.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>관리되는 인스턴스의 기존 가상 네트워크를 수정합니다. 

이 섹션의 질문 및 답변은 기존 가상 네트워크에 관리되는 인스턴스를 추가하는 방법을 보여줍니다. 

**기존 가상 네트워크에 클래식 또는 리소스 관리자 배포 모델을 사용하나요?** 

리소스 관리자 가상 네트워크에서 관리되는 인스턴스만을 만들 수 있습니다. 

**관리되는 SQL 인스턴스의 새 서브넷을 만들거나 기존 서브넷을 사용하시겠습니까?**

새로 만들려고 하는 경우: 

- [관리되는 인스턴스에 대한 서브넷 크기 확인](#determine-the-size-of-subnet-for-managed-instances) 섹션에 있는 지침에 따라 서브넷 크기를 계산합니다.
- [가상 네트워크 서브넷 추가, 변경 또는 삭제](../virtual-network/virtual-network-manage-subnet.md)에서 단계를 수행합니다. 
- 다음 홉 인터넷과 같이 단일 항목 **0.0.0.0/0**을 포함하는 경로 테이블을 만들고 관리되는 인스턴스의 서브넷과 연결합니다.  

기존 서브넷 내에서 관리되는 인스턴스를 만들려는 경우: 
- 서브넷이 비어 있는지 확인합니다. 게이트웨이 서브넷을 비롯한 기타 리소스를 포함하는 서브넷에서 관리되는 인스턴스를 만들 수 없습니다. 
- [관리되는 인스턴스에 대한 서브넷 크기 확인](#determine-the-size-of-subnet-for-managed-instances) 섹션에 있는 지침에 따라 서브넷 크기를 계산하고 적절하게 크기를 지정했는지 확인합니다. 
- 서비스 끝점이 서브넷에서 설정되어 있지 않는지 확인합니다.
- 서브넷과 연결된 네트워크 보안 그룹이 없는지 확인합니다. 

**사용자 지정 DNS 서버를 구성했나요?** 

그러한 경우 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요. 

- 필수 경로 테이블을 만들고 연결합니다. [필수 경로 테이블 만들기 및 연결](#create-the-required-route-table-and-associate-it)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 개요는 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- VNet을 만들고, 관리되는 인스턴스를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database 관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
