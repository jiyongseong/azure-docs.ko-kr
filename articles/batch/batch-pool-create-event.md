---
title: Azure Batch 풀 만들기 이벤트 | Microsoft Docs
description: Batch 풀 만들기 이벤트에 대한 참조입니다.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: bf7dfc2600c3d94faeb8d03561f6f2b30a0ee2d2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="pool-create-event"></a>풀 만들기 이벤트

 이 이벤트는 풀이 생성되면 내보내집니다. 로그의 내용은 풀에 대한 일반 정보를 노출합니다. 풀의 대상 크기가 0 계산 노드보다 큰 경우 이 이벤트 직 후 풀 크기 조정 시작 이벤트가 수행됩니다.

 다음 예에서는 CloudServiceConfiguration 속성을 사용하여 만든 풀의 풀 만들기 이벤트 본문을 보여줍니다.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|요소|형식|메모|
|-------------|----------|-----------|
|id|문자열|풀의 ID입니다.|
|displayName|string|풀의 표시 이름입니다.|
|vmSize|문자열|풀에 포함된 가상 머신의 크기입니다. 풀에 포함된 모든 가상 머신의 크기는 같습니다. <br/><br/> Cloud Services 풀(cloudServiceConfiguration을 사용하여 만든 풀)에 사용 가능한 가상 머신 크기에 대한 자세한 내용은 [Cloud Services에 적합한 크기](http://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/)를 참조하세요. Batch는 `ExtraSmall`을 제외한 모든 Cloud Services VM 크기를 지원합니다.<br/><br/> Virtual Machines Marketplace(virtualMachineConfiguration을 사용하여 만든 풀)의 이미지를 사용한 풀에 사용 가능한 VM 크기에 대한 자세한 내용은 [Virtual Machines에 적합한 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/)(Linux) 또는 [Virtual Machines에 적합한 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)(Windows)를 참조하세요. 배치는 `STANDARD_A0`및 프리미엄 저장소(`STANDARD_GS`, `STANDARD_DS` 및 `STANDARD_DSV2` 시리즈) 크기를 제외한 모든 Azure VM 크기를 지원합니다.|
|[cloudServiceConfiguration](#bk_csconf)|복합 형식|풀의 클라우드 서비스 구성입니다.|
|[virtualMachineConfiguration](#bk_vmconf)|복합 형식|풀의 가상 머신 구성입니다.|
|[networkConfiguration](#bk_netconf)|복합 형식|풀의 네트워크 구성입니다.|
|resizeTimeout|Time|풀에서 마지막 크기 조정 작업에 지정된 풀에 계산 노드를 할당하는 데 정해진 제한 시간입니다.  (풀을 만들 때 초기 크기 조정은 크기 조정으로 간주됩니다.)|
|targetDedicated|Int32|풀에 대해 요청된 계산 노드 수입니다.|
|enableAutoScale|Bool|풀 크기가 시간이 지남에 따라 자동으로 조정되는지 여부를 지정합니다.|
|enableInterNodeCommunication|Bool|풀에 노드 간 직접 통신이 설정되어 있는지 여부를 지정합니다.|
|isAutoPool|Bool|풀이 작업의 자동 풀 메커니즘을 통해 만들어졌는지 여부를 지정합니다.|
|maxTasksPerNode|Int32|풀의 단일 계산 노드에서 동시에 실행할 수 있는 최대 태스크 수입니다.|
|vmFillType|문자열|Batch 서비스는 풀의 계산 노드 간에 태스크를 분배하는 방법을 정의합니다. 유효한 값은 Spread 또는 Pack입니다.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|요소 이름|유형|메모|
|------------------|----------|-----------|
|osFamily|문자열|풀의 가상 머신에 설치할 Azure 게스트 OS 제품군입니다.<br /><br /> 가능한 값은 다음과 같습니다.<br /><br /> **2** – OS 제품군 2이며, Windows Server 2008 R2 SP1에 해당합니다.<br /><br /> **3** – OS 제품군 3이며, Windows Server 2012에 해당합니다.<br /><br /> **4** – OS 제품군 4이며, Windows Server 2012 R2에 해당합니다.<br /><br /> 자세한 내용은 [Azure 게스트 OS 릴리스](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)를 참조하세요.|
|targetOSVersion|문자열|풀의 가상 머신에 설치할 Azure 게스트 OS 버전입니다.<br /><br /> 기본값은 지정된 제품군의 최신 운영 체제 버전을 지정하는 **\***입니다.<br /><br /> 기타 허용된 값은 [Azure 게스트 OS 릴리스](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)를 참조하세요.|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|요소 이름|유형|메모|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|복합 형식|사용할 플랫폼 또는 Marketplace 이미지에 대한 정보를 지정합니다.|
|nodeAgentSKUId|문자열|계산 노드에서 프로비전되는 Batch 노드 에이전트의 SKU입니다.|
|[windowsConfiguration](#bk_winconf)|복합 형식|가상 머신의 Windows 운영 체제 설정을 지정합니다. imageReference가 Linux OS 이미지를 참조하는 경우 이 속성을 지정해서는 안 됩니다.|

###  <a name="bk_imgref"></a> imageReference

|요소 이름|유형|메모|
|------------------|----------|-----------|
|publisher|문자열|이미지의 게시자입니다.|
|offer|문자열|이미지 제안입니다.|
|sku|문자열|이미지의 SKU입니다.|
|버전|문자열|이미지의 버전입니다.|

###  <a name="bk_winconf"></a> windowsConfiguration

|요소 이름|유형|메모|
|------------------|----------|-----------|
|enableAutomaticUpdates|BOOLEAN|가상 머신에 자동 업데이트가 활성화되었는지 여부를 나타냅니다. 이 속성을 지정하지 않을 경우 기본값은 true입니다.|

###  <a name="bk_netconf"></a> networkConfiguration

|요소 이름|유형|메모|
|------------------|--------------|----------|
|subnetId|문자열|풀의 계산 노드가 생성된 서브넷의 리소스 식별자를 지정합니다.|
