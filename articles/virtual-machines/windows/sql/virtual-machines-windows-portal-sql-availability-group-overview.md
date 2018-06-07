---
title: "SQL Server 가용성 그룹 - Azure Virtual Machines - 개요 | Microsoft Docs"
description: "이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure Virtual Machines의 SQL Server Always On 가용성 그룹 소개 #

이 문서에서는 Azure Virtual Machines의 SQL Server 가용성 그룹을 소개합니다. 

Azure Virtual Machines의 Always On 가용성 그룹은 온-프레미스의 Always On 가용성 그룹과 비슷합니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요. 

이 다이어그램에서는 Azure Virtual Machines의 전체 SQL Server 가용성 그룹 일부를 보여 줍니다.

![가용성 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure Virtual Machines의 가용성 그룹에 대한 주요 차이점은 Azure Virtual Machines에 [부하 분산 장치](../../../load-balancer/load-balancer-overview.md)가 필요하다는 것입니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 보유합니다. 가용성 그룹을 여러 개 있는 경우 그룹마다 수신기가 필요합니다. 하나의 부하 분산 장치가 여러 수신기를 지원할 수 있습니다.

Azure Virtual Machines에서 SQL Server 가용성을 빌드할 준비가 되면 다음 자습서를 참조하세요.

## <a name="automatically-create-an-availability-group-from-a-template"></a>템플릿에서 자동으로 가용성 그룹 만들기

[자동으로 Azure VM의 Always On 가용성 그룹 구성 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Azure Portal에서 수동으로 가용성 그룹 만들기

또한 템플릿을 사용하지 않고 가상 머신을 직접 만들 수도 있습니다. 먼저 필수 구성 요소를 완료한 다음 가용성 그룹을 만듭니다. 또한 다음 항목을 참조하세요. 

- [Azure Virtual Machines의 SQL Server Always On 가용성 그룹에 대한 필수 구성 요소 구성](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On 가용성 그룹을 만들어 가용성 및 재해 복구 개선](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>다음 단계

[다른 지역의 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 구성](virtual-machines-windows-portal-sql-availability-group-dr.md).
