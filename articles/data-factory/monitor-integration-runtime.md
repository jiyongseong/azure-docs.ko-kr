---
title: Azure Data Factory의 통합 런타임 모니터링 | Microsoft Docs
description: Azure Data Factory에서 다양한 유형의 통합 런타임을 모니터링하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: ''
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: douglasl
ms.openlocfilehash: 4c857af02e104940559cb86daa3ccd208d8e35a1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Azure Data Factory의 통합 런타임 모니터링  
**통합 런타임**은 서로 다른 네트워크 환경에서 다양한 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 계산 인프라입니다. Data Factory는 세 가지 유형의 통합 런타임을 제공합니다.

- Azure 통합 런타임
- 자체 호스팅 통합 런타임
- Azure SSIS 통합 런타임

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

IR(통합 런타임) 인스턴스의 상태를 가져 오려면 다음 PowerShell 명령을 실행합니다. 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

이 cmdlet은 여러 유형의 통합 런타임에 대해 서로 다른 정보를 반환합니다. 이 문서는 각 통합 런타임 유형의 속성 및 상태에 대해 설명합니다.  

## <a name="azure-integration-runtime"></a>Azure 통합 런타임
Azure 통합 런타임의 계산 리소스는 Azure에서 완전히 탄력적으로 관리됩니다. 다음 테이블은 **Get-AzureRmDataFactoryV2IntegrationRuntime** 명령에 의해 반환되는 속성에 대한 설명을 제공합니다.

### <a name="properties"></a>properties
다음 테이블은 Azure 통합 런타임에 대해 cmdlet에서 반환하는 속성에 대한 설명을 제공합니다.

| 자산 | 설명 |
-------- | ------------- | 
| Name | Azure 통합 런타임의 이름. |  
| 시스템 상태 | Azure 통합 런타임의 상태. | 
| 위치 | Azure 통합 런타임의 위치. Azure 통합 런타임의 위치에 대한 자세한 내용은 [통합 런타임 소개](concepts-integration-runtime.md)를 참조하세요. |
| DataFactoryName | Azure 통합 런타임이 속한 데이터 팩터리의 이름. | 
| ResourceGroupName | 데이터 팩터리가 속한 리소스 그룹의 이름.  |
| 설명 | 통합 런타임에 대한 설명.  |

### <a name="status"></a>상태
다음 테이블은 Azure 통합 런타임의 가능한 상태를 제공합니다.

| 상태 | 설명/시나리오 | 
| ------ | ------------------ |
| 온라인 | Azure 통합 런타임이 온라인 상태이며 사용할 준비가 되었습니다. | 
| 오프라인 | Azure 통합 런타임이 내부 오류로 인해 오프라인 상태입니다. |

## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임
이 섹션은 Get-AzureRmDataFactoryV2IntegrationRuntime cmdlet에 의해 반환되는 속성에 대한 설명을 제공합니다. 

> [!NOTE] 
> 반환된 속성 및 상태에는 전반적인 자체 호스팅 통합 런타임 및 런타임의 각 노드에 대한 정보가 포함됩니다.  

### <a name="properties"></a>properties

다음 테이블은 **각 노드**의 속성 모니터링에 대한 설명을 제공합니다.

