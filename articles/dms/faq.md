---
title: Azure Database Migration Service 사용에 대한 FAQ | Microsoft Docs
description: Azure Database Migration Service를 사용하여 데이터베이스 마이그레이션을 수행하는 데 대한 질문과 대답을 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 85052e1363ebbfe21cd7d6d5b3720f79cec7c417
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Azure Database Migration Service 사용에 대한 FAQ
이 문서에는 Azure Database Migration Service 사용에 대한 일반적인 질문이 관련된 답변과 함께 나열되어 있습니다.

### <a name="q-what-is-azure-database-migration-service"></a>Q. Azure Database Migration Service란 무엇인가요?
Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 현재 공개 미리 보기 중에 있으며 다음 사항에 초점을 맞추면서 개발에 노력하고 있습니다.
- 안정성 및 성능.
- 원본 -대상 쌍의 반복적 추가
- 충돌 없는 마이그레이션에 대한 지속적인 투자

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>Q. Azure Database Migration Service에서 현재 지원되는 원본-대상 쌍은 무엇인가요?
이 서비스는 공개 미리 보기 중이며 현재는 SQL Server에서 Azure SQL Database로 마이그레이션을 지원합니다. Azure Portal로 이동하면 이 시나리오에 대해 Azure Database Migration Service를 사용할 수 있습니다. SQL Server에서 Azure SQL Database 관리되는 인스턴스 및 Oracle에서 Azure SQL Database 등과 같은 다른 원본-대상 쌍은 제한된 비공개 미리 보기를 통해 사용할 수 있습니다. 이러한 시나리오의 제한된 비공개 미리 보기에 참여할 기회를 얻으려면 [여기](https://sqldatabase-migrationpreview.azurewebsites.net/)에서 등록하세요.

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Q. Azure Database Migration Service는 DMA(Database Migration Assistant)나 SSMA(SQL Server Migration Assistant)와 같은 다른 Microsoft 데이터베이스 마이그레이션 도구와 어떻게 비교되나요?
Azure Database Migration Service는 데이터베이스를 Microsoft Azure로 대 규모 마이그레이션하기에 좋은 방법입니다. Azure Database Migration Service가 다른 Microsoft 데이터베이스 마이그레이션 도구와 어떻게 비교되는지에 대한 자세한 내용과 다양한 시나리오에서 이 서비스를 사용하는 데 대한 권장 사항은 블로그 게시물인 [Differentiating Microsoft’s Database Migration Tools and Services](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)(Microsoft의 데이터베이스 마이그레이션 도구 및 서비스 차별화)를 참조하세요.

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Q. Azure Database Migration Service는 Azure Migrate 제품과 어떻게 비교되나요?
Azure Migrate 서비스는 온-프레미스 가상 머신을 Azure IaaS로 마이그레이션하는 데 도움이 됩니다. 이 서비스는 마이그레이션 적합성 및 성능 기반 크기 조정을 평가하며, Azure에서 온-프레미스 가성 머신을 실행할 때 드는 비용을 예측합니다. Azure Migrate는 온-프레미스 VM 기반 워크로드를 Azure IaaS VM으로 리프트 앤 시프트 마이그레이션하는 데 유용합니다. Azure Database Migration Service와 달리 Azure Migrate는 Azure SQL Database 또는 SQL Azure 또는 Azure SQL Database 관리되는 인스턴스 등의 Azure PaaS 관계형 데이터베이스 플랫폼에 특화된 데이터베이스 마이그레이션 서비스 제품이 아닙니다.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>Q. Azure Database Migration Service를 사용하여 데이터베이스 마이그레이션을 수행하기 위해 필요한 단계를 요약한 정보는 무엇입니까?
일반적이고 간단한 데이터베이스 마이그레이션 단계:
1.  대상 데이터베이스를 만듭니다.
2.  [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595)를 사용하여 데이터베이스 스키마를 마이그레이션합니다.
3.  Azure Database Migration Service의 인스턴스를 만듭니다.
4.  마이그레이션할 원본 데이터베이스, 대상 데이터베이스 및 테이블을 지정하는 마이그레이션 프로젝트를 만듭니다.
5.  전체 로드를 시작합니다.
6.  후속 유효성 검사를 선택합니다.
7.  새 클라우드 기반 데이터베이스로 프로덕션 환경의 수동 전환을 수행합니다. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>Q. Azure Database Migration Service 사용을 위한 필수 구성 요소에는 무엇이 있나요?
데이터베이스 마이그레이션을 수행할 때 Azure Database Migration Service가 원활히 실행되도록 하기 위해 필요한 필수 구성 요소가 몇 가지 있습니다. 일부 필수 구성 요소는 서비스가 지원하는 모든 시나리오(원본-대상 쌍)에 적용되는 반면에 특정 시나리오에만 적용되는 필수 구성 요소도 있습니다.
지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.
- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 VNET을 만듭니다. 이를 통해 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 온-프레미스 원본 서버에서 사이트 간 연결을 제공합니다.
- Azure VNET(Virtual Network) 네트워크 보안 그룹 규칙이 443, 53, 9354, 445, 12000과 같은 통신 포트를 차단하지 않는지 확인합니다. Azure VNET NSG 트래픽 필터링에 대한 자세한 정보는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
 
Azure Database Migration Service를 사용하여 특정 마이그레이션 시나리오를 수행하는 데 필요한 모든 필수 구성 요소 목록은 docs.microsoft.com에 있는 Azure Database Migration Service [설명서](https://docs.microsoft.com/azure/dms/dms-overview)에서 관련 자습서를 참조하세요.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Q. 마이그레이션을 위해 내 원본 데이터베이스에 액세스하는 데 사용되는 방화벽 규칙에 대한 허용 목록을 만들기 위한 Azure Database Migration Service의 IP 주소는 어떻게 찾을 수 있나요?
마이그레이션을 위해 Azure Database Migration Service가 원본 데이터베이스에 액세스 하도록 허용하는 방화벽 규칙을 추가해야 할 수도 있습니다. 서비스에 대한 IP 주소는 동적이지만 Express 경로를 사용하는 경우에는 이 주소가 회사 네트워크에서 비공개로 할당됩니다. 적절한 IP 주소를 식별하는 가장 쉬운 방법은 프로비전된 Azure Database Migration Service 리소스와 동일한 리소스 그룹을 살펴보고 관련 네트워크 인터페이스를 찾는 것입니다. 일반적으로 네트워크 인터페이스 리소스의 이름은 NIC 접두사로 시작되고 그 뒤에 고유한 문자와 숫자 시퀀스가 붙습니다(예 : NIC-jj6tnztnmarpsskr82rbndyp). 이 네트워크 인터페이스 리소스를 선택하면 Azure Portal 리소스 개요 페이지에서 허용 목록에 포함되어야 하는 IP 주소를 볼 수 있습니다.

SQL Server가 수신 대기하는 포트 원본을 허용 목록에 포함해야 할 수도 있습니다. 기본적으로 이 포트는 1433이지만 원본 SQL Server는 다른 포트도 수신 대기하도록 구성될 수 있습니다. 이 경우 해당 포트도 허용 목록에 포함시켜야 합니다. 동적 관리 뷰 쿼리를 사용하여 SQL Server가 수신 대기하는 포트를 확인할 수 있습니다.

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
SQL Server 오류 로그를 쿼리하여 SQL Server가 수신 대기하는 포트를 확인할 수도 있습니다.

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>Q. Azure Database Migration Service의 성능을 최적화하기 위한 권장 사항은 없나요?
몇 가지 작업을 수행하면 서비스를 사용한 데이터베이스 마이그레이션 속도를 높일 수 있습니다.
- 서비스 인스턴스를 만들 때 다중 CPU 범용 가격 책정 계층을 사용하면 서비스에서 다중 vCPU를 활용하여 병렬 처리 및 빠른 데이터 전송이 가능합니다.
- 하위 수준의 SKU를 사용할 때 데이터 전송 작업에 영향을 줄 수 있는 Azure SQL 데이터베이스 제한을 최소화하기 위해 데이터 마이그레이션 작업 중에 Azure SQL Database 대상 인스턴스를 프리미엄 계층 SKU로 일시 강화합니다.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Q. Azure Virtual Network는 어떻게 설정하나요?
Azure VNET 설정 프로세스를 단계별로 안내하는 Microsoft 자습서는 많이 있으며, 공식적인 문서는 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 문서에 나와있습니다.

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Q. 내 Azure Database Migration Service를 사용할 수 없거나 중지된 이유는 무엇입니까?
사용자가 명시적으로 Azure DMS(Database Migration Service)를 중지하거나 서비스가 24시간 동안 비활성 상태인 경우 서비스는 중지되거나 자동 일시 중지된 상태에 있게 됩니다. 각각의 경우에서 서비스는 사용할 수 없으며 중지된 상태에 있게 됩니다.  활성 마이그레이션을 다시 시작하려면 서비스를 다시 시작합니다.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>Q. Azure Database Migration Service에 대한 피드백은 어디에 남길 수 있나요?
많은 의견 부탁드립니다. Azure Database Migration Service에 대한 피드백이나 아이디어는 [여기](https://feedback.azure.com/forums/906100-azure-database-migration-service)에서 사용자 의견을 통해 보내주세요.

## <a name="next-steps"></a>다음 단계
공개 미리 보기 중 Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service 미리 보기란 무엇인가요?](dms-overview.md) 문서를 참조하세요. 
