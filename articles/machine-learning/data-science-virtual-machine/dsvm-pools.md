---
title: 데이터 과학 가상 머신 풀 - Azure | Microsoft Docs
description: 팀의 공유 리소스로 데이터 과학 VM 풀 배포
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 625154378fecbd4d45904fa5669adb866fc9487c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>데이터 과학 가상 머신의 공유 풀 만들기

이 문서에서는 팀에서 사용하기 위한 DSVM(데이터 과학 가상 머신)의 공유 풀을 만들 수 있는 방법을 설명합니다. 공유 풀을 사용하는 경우 이점은 리소스 사용률이 개선되어 공유 및 공동 작업이 원활하게 이루어지며 IT에서 DSVM 리소스를 더 효과적으로 관리할 수 있다는 것입니다. 

DSVM의 풀을 만드는 데 사용할 수 있는 많은 방법과 다양한 기술이 있습니다.  다음은 주요 시나리오입니다.

* 일괄 처리용 풀
* 대화형 VM용 풀

## <a name="batch-processing-pool"></a>일괄 처리 풀
DSVM의 풀을 주로 오프라인에서 작업을 일괄 처리로 실행하기 위해 설정하려는 경우, [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) 서비스 또는 [Azure Batch](https://docs.microsoft.com/azure/batch/)를 사용할 수 있습니다. 

### <a name="azure-batch-ai"></a>Azure Batch AI
DSVM의 Ubuntu 판은 Azure Batch AI에서 이미지 중 하나로 지원됩니다. Azure Batch AI 클러스터를 만드는 Azure CLI 또는 Python SDK에서 ```image``` 파라미터를 지정하고 이를 ```UbuntuDSVM```으로 설정할 수 있습니다. GPU 기반 인터페이스 또는 CPU 전용 인스턴스, CPU 수, 메모리 등 Azure에서 사용할 수 있는 [폭 넓은 VM 인스턴스 선택 항목](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에서 원하는 어떤 종류의 처리 노드도 선택할 수 있습니다. GPU 기반 노드를 포함한 Batch AI에서 Ubuntu DSVM 이미지를 사용하는 경우, 모든 필요한 GPU 드라이버와 딥 러닝 프레임워크는 미리 설치되어 있으므로 일괄 처리 노드를 준비할 때 많은 시간이 절약됩니다. 사실 Ubuntu DSVM에서 대화형으로 개발하는 경우, Batch AI 노드가 환경과 똑같은 설정 및 구성이라는 것을 확인할 수 있습니다. 일반적으로 Batch AI 클러스터를 만들 때 모든 노드에서 마운트하고 데이터 입력과 출력 및 일괄 처리 작업 코드 / 스크립트 저장에 사용되는 파일 공유도 만들 수 있습니다. 

Batch AI 클러스터가 만들어진 후 같은 CLI 또는 Python SDK를 사용하여 실행할 작업을 제출할 수 있습니다. 일괄 처리 작업을 술행하는 데 사용되는 시간에 대해서만 지불하면 됩니다. 

#### <a name="more-information"></a>자세한 정보
* [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli)를 사용하여 Batch AI 관리 단계별 연습
* [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python)을 사용하여 Batch AI 관리 단계별 연습
* [Batch AI 레시피](https://github.com/Azure/BatchAI)를 사용하여 Batch AI를 통해 다양한 AI/딥 러닝 프레임워크를 사용하는 방법을 보여 줄 수 있습니다.

## <a name="interactive-vm-pool"></a>대화형 VM 풀

전체 AI / 데이터 과학 팀에서 공유되는 대화형 DSVM의 풀을 사용하면 각 사용자 집합에 대해 전용 인스턴스를 가질 필요 없이 사용자가 DSVM의 사용 가능한 인스턴스에 로그인할 수 있습니다. 이렇게 하면 가용성 개선 및 리소스의 더 효과적인 이용에 도움이 됩니다. 

대화형 VM 풀을 만드는 데 사용하는 기술은 동일하고 부하 분산된 자동 크기 조정 VM의 그룹을 만들어 관리할 수 있는 [Azure VMSS(Virtual Machine Scale Sets)](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)입니다. 사용자는 주 풀의 IP 또는 DNS 주소에 로그인합니다. 확장 집합은 세션을 자동으로 확장 집합의 사용 가능한 DSVM으로 경로 설정합니다. 사용자는 자신이 로그인한 VM과 상관없이 유사한 환경을 원하므로, 확장 집합에 있는 VM의 모든 인스턴스는 Azure Files 또는 NFS Share 같은 공유 네트워크 드라이브를 마운트합니다. 사용자의 공유 작업 영역은 일반적으로 각 인스턴스에 마운트된 공유 파일 저장소에 유지됩니다. 

Ubuntu DSVM 인스턴스를 통해 VM 확장 집합을 만드는 샘플 Azure Resource Manager 템플릿은 [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)에서 찾을 수 있습니다. Azure Resource Manager 템플릿 [매개 변수 파일](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)도 같은 위치에 제공됩니다. 

Azure Resource Manager 템플릿에서 Azure CLI를 사용하여 매개 변수 파일에 적합한 값을 지정하여 VM 확장 집합을 만들 수 있습니다. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
위의 명령에서는 사용자가 각 VM에 마운트할 저장소 계정에 대한 자격 증명과 함께 VM 확장 집합에 대한 사용자의 인스턴스에 지정된 값, VM 인스턴스 수, Azure 파일에 대한 포인터를 포함한 매개 변수 파일의 복사본을 가지고 있다고 가정합니다. 매개 변수 파일은 위의 명령에서 로컬로 참조됩니다. 매개 변수를 인라인으로 전달하거나 스크립트에서 그에 대한 프롬프트를 표시할 수도 있습니다.  

위의 템플릿은 Ubuntu DSVM의 백 엔드 풀로 설정된 프런트 엔드 VM 확장 집합에서 SSH 및 Jupyterhub 포트를 사용할 수 있습니다.  사용자는 일상적인 방법으로 SSH 또는 JupyterHub의 VM에 로그인하면 됩니다. VM 인스턴스는 동적으로 규모 확장 또는 축소할 수 있으므로 모든 상태는 마운트된 Azure Files 공유에 저장되어야 합니다. 같은 방식을 사용하여 Windows DSVM의 풀을 만들 수 있습니다. 

[Azure Files를 마운트하는 스크립트](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)는 Azure DataScienceVM Github에서도 사용할 수 있습니다. 매개 변수 파일의 지정된 마운트 지점에 Azure Files를 마운트할 뿐만 아니라 초기 사용자의 홈 디렉터리에 마운트된 드라이브에 대한 추가 소프트 링크도 만들며, 공유 Azure Files 내의 사용자별 노트북 디렉터리는 ```$HOME/notebooks/remote``` 디렉터리에 소프트 링크되므로 사용자가 자신의 Jupyter 노트북을 액세스, 실행 및 저장할 수 있습니다.  VM에 추가 사용자를 만들 때에도 같은 규칙을 사용하여 각 사용자의 Jupyter 작업 영역이 공유 Azure 파일을 가리키게 할 수 있습니다. 

Azure VM 확장 집합은 VM이 전혀 사용되지 않을 때 클라우드 하드웨어 사용 비용을 절약하기 위해 0 인스턴스로 규모 축소하는 기능을 포함하여 추가 인스턴스를 만드는 시기 및 인스턴스를 규모 축소하는 환경에 대한 규칙을 설정할 수 있는 자동 크기 조정을 지원합니다. VM 확장 집합에 대한 문서 페이지는 [자동 크기 조정](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)에 대한 자세한 단계를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [공통 ID 설정](dsvm-common-identity.md)
* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)















