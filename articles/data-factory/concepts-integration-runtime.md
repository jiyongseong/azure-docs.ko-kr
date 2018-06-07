---
title: Azure Data Factory의 통합 런타임 | Microsoft Docs
description: Azure Data Factory의 통합 런타임에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 97c2f68356a6a589f48224d297493509786ceff1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory의 통합 런타임
IR(통합 런타임)은 서로 다른 네트워크 환경 간에 다음과 같은 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 계산 인프라입니다.

- **데이터 이동**: 공용 네트워크의 데이터 저장소 및 개인 네트워크(온-프레미스 또는 가상 사설망)의 데이터 저장소 간에 데이터를 이동합니다. 기본 제공 커넥터, 형식 변환, 열 매핑 및 성능이 뛰어나고 확장 가능한 데이터 전송에 대한 지원을 제공합니다.
- **작업 디스패치**:  통합 런타임은 Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server 등 다양한 계산 서비스에서 실행하는 변환 작업을 디스패치 및 모니터링합니다.
- **SSIS 패키지 실행**: SSIS(SQL Server 통합 서비스) 패키지를 관리되는 Azure 계산 환경에서 고유하게 실행합니다.


> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

Data Factory에서 작업은 수행할 작업을 정의합니다. 연결된 서비스는 대상 데이터 저장소 또는 계산 서비스를 정의합니다. 통합 런타임은 작업과 연결된 서비스 간의 브리지를 제공합니다.  이는 연결된 서비스에 의해 참조되며 작업이 실행하거나 디스패치되는 계산 환경을 제공합니다.  이러한 방식으로 보안 및 준수 요구를 충족하면서 가장 성능이 뛰어난 방법으로 대상 데이터 저장소 또는 계산 서비스에 가능하면 가장 가까운 영역에서 작업을 수행할 수 있습니다.

## <a name="integration-runtime-types"></a>통합 런타임 유형
Data Factory는 세 유형의 통합 런타임을 제공하며, 데이터 통합 기능 및 원하는 네트워크 환경 요구에 가장 적합한 유형을 선택해야 합니다.  세 유형은 다음과 같습니다.

- Azure
- 자체 호스팅
- Azure-SSIS

다음 테이블은 각 통합 런타임 유형에 대한 기능 및 네트워크 지원을 설명합니다.

IR 유형 | 공용 네트워크 | 개인 네트워크
------- | -------------- | ---------------
Azure | 데이터 이동<br/>작업 디스패치 | &nbsp;
자체 호스팅 | 데이터 이동<br/>작업 디스패치 | 데이터 이동<br/>작업 디스패치
Azure-SSIS | SSIS 패키지 실행 | SSIS 패키지 실행

다음 다이어그램은 서로 다른 통합 런타임을 조합해 사용하여 풍부한 데이터 통합 기능 및 네트워크 지원을 제공할 수 있는 방법을 보여 줍니다.

