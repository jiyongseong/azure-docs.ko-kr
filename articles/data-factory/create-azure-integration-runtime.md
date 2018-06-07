---
title: Azure Data Factory에서 Azure 통합 런타임 만들기 | Microsoft Docs
description: Azure Data Factory에서 데이터를 복사하고 변환 작업을 디스패치하는 Azure 통합 런타임을 만드는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: db757c5a8a1b51278ada24a5ff50a2da1b357397
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure 통합 런타임을 만들고 구성하는 방법
IR(통합 런타임)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 계산 인프라입니다. IR에 대한 자세한 내용은 [통합 런타임](concepts-integration-runtime.md)을 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory V1 설명서](v1/data-factory-introduction.md)를 참조하세요.

Azure IR은 완전히 관리되는 계산을 제공하여 기본적으로 데이터 이동을 수행하고 HDInsight와 같은 계산 서비스로 데이터 변환 작업을 디스패치합니다. Azure 환경에서 호스트되며 액세스 가능한 공용 끝점을 통해 공용 네트워크 환경의 리소스에 연결할 수 있습니다.

이 문서에서는 Azure 통합 런타임을 만들고 구성할 수 있는 방법을 소개합니다. 

## <a name="default-azure-ir"></a>기본 Azure IR
기본적으로 각 데이터 팩터리에는 백 엔드에 클라우드 데이터 저장소의 작업 및 공용 네트워크의 계산 서비스를 지원하는 Azure IR이 있습니다. 해당 Azure IR 위치는 자동으로 확인됩니다. **connectVia** 속성이 연결된 서비스 정의에 지정되지 않은 경우 기본 Azure IR이 사용됩니다. IR의 위치를 명시적으로 정의하려는 경우 또는 관리 목적으로 다른 IR에 대한 작업 실행을 가상으로 그룹화하려는 경우에만 Azure IR을 명시적으로 만들어야 합니다. 

## <a name="create-azure-ir"></a>Azure IR 만들기
통합 런타임은 **Set-AzureRmDataFactoryV2IntegrationRuntime** PowerShell cmdlet을 사용하여 만들 수 있습니다 Azure IR을 만들려면 명령에 이름, 위치 및 형식을 지정합니다. 다음은 위치가 "West Europe"으로 설정된 Azure IR을 만드는 샘플 명령입니다.

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR의 경우 형식은 **Managed**로 설정되어야 합니다. 계산 세부 정보는 클라우드에서 탄력적으로 완전히 관리되므로 지정할 필요가 없습니다. Azure-SSIS IR을 만들려는 경우 노드 크기 및 노드 개수와 같은 계산 세부 정보를 지정합니다. 자세한 내용은 [Azure SSIS IR 만들기 및 구성](create-azure-ssis-integration-runtime.md)을 참조하세요.

기존 Azure IR을 Set-AzureRmDataFactoryV2IntegrationRuntime PowerShell cmdlet을 사용하여 해당 위치를 변경하도록 구성할 수 있습니다. Azure IR의 위치에 대한 자세한 내용은 [통합 런타임 소개](concepts-integration-runtime.md)를 참조하세요.

## <a name="use-azure-ir"></a>Azure IR 사용

Azure IR을 만든 후 연결된 서비스 정의에서 참조할 수 있습니다. 다음은 Azure Storage 연결된 서비스에서 이전에 만든 Azure 통합 런타임을 참조할 수 있는 방법에 대한 샘플입니다.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>다음 단계
다른 형식의 통합 런타임을 만드는 방법은 다음 문서를 참조하세요.

- [자체 호스팅된 통합 런타임](create-self-hosted-integration-runtime.md)
- [Azure SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)
 