| 자산 | 설명 | 
| -------- | ----------- | 
| Name | 자체 호스팅 통합 런타임의 이름 및 이와 연결된 노드. 노드는 자체 호스팅 통합 런타임이 설치된 온-프레미스 Windows 컴퓨터입니다. |  
| 상태 | 전반적인 자체 호스팅 통합 런타임 및 각 노드의 상태. 예를 들어 온라인/오프라인/제한 등이 있습니다. 이러한 상태에 대한 자세한 내용은 다음 섹션을 참조하세요. | 
| 버전 | 자체 호스팅 통합 런타임 및 각 노드의 버전. 자체 호스팅 통합 런타임 버전은 그룹에 있는 노드의 대다수 버전을 기반으로 결정됩니다. 자체 호스팅 통합 런타임 설정에 다른 버전의 노드가 있는 경우 논리 자체 호스팅 통합 런타임과 버전 번호가 동일한 노드만 제대로 작동합니다. 다른 버전의 노드는 제한된 모드에 있으므로 수동으로 업데이트해야 합니다(자동 업데이트가 실패할 경우에만). | 
| 사용 가능한 메모리 | 자체 호스팅 통합 런타임 노드에서 사용 가능한 메모리. 이 값은 거의 실시간 스냅숏입니다. | 
| CPU 사용률 | 자체 호스팅 통합 런타임 노드의 CPU 사용률. 이 값은 거의 실시간 스냅숏입니다. |
| 네트워킹(수신/송신) | 자체 호스팅 통합 런타임 노드의 네트워크 사용률. 이 값은 거의 실시간 스냅숏입니다. | 
| 동시 작업(실행/제한) | 각 노드에서 실행되는 작업 또는 태스크의 수입니다. 이 값은 거의 실시간 스냅숏입니다. 제한은 각 노드의 최대 동시 작업 수를 나타냅니다. 이 값은 컴퓨터 크기에 따라 정의됩니다. CPU/메모리/네트워크의 활용도가 낮지만 작업은 시간이 초과된 고급 시나리오에서 동시 작업 실행을 강화하도록 제한을 높일 수 있습니다. 이 기능은 단일 노드 자체 호스팅 통합 런타임에서도 사용할 수 있습니다. |
| 역할 | 다중 노드 자체 호스팅 통합 런타임에는 디스패처 및 작업자라는 두 가지 유형의 역할이 있습니다. 모든 노드는 작업자이며, 이는 모두 작업을 실행하는 데 사용할 수 있음을 의미합니다. 디스패처 노드는 하나만 존재합니다. 이 노드는 클라우드 서비스에서 작업/태스크를 가져와서 다른 작업자 노드에 발송하는 데 사용됩니다. 디스패처 노드는 작업자 노드이기도 합니다. |

자체 호스팅 통합 런타임에 둘 이상의 노드가 있는 경우(규모 확장 시나리오) 속성의 일부 설정이 더 적합합니다. 
  
### <a name="status-per-node"></a>상태(노드당)
다음 테이블은 자체 호스팅 통합 런타임 노드의 가능한 상태를 제공합니다.

| 상태 | 설명 |
| ------ | ------------------ | 
| 온라인 | 노드가 Data Factory 서비스에 연결되어 있습니다. |
| 오프라인 | 노드가 오프라인 상태입니다. |
| 업그레이드 중 | 노드가 자동 업데이트 중입니다. |
| 제한적 | 연결 문제로 인해 제한되는 상태입니다. 8050 HTTP 포트 문제, 서비스 버스 연결 문제 또는 자격 증명 동기화 문제 때문일 수 있습니다. |
| 비활성 | 노드의 구성이 다른 주 노드의 구성과 다릅니다. |

다른 노드에 연결할 수 없을 때 노드가 비활성 상태일 수 있습니다.

### <a name="status-overall-self-hosted-integration-runtime"></a>상태(전반적인 자체 호스팅 통합 런타임)
다음 테이블은 자체 호스팅 통합 런타임의 가능한 상태를 제공합니다. 이 상태는 런타임에 속한 모든 노드의 상태에 따라 달라집니다. 

| 상태 | 설명 |
| ------ | ----------- | 
| 등록 필요 | 자체 호스팅 통합 런타임에 등록된 노드가 아직 없습니다. |
| 온라인 | 모든 노드가 온라인 상태입니다. |
| 오프라인 | 온라인 상태인 노드가 없습니다. |
| 제한적 | 자체 호스팅 통합 런타임의 일부 노드가 정상 상태가 아닙니다. 이 상태는 일부 노드가 중단되었을 수 있다는 경고입니다. 이 상태는 디스패처/작업자 노드의 자격 증명 동기화 문제 때문일 수 있습니다. |

**Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** cmdlet을 사용하여 세부적인 자체 호스팅 통합 런타임 속성 및 cmdlet이 실행되는 동안 스냅숏 값이 포함된 JSON 페이로드를 가져옵니다.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

샘플 출력(자체 호스팅 통합 런타임과 연결된 노드가 두 개라고 가정):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임
Azure-SSIS 통합 런타임은 SSIS 패키지 실행 전용 Azure 가상 머신(또는 노드)의 완전히 관리되는 클러스터입니다. Azure Data Factory의 다른 작업은 실행하지 않습니다. 프로비저닝되면 속성을 쿼리하고 전체/노드별 상태를 모니터링할 수 있습니다.

### <a name="properties"></a>properties