![통합 런타임의 다양한 유형](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Azure 통합 런타임
Azure 통합 런타임은 다음을 수행할 수 있습니다.

- 클라우드 데이터 저장소 간에 복사 작업 실행
- 공용 네트워크에서 다음과 같은 변환 작업 수행: HDInsight Hive 작업, HDInsight Pig 작업, HDInsight MapReduce 작업, HDInsight Spark 작업, HDInsight Streaming 작업, Machine Learning 배치 실행 작업, Machine Learning 업데이트 리소스 작업, 저장 프로시저 작업, Data Lake Analytics U-SQL 작업, .Net 사용자 지정 작업, 웹 작업, 조회 작업 및 메타데이터 가져오기 작업.

### <a name="network-environment"></a>네트워크 환경
Azure 통합 런타임은 공개 액세스 가능한 끝점이 있는 공용 네트워크에서 데이터 소스에 연결 및 계산 서비스를 지원합니다. Azure Virtual Network 환경에는 자체 호스팅 통합 런타임을 사용합니다.

### <a name="compute-resource-and-scaling"></a>계산 리소스 및 크기 조정
Azure 통합 런타임은 Azure에서 완전히 관리되고 서버를 사용하지 않는 계산을 제공합니다.  인프라 프로비전, 소프트웨어 설치, 패치 또는 용량 크기 조정을 걱정할 필요가 없습니다.  또한 실제 사용 기간에 대해서만 지불합니다.

Azure 통합 런타임은 안전하고 안정적이고 고성능의 방법으로 클라우드 데이터 저장소 간에 데이터를 이동하는 고유 계산을 제공합니다.  Azure 통합 런타임의 크기를 명시적으로 조정할 필요 없이 복사 작업에 사용할 데이터 이동 단위 수를 설정할 수 있으며 Azure IR의 계산 크기는 그에 따라 탄력적으로 확장됩니다.

작업 디스패치는 작업을 대상 계산 서비스에 경로 설정하는 간단한 작업이므로 이 시나리오를 위해 계산 크기를 확장할 필요가 없습니다.

Azure IR 만들기 및 구성에 대한 자세한 내용은 방법 가이드 아래의 Azure IR 만들기 및 구성 방법을 참조하세요. 

## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임
자체 호스팅 IR로 다음을 수행할 수 있습니다.

- 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 작업 실행.
- 온-프레미스 또는 Azure Virtual Network의 계산 리소스에 대해 다음과 같은 변환 작업 디스패치: HDInsight Hive 작업(BYOC), HDInsight Pig 작업(BYOC), HDInsight MapReduce 작업(BYOC), HDInsight Spark 작업(BYOC), HDInsight Streaming 작업(BYOC), Machine Learning 배치 실행 작업, Machine Learning 업데이트 리소스 작업, 저장 프로시저 작업, Data Lake Analytics U-SQL 작업, .Net 사용자 지정 작업, 조회 작업 및 메타데이터 가져오기 작업.

> [!NOTE] 
> 자체 호스팅 통합 런타임을 사용하여 SAP Hana, MySQL 등 BYOD(bring-your-own driver)를 요구하는 데이터 저장소를 지원합니다.  자세한 내용은 [지원되는 데이터 소스](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.

### <a name="network-environment"></a>네트워크 환경
공용 클라우드 환경에서 직접 시야를 가지고 있지 않은 개인 네트워크 환경에서 데이터 통합을 안전하게 수행하려면 회사 방화벽 뒤에 또는 가상 사설망 내에 온-프레미스 환경에 자체 호스팅 IR을 설치할 수 있습니다.  자체 호스팅 통합 런타임은 아웃바운드 HTTP 기반 연결만이 인터넷을 열게 만듭니다.

### <a name="compute-resource-and-scaling"></a>계산 리소스 및 크기 조정
자체 호스팅 IR은 온-프레미스 컴퓨터 또는 개인 네트워크 내의 가상 머신에 설치해야 합니다. 현재 Windows 운영 체제에서만 자체 호스팅 IR의 실행을 지원합니다.  

고가용성과 확장성을 위해 논리 인스턴스를 활성-활성 모드의 여러 온-프레미스 컴퓨터와 연결하여 자체 호스팅 IR을 확장할 수 있습니다.  자세한 내용은 세부 정보 방법 가이드 아래의 자체 호스팅 IR 만들기 및 구성 방법 문서를 참조하세요.

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임
기존 SSIS 워크로드를 올리고 이동하려면 SSIS 패키지를 고유하게 실행하는 Azure-SSIS IR을 만들 수 있습니다.

### <a name="network-environment"></a>네트워크 환경
Azure SSIS IR은 공용 네트워크 또는 개인 네트워크에서 프로비전할 수 있습니다.  온-프레미스 데이터 액세스는 Azure-SSIS IR을 온-프레미스 네트워크에 연결된 VNet(가상 네트워크)에 조인하면 지원됩니다.  

### <a name="compute-resource-and-scaling"></a>계산 리소스 및 크기 조정
Azure-SSIS IR은 SSIS 패키지 실행을 전담하는 완전히 관리되는 Azure VM 클러스터입니다. 자기만의 Azure SQL Database 또는 관리되는 인스턴스(미리 보기) 서버를 해당 서버에 연결할 SSIS 프로젝트/패키지(SSISDB)의 카탈로그를 호스팅하게 만들 수 있습니다. 노드 크기를 지정하여 계산 능력을 확장하고 클러스터의 노드 수를 지정하여 확장할 수 있습니다. Azure-SSIS 통합 런타임을 중단하고 적합해 보일 때 시작하여 그 실행 비용을 관리할 수 있습니다.

자세한 내용은 방법 가이드 아래의 Azure-SSIS IR 만들기 및 구성 방법 문서를 참조하세요.  만든 후 SSIS 온-프레미스를 사용할 때와 마찬가지로 SSDT(SQL Server 데이터 도구) 및 SSMS(SQL Server Management Studio) 등 친숙한 도구를 사용하여 기존 SSIS 패키지를 조금만 변경하고 배포 및 관리할 수 있습니다.

Azure-SSIS 런타임에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [자습서: Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL 관리되는 인스턴스(미리 보기)를 사용하고 IR을 VNet에 조인하는 지침을 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보와 반환된 정보의 상태 설명을 검색하는 방법을 설명합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
- [Azure-SSIS IR을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서는 Azure-SSIS IR을 Azure 가상 네트워크(VNet)에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 VNet에 조인할 수 있도록 VNet을 구성하는 단계도 제공합니다. 

## <a name="determining-which-ir-to-use"></a>사용할 IR 결정
각 변환 작업에는 통합 런타임을 가리키는 대상 계산 연결된 서비스가 있습니다. 이 통합 런타임 인스턴스는 변환 작업이 디스패치되는 곳입니다.

복사 작업의 경우 데이터 흐름의 방향을 정의하기 위해 소스 및 싱크 연결된 서비스가 필요합니다. 다음 논리를 사용하여 복사를 수행하는 데 사용하는 통합 런타임 인스턴스를 결정합니다. 

- **두 클라우드 데이터 소스 간 복사**: 소스 및 싱크 연결된 서비스가 모두 Azure IR을 사용하는 경우 싱크 연결된 서비스가 사용하는 통합 런타임을 사용하여 복사 작업을 실행합니다.
- **클라우드 데이터 소스와 개인 네트워크의 데이터 소스 간 복사**: 소스 또는 싱크 연결된 서비스 중 하나가 자체 호스팅 IR을 가리키는 경우 해당 자체 호스팅 통합 런타임에서 복사 작업을 실행합니다.
- **개인 네트워크의 두 데이터 소스 간 복사**: 소스 및 싱크 연결된 서비스가 모두 통합 런타임의 같은 인스턴스를 가리켜야 하며 해당 통합 런타임을 사용하여 복사 작업을 실행합니다.

다음 다이어그램은 두 가지 복사 작업 샘플을 보여 줍니다.

- 복사 작업 1의 경우 해당 소스는 자체 호스팅 IR A를 참조하는 SQL Server 연결된 서비스이며 그 싱크는 Azure IR B를 참조하는 Azure Storage 연결된 서비스입니다. 복사 작업을 실행하는 경우 자체 호스팅 IR A에서 실행됩니다.
- 복사 작업 2의 경우 그 소스는 Azure IR C를 참조하는 Azure SQL Database 연결된 서비스이며 그 싱크는 Azure IR B를 참조하는 Azure Storage 연결된 서비스입니다. 복사 작업을 실행하는 경우 싱크 연결된 서비스가 사용하는 통합 런타임이기 때문에 Azure IR B에서 실행됩니다.

![사용할 IR](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>통합 런타임 위치
Data Factory 위치는 Data Factory의 메타데이터가 저장되는 곳이며 파이프라인 트리거가 시작되는 곳입니다. 현재 지원되는 Data Factory 위치는 미국 동부, 미국 동부 2, 동남 아시아 및 유럽 서부입니다. 그러나 데이터 팩터리는 계산 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 계산 서비스에 액세스할 수 있습니다. 이 동작은 데이터 준수, 효율성 및 네트워크 송신 비용 감소를 위해 여러 지역에서 전역적으로 사용할 수 있는 IR을 통해 이루어집니다.

IR 위치는 해당 백 엔드 계산의 위치, 즉 기본적으로 데이터 이동, 작업 디스패치 및 SSIS 패키지 실행이 수행되는 위치를 정의합니다. IR 위치는 자신이 속한 데이터 팩터의 위치와 다를 수 있습니다. 다음 다이어그램은 Data Factory 및 해당 통합 런타임의 위치를 보여 줍니다.

![통합 런타임 위치](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure IR
Data Factory는 데이터를 이동하는 것과 같은 지리적 위치의 싱크에 가장 가까운 지역의 Azure IR을 사용합니다. 매핑은 다음 표를 참조하세요.

싱크 데이터 저장소의 지리적 위치 | 싱크 데이터 저장소의 위치 | Azure 통합 런타임에 사용되는 위치
-------------------------------| ----------------| ------------------
미국 | 미국 동부 | 미국 동부
&nbsp; | 미국 동부 2 | 미국 동부 2
&nbsp; | 미국 중부 | 미국 중부
&nbsp; | 미국 중북부 | 미국 중북부
&nbsp; | 미국 중남부 | 미국 중남부
&nbsp; | 미국 중서부 | 미국 중서부
&nbsp; | 미국 서부 | 미국 서부
&nbsp; | 미국 서부 2 | 미국 서부 2
캐나다 | 캐나다 동부 | 캐나다 중부
&nbsp; | 캐나다 중부 | 캐나다 중부
브라질 | 브라질 남부 | 브라질 남부
유럽 | 북유럽 | 북유럽
&nbsp; | 서유럽 | 서유럽
영국 | 영국 서부 | 영국 남부
&nbsp; | 영국 남부 | 영국 남부
아시아 태평양 | 동남아시아 | 동남아시아
&nbsp; | 동아시아 | 동남아시아
오스트레일리아 | 오스트레일리아 동부 | 오스트레일리아 동부
&nbsp; | 오스트레일리아 남동부 | 오스트레일리아 남동부
일본 | 일본 동부 | 일본 동부
&nbsp; | 일본 서부 | 일본 동부
한국 | 한국 중부 | 한국 중부
&nbsp; | 한국 남부 | 한국 중부
인도 | 인도 중부 | 인도 중부
&nbsp; | 인도 서부 | 인도 중부
&nbsp; | 인도 남부 | 인도 중부

또한 Azure IR의 위치를 자동 확인으로 설정할 수 있으며, 이는 Data Factory가 연결된 서비스 정의를 기반으로 사용하기에 가장 적합한 위치를 자동으로 검색하기 위해 최선을 다한다는 것을 의미합니다.

> [!NOTE] 
> 대상 데이터 저장소의 지역이 목록에 없거나 검색할 수 없는 경우 작업은 준수상의 이유로 대체 지역을 통해 이동하는 대신에 실패합니다. 이 경우 복사를 수행하는 데 사용할 대체 위치를 명시적으로 지시해야 합니다.
 
다음 그림은 Azure IR의 위치가 자동 확인으로 설정된 경우 효과적인 위치의 예를 보여 줍니다. 복사 작업을 실행하는 경우 데이터 대상의 위치를 검색하며, 이 경우 이 위치는 일본 서부입니다.  테이블을 기반으로 일본 동부의 Azure IR을 사용하여 실제 데이터 복사를 수행합니다. 같은 IR을 사용하여 Spark 작업을 위해 HDInsight에 연결하는 경우 Data Factory 위치(이 경우 미국 동부)에서 Spark 응용 프로그램 제출이 이루어지며 Spark 응용 프로그램의 실제 실행은 HDInsight 서버 위치에서 이루어집니다. 

![효과적인 위치](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>자체 호스팅 IR
자체 호스팅 IR은 논리적으로 Data Factory에 등록되며 해당 기능을 지원하기 위해 사용되는 계산은 사용자가 제공합니다. 따라서 자체 호스팅 IR에 대한 명시적 위치 속성은 없습니다. 

데이터 이동을 수행하기 위해 사용하는 경우 자체 호스팅 IR은 소스에서 데이터를 추출하고 대상에 씁니다.

### <a name="azure-ssis-ir"></a>Azure-SSIS IR
Azure SSIS IR에 적합한 위치 선택은 ETL(추출-변환-로드) 워크플로에서 고성능을 달성하기 위해 필수적입니다.  6개의 위치는 처음에 미리 보기할 수 있습니다(미국 동부, 미국 동부 2, 미국 중부, 오스트레일리아 동부, 북유럽 및 유럽 서부).

- Azure SSIS IR의 위치가 데이터 팩터리의 위치와 같을 필요는 없지만, SSISDB를 호스팅할 사용자 자신의 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버의 위치와 같아야 합니다. 이러한 방식으로 Azure SSIS 통합 런타임은 서로 다른 위치 간에 과도한 트래픽을 초래하지 않고 SSISDB에 쉽게 액세스할 수 있습니다.
- SSISDB를 호스팅할 기존 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버는 없지만 온-프레미스 데이터 소스/대상이 있는 경우 온-프레미스 네트워크에 연결된 VNet의 같은 위치에 새 Azure SQL Database/관리되는 인스턴스(미리 보기)를 만들어야 합니다.  이러한 방식으로 새 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버를 사용하여 모두 같은 위치에 있는 VNet이 서로 다른 위치 간에 데이터 이동을 효과적으로 최소화하도록 조인하여 Azure SSIS IR을 만들 수 있습니다.
- SSISDB를 호스팅하는 기존 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버의 위치가 온-프레미스 네트워크에 연결된 VNet의 위치와 같지 않은 경우 먼저 기존 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버를 사용하여 같은 위치에 다른 VNet을 조인하여 Azure-SSIS IR을 만든 다음, 서로 다른 위치 간에 VNet 대 VNet 연결을 구성합니다.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [자체 호스팅 통합 런타임 만들기](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 통합 런타임을 만듭니다](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL 관리되는 인스턴스(미리 보기)를 사용하고 IR을 VNet에 조인하는 지침을 제공합니다. 
