---
title: Cortana Intelligence 솔루션 평가 도구 | Microsoft Docs
description: 여기서는 Microsoft 파트너가 Cortana Intelligence 솔루션을 AppSource에 게시하기 위해 수행해야 하는 모든 단계를 설명합니다.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams
ms.openlocfilehash: 24e6ab7e94d8a1bdbfa0b5ee9112aaaff7e358ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Cortana Intelligence 솔루션 평가 도구
## <a name="overview"></a>개요
Cortana Intelligence 솔루션 평가 도구를 사용하여 고급 분석 솔루션의 Microsoft 권장 모범 사례 준수를 평가할 수 있습니다. Microsoft는 파트너(ISV/SI)와 협력하여 고객, 재판매인 및 구현을 위한 고품질 솔루션을 제공합니다. 이 가이드에서는 솔루션과 함께 솔루션 평가 도구를 사용하는 프로세스를 안내하고 검사 대상의 특정 모범 사례에 대해 설명합니다.

## <a name="getting-started"></a>시작
Cortana Intelligence 솔루션 평가 도구를 [다운로드](https://aka.ms/aa-evaluation-tool-download)하고 설치하세요.

필수 조건:
- Windows 10: [Windows 10 공식 사이트](https://www.microsoft.com/en-us/windows)
- Azure Powershell: [Azure PowerShell을 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)합니다.

## <a name="identifying-your-app"></a>앱 식별
설치가 완료된 후 도구를 열고 첫 번째 평가를 시작합니다.

![평가 도구 열기](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

솔루션에 대한 식별 정보를 제공합니다.

![Azure 구독 연결](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Azure 구독에 연결하고 앱을 포함하는 리소스 그룹을 제공합니다.

![리소스 선택](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

리소스 그룹이 로드되면 솔루션에 포함된 리소스를 선택하고 데이터 리소스의 접근성을 다음 중 하나로 식별합니다.
- 수집
- Consumption
- 내부

이 정보를 통해 솔루션이 다양한 구성 요소를 활용하는 방식을 보다 정확하게 파악하고 사용자 연결 구성 요소가 모범 사례와 일치하도록 합니다.

### <a name="ingestion"></a>수집
이 경우 수집은 솔루션 외부에서 데이터를 가져오는 데 사용되거나 솔루션 외부 서비스가 데이터를 솔루션에 푸시하는 데 사용하는 데이터 원본을 의미합니다.

### <a name="consumption"></a>Consumption
이 경우 소비는 직접 또는 간접적으로 최종 사용자에게 데이터를 푸시하는 데 사용되는 데이터 집합을 의미합니다. 예: 
- PowerBI에서 직접 쿼리에 사용되는 데이터 집합
- WebApp에서 쿼리되는 데이터 집합

>[!NOTE]
특정 리소스가 수집과 소비 둘 다에 사용되는 경우 **소비**를 선택하세요.

### <a name="internal"></a>내부
내부 응용 프로그램 처리에만 사용되는 데이터 리소스의 경우 내부를 사용합니다.

이전 단계에서 지정한 데이터베이스에 유효한 자격 증명을 제공하라는 메시지가 표시됩니다.

![테스트 필수 조건 설정](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>솔루션 테스트 사례
솔루션 도구는 솔루션에 대해 자동화된 테스트 컬렉션을 수행합니다.

![테스트 실행 설정](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

테스트가 완료되면 솔루션이 요구 사항을 준수하지 않는 이유에 대한 설명이나 근거를 제공하라는 메시지가 표시됩니다.

![비즈니스 근거 제공](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

예를 들어 솔루션이 Azure SQL DW에 게시되는 경우 평가 테스트에서 Azure Analysis Services에도 게시하도록 요구합니다. 

솔루션이 Azure Analysis Services 대신 SQL Server Analysis Services를 실행하는 IaaS 가상 머신을 사용할 수도 있습니다. 이는 테스트 오류에 대해 허용되는 이유가 됩니다.
## <a name="packaging-your-evaluation-results"></a>평가 결과 패키징
테스트 사례를 완료하면 평가 패키지가 zip 파일로 내보내지고, 평가 도구에 대한 피드백을 제공하라는 메시지가 표시됩니다. 

AppSource에 솔루션을 추가하도록 승인을 받기 전에 솔루션을 평가할 수 있도록 이 테스트 결과 zip 파일을 Microsoft와 공유해야 합니다.

![평가 도구에 등급 지정](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

이 문서의 위 섹션에는 이 도구의 다양한 기능이 설명되어 있으므로 이 도구를 평가하는 모범 사례 유형을 검토해 보겠습니다.

## <a name="security-evaluation-considerations"></a>보안 평가 고려 사항
### <a name="databases-should-use-azure-active-directory-authentication"></a>데이터베이스에서 Azure Active Directory 인증을 사용해야 함
AAD(Azure Active Directory) 인증을 통해 솔루션의 Azure SQL 또는 Azure SQL DW 리소스를 사용하도록 설정해야 합니다. AAD는 모든 ID 및 역할을 관리하는 단일 위치가 됩니다.

| 항목 | 참조 문서 |
| --- | --- |
| SQL Database 및 SQL Data Warehouse에서의 AAD | [SQL Database 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) |
| AAD 구성 및 관리 | [SQL Database 또는 SQL Data Warehouse에서의 Azure Active Directory 인증 구성 및 관리](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) |
| Azure WebApps 인증 | [Azure App Service의 인증 및 권한 부여](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) |
| AAD를 사용하여 WebApps 구성 | [Azure Active Directory 로그인을 사용하도록 App Service 응용 프로그램을 구성하는 방법](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>최종 사용자가 액세스할 수 있는 데이터 집합이 역할 기반 액세스 제어를 지원해야 함
평가 도구를 실행하는 동안 보고 또는 게시 리소스를 지정하라는 메시지가 표시됩니다. 이러한 리소스는 개발자가 아닌 최종 사용자가 액세스하기 위한 것으로 간주됩니다. 최종 사용자가 권한이 있는 데이터만 액세스할 수 있도록 하기 위해 이러한 리소스는 RBAC(역할 기반 액세스 제어)를 제공해야 합니다.

특히, 다음과 같은 Azure 리소스는 RBAC로 구성될 수 있으며 허용되는 것으로 간주됩니다.
- Secure HDInsight, [도메인 가입 HDInsight 클러스터를 사용한 Hadoop 보안 소개](https://docs.microsoft.com/azure/hdinsight/hdinsight-domain-joined-introduction) 참조
- Azure SQL, [Azure SQL에서의 AAD 인증]( https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) 참조
- Azure Analysis Services, [Azure Analysis Services에 대한 데이터베이스 역할 및 사용자 관리](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users) 참조
- Azure SQL Data Warehouse(SQL DW는 RBAC를 지원하기 때문에 직접적인 최종 사용자 액세스에는 권장되지 않음)

RBAC를 지원하는 다른 리소스 종류를 사용하는 경우 테스트 사례 근거에서 지정하세요.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store에서 미사용 암호화를 사용해야 함
ADLS(Azure Data Lake Store)는 기본적으로 ADLS 관리 암호화 키를 사용하여 미사용 암호화를 지원합니다. Azure Key Vault를 사용하여 암호화를 구성할 수도 있습니다.

ADLS 암호화 설정을 지정하는 방법에 대한 자세한 내용은 [Azure Data Lake Store 계정 만들기](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account)를 참조하세요.

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL 및 Azure SQL Data Warehouse에서 암호화를 사용해야 함
Azure SQL 및 Azure SQL DW는 둘 다 TDE(투명한 데이터 암호화)를 지원합니다. TDE는 데이터와 로그 파일 둘 다의 실시간 암호화 및 암호 해독을 제공합니다.

| 항목 | 참조 문서 |
| --- | --- |
| TDE(투명한 데이터 암호화) | [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse 및 TDE | [SQL Data Warehouse 암호화 TDE TSQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| TDE를 사용하여 Azure SQL 구성 | [Azure SQL Database를 사용한 투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Always Encrypted를 사용하여 Azure SQL 구성 | [SQL Database Always Encrypted Azure Key Vault](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

TDE 외에도 Azure SQL은 데이터가 미사용 및 클라이언트와 서버 간에 이동 중일 때뿐 아니라 서버에서 명령을 실행하여 데이터가 사용 중일 때도 데이터가 암호화되도록 하는 새로운 데이터 암호화 기술인 Always Encrypted를 지원합니다.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Virtual Machines는 Azure Marketplace에서 배포되어야 함
AppSource 간에 일관된 수준의 보안을 제공하기 위해 Cortana Intelligence 솔루션의 일부로 배포되는 가상 머신은 Azure Marketplace에서 인증 및 게시되어야 합니다.

Azure Marketplace 이미지의 현재 목록을 검색하려면 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute)를 참조하세요.

Azure Marketplace용 가상 머신 이미지를 게시하는 방법에 대한 자세한 내용은 [Azure Marketplace용 가상 머신 이미지 만들기 가이드](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)를 참조하세요.

## <a name="scalability-evaluation-considerations"></a>확장성 평가 고려 사항
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Cortana Intelligence 솔루션에 확장 가능한 빅 데이터 플랫폼이 포함되어야 함
Cortana Intelligence 솔루션은 매우 큰 데이터 크기로 확장되어야 합니다. Azure에서 이는 다음 두 가지 페타바이트 규모 데이터 플랫폼 중 하나를 포함해야 함을 의미합니다.
- Azure Data Lake Store
- Azure SQL Data Warehouse

솔루션이 이러한 데이터 크기에 대한 지원을 요구하지 않거나 대체 데이터 플랫폼을 사용하는 경우 테스트 사례 근거에서 설명해 주세요.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Cortana Intelligence 솔루션에 전용 수집 데이터 환경이 포함되어야 함
일반적으로 Cortana Intelligence 솔루션은 관계형 데이터 원본에 직접 데이터를 삽입하지 않아야 합니다. 대신, 구조화되지 않은 환경에 원시 데이터를 저장하고 Azure Data Factory를 사용하여 관계형 저장소에 idempotent 삽입/업데이트해야 합니다.

Azure Data Factory를 사용하여 데이터를 복사하는 방법에 대한 자세한 내용은 [자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기](https://docs.microsoft.com/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio)를 참조하세요.

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL Data Warehouse는 데이터 수집에 PolyBase를 사용해야 함
Azure SQL DW는 확장성이 높은 병렬 데이터 수집을 위해 PolyBase를 지원합니다. PolyBase를 사용하면 Azure SQL DW를 통해 Azure Blob Storage 또는 Azure Data Lake Store에 저장된 외부 데이터 집합에 대해 쿼리를 실행할 수 있습니다. 이는 대량 업데이트의 대체 방법에 비해 뛰어난 성능을 제공합니다.

PolyBase 및 Azure SQL DW 시작 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 PolyBase를 사용하여 데이터 로드](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase)를 참조하세요.

PolyBase 및 Azure SQL DW를 사용한 모범 사례에 대한 자세한 내용은 [SQL Data Warehouse에서 PolyBase를 사용하기 위한 가이드](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide)를 참조하세요.

## <a name="availability-evaluation-considerations"></a>가용성 평가 고려 사항

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>최종 사용자가 액세스할 수 있는 데이터 집합은 대량 동시 사용자를 지원해야 함
평가 도구를 실행하는 동안 보고 또는 게시 리소스를 지정하라는 메시지가 표시됩니다. 이러한 리소스는 개발자가 아닌 최종 사용자가 액세스하기 위한 것으로 간주됩니다. 이러한 리소스는 중간-다수의 동시 사용자를 지원해야 합니다.

특히, Azure SQL Data Warehouse가 최종 사용자에게 제공되는 유일한 데이터 원본이 아니어야 합니다. Azure SQL DW가 고급 사용자용 리소스로 제공되는 경우 일반 사용자에게 Azure Analysis Services를 제공해야 합니다.

Azure SQL DW 동시성 제한에 대한 자세한 내용은 [SQL Data Warehouse의 동시성 및 워크로드 관리](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency)를 참조하세요.

Azure Analysis Services에 대한 자세한 내용은 [Analysis Services 개요](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)를 참조하세요.

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Azure SQL 리소스에 장애 조치(failover)를 위한 읽기 전용 복제본이 있어야 함
Azure SQL Database는 보조 인스턴스에 대한 지역에서 복제를 지원합니다. 그런 다음 이 인스턴스를 장애 조치(failover) 인스턴스로 사용하여 고가용성 응용 프로그램을 제공할 수 있습니다.

Azure SQL Database의 지역에서 복제에 대한 자세한 내용은 [SQL Database 지역 복제 개요](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)를 참조하세요.

Azure SQL에 대해 지역에서 복제를 구성하는 방법에 대한 지침은 [Transact-SQL로 Azure SQL Database에 대한 활성 지역 복제 구성](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-transact-sql)을 참조하세요.

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL Data Warehouse에 지역 중복 백업이 사용하도록 설정되어 있어야 함
Azure SQL DW는 지역 중복 저장소에 대한 매일 백업을 지원합니다. 이 지역에서 복제는 주 지역에 저장된 스냅숏에 액세스할 수 없는 경우에도 데이터 웨어하우스를 복원할 수 있도록 해줍니다. 이 기능은 기본적으로 켜져 있으며 Cortana Intelligence 솔루션에 대해 사용하지 않도록 설정하면 안 됩니다.

Azure SQL DW 백업 및 복원에 대한 자세한 내용은 [SQL Data Warehouse Backup](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-backups)을 참조하세요.

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>가상 머신을 가용성 집합으로 구성해야 함
계획되었거나 계획되지 않은 유지 관리 이벤트의 영향을 최소화하려면 Azure Virtual Machines를 가용성 집합으로 구성해야 합니다.

Azure 가상 머신 가용성에 대한 자세한 내용은 [Azure에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)를 참조하세요.

## <a name="other-evaluation-considerations"></a>기타 평가 고려 사항
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Cortana Intelligence 앱은 데이터 오케스트레이션에 중앙 집중식 도구를 사용해야 함
데이터 이동 및 변환을 관리하고 예약하는 데 단일 도구를 사용하면 중요 업무용 데이터에 대한 일관성을 유지할 수 있습니다. 재시도 논리, 종속성 관리, 경고/로깅 등에 대한 명확한 논리를 제공합니다. Azure의 데이터 오케스트레이션에는 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-introduction)를 사용하는 것이 좋습니다.

Azure Data Factory 이외의 도구를 데이터 오케스트레이션에 사용하는 경우 사용 중인 도구를 설명해 주세요.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Machine Learning 모델을 다시 학습해야 함
AzureML(Azure Machine Learning)은 예측 모델링 및 기계 학습 파이프라인의 생성 및 배포를 위한 사용하기 쉬운 도구를 제공합니다. 그러나 이 AzureML 모델의 프로덕션 배포는 단일 고정 데이터 집합을 기반으로 하지 않고 실제 현상의 동적 변화에 맞게 조정되어야 합니다.

AzureML에서 다시 학습 웹 서비스를 만드는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](https://docs.microsoft.com/azure/machine-learning/machine-learning-retrain-models-programmatically)을 참조하세요.

Azure Data Factory를 사용하여 모델 학습 프로세스를 자동화하는 방법에 대한 자세한 내용은 [업데이트 리소스 작업을 사용하여 Azure Machine Learning 모델 업데이트](https://docs.microsoft.com/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity)를 참조하세요.

## <a name="existing-documentation"></a>기존 설명서
[클라우드 비즈니스 성장을 위한 Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/)

[Cortana Intelligence용 Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/cortana/)