| 속성/상태 | 설명 |
| --------------- | ----------- |
| CreateTime | Azure-SSIS 통합 런타임이 만들어진 UTC 시간. |
| 노드 | 노드별 상태(시작 중/사용 가능/재생 중/사용할 수 없음)와 조치 가능한 오류가 있는 Azure-SSIS 통합 런타임의 할당 된/사용 가능한 노드. |
| OtherErrors | Azure-SSIS 통합 런타임의 비 노드별 조치 가능한 오류. |
| LastOperation | 실패한 경우 조치 가능한 오류가 있는 Azure-SSIS 통합 런타임의 마지막 시작/중지 작업의 결과. |
| 시스템 상태 | Azure-SSIS 통합 런타임의 전반적인 상태(초기/시작 중/시작됨/중지 중/중지됨). |
| 위치 | Azure-SSIS 통합 런타임의 위치. |
| NodeSize | Azure-SSIS 통합 런타임의 각 노드의 크기. |
| NodeCount | Azure-SSIS 통합 런타임의 노드 수. |
| MaxParallelExecutionsPerNode | Azure-SSIS 통합 런타임의 노드당 병렬 실행 수. |
| CatalogServerEndpoint | 기존 Azure SQL Database/SSISDB를 호스트할 관리되는 인스턴스(미리 보기) 서버의 끝점. |
| CatalogAdminUserName | 기존 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버의 관리 사용자 이름. Data Factory 서비스는 이 정보를 사용하여 사용자 대신 SSISDB를 준비하고 관리합니다. |
| CatalogAdminPassword | 기존 Azure SQL Database/관리되는 인스턴스(미리 보기) 서버의 관리자 암호. |
| CatalogPricingTier | 기존 Azure SQL Database 서버에서 호스트하는 SSISDB의 가격 책정 계층.  SSISDB를 호스트하는 Azure SQL 관리되는 인스턴스(미리 보기)에는 해당되지 않습니다. |
| VNetId | Azure-SSIS 통합 런타임이 조인할 가상 네트워크(VNet) 리소스 ID. |
| 서브넷 | Azure-SSIS 통합 런타임이 조인할 서브넷 이름. |
| ID | Azure-SSIS 통합 런타임의 리소스 ID. |
| 유형 | Azure-SSIS 통합 런타임의 유형(관리되는/자체 호스팅). |
| ResourceGroupName | 데이터 팩터리 및 Azure-SSIS 통합 런타임이 만들어진 Azure 리소스 그룹의 이름입니다. |
| DataFactoryName | Azure Data Factory의 이름. |
| Name | Azure-SSIS 통합 런타임의 이름. |
| 설명 | Azure-SSIS 통합 런타임에 대한 설명. |

  
### <a name="status-per-node"></a>상태(노드당)

| 상태 | 설명 |
| ------ | ----------- | 
| 시작 중 | 이 노드가 준비 중입니다. |
| 사용 가능 | 이 노드는 SSIS 패키지를 배포/실행할 준비가 되었습니다. |
| 재생 중 | 이 노드는 복구 중/다시 시작 중입니다. |
| 사용할 수 없음 | 이 노드는 SSIS 패키지를 배포/실행할 준비가 되지 않았으며 해결할 수 있는 조치 가능한 오류/문제가 있습니다. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>상태(전반적인 Azure-SSIS 통합 런타임)

| 전반적인 상태 | 설명 | 
| -------------- | ----------- | 
| Initial | Azure-SSIS 통합 런타임의 노드가 할당/준비되지 않았습니다. | 
| 시작 중 | Azure-SSIS 통합 런타임의 노드가 할당/준비 중이고 청구가 시작되었습니다. |
| Started | Azure-SSIS 통합 런타임의 노드가 할당/준비되었으며 SSIS 패키지를 배포/실행할 준비가 되었습니다. |
| 중지 중  | Azure-SSIS 통합 런타임의 노드가 해제되고 있습니다. |
| 중지됨 | Azure-SSIS 통합 런타임의 노드가 해제되었고 청구가 중단되었습니다. |

Azure-SSIS 통합 런타임에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL 관리되는 인스턴스(미리 보기)를 사용하고 IR을 VNet에 조인하는 지침을 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
- [Azure-SSIS IR을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서는 Azure-SSIS IR을 Azure 가상 네트워크(VNet)에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 VNet에 조인할 수 있도록 VNet을 구성하는 단계도 제공합니다. 

## <a name="next-steps"></a>다음 단계
다른 방식으로 파이프라인을 모니터링하려면 다음 문서를 참조하세요. 

- [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor를 사용하여 Data Factory 파이프라인 모니터링](monitor-using-azure-monitor.md)