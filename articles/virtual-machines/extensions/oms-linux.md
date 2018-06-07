---
title: Linux용 OMS Azure 가상 머신 확장 | Microsoft Docs
description: 가상 머신 확장을 사용하여 Linux 가상 머신에 OMS 에이전트를 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: danis
ms.openlocfilehash: dcc5637b159341fc4b6cc8130b1807c8a2f604fc
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261828"
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Linux용 OMS 가상 머신 확장

## <a name="overview"></a>개요

Log Analytics는 클라우드와 온-프레미스 자산에서 모니터링, 경고 및 경고 수정 기능을 제공합니다. Linux용 OMS 에이전트 가상 머신 확장은 Microsoft에서 게시 및 지원합니다. 확장 버전은 Azure Virtual Machines에 OMS 에이전트를 설치하고 기존 Log Analytics 작업 영역에 Virtual Machines를 등록합니다. 이 문서에서는 지원되는 플랫폼, 구성 및 Linux용 OMS 가상 머신 확장에 대한 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

OM 에이전트 확장은 다음 Linux 배포판에 대해 실행할 수 있습니다.

| 배포 | 버전 |
|---|---|
| CentOS Linux | 5, 6 및 7(x86/x64) |
| Oracle Linux | 5, 6 및 7(x86/x64) |
| Red Hat Enterprise Linux Server | 5, 6 및 7(x86/x64) |
| Debian GNU/Linux | 6, 7 및 8(x86/x64) |
| Ubuntu | 12.04 LTS, 14.04 LTS, 16.04 LTS(x86/x64) |
| SUSE Linux Enterprise Server | 11 및 12(x86/x64) |

### <a name="agent-and-vm-extension-version"></a>에이전트 및 VM 확장 버전
다음 표에서는 각 릴리스에서 OMS VM 확장의 각 버전과 OMS Agent 번들에 대한 매핑을 제공합니다. OMS 에이전트 번들 버전에 대한 릴리스 노트 링크가 포함되어 있습니다. 릴리스 정보는 버그 수정에 대한 세부 정보 및 지정된 에이전트 릴리스에 사용 가능한 새로운 기능을 포함합니다.  

| OMS Linux VM 확장 버전 | OMS Agent 번들 버전 | 
|--------------------------------|--------------------------|
| 1.6.42.0 | [1.6.42.0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center는 자동으로 OMS 에이전트를 프로비전하고 Azure 구독에서 ASC가 만든 기본 Log Analytics 작업 영역에 연결합니다. Azure Security Center를 사용하는 경우 이 문서의 단계를 실행하지 마세요. 이렇게 하면 구성된 작업 영역을 덮어쓰고 Azure Security Center와의 연결을 끊습니다.

### <a name="internet-connectivity"></a>인터넷 연결

Linux용 OMS 에이전트 확장은 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 OMS 에이전트 확장에 대한 스키마를 보여줍니다. 이 확장은 대상 Log Analytics 작업 영역에서 작업 영역 ID와 작업 영역 키가 필요하며, 이러한 값은 Azure Portal의 [Log Analytics 작업 영역에서 확인할 수 있습니다.](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) 작업 영역 키는 중요한 데이터로 처리되므로 보호되는 설정에 저장됩니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다. **workspaceId** 및 **workspaceKey**는 대/소문자를 구분합니다.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>속성 값

| Name | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| 형식 | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId(예) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey(예) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 템플릿은 Log Analytics에 온보딩과 같이 배포 후 구성이 필요한 하나 이상의 Virtual Machines를 배포하는 경우에 이상적입니다. OMS 에이전트 VM 확장을 포함하는 샘플 Resource Manager 템플릿은 [Azure 빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)에서 찾을 수 있습니다. 

가상 머신 확장에 대한 JSON 구성은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON 구성의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources)을 참조하세요. 

다음 예제에서는 VM 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

템플릿의 루트에 JSON 확장을 배치하면 리소스 이름에 부모 가상 머신에 대한 참조가 포함되며 형식은 중첩된 구성을 반영합니다.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 OMS 에이전트 VM 확장을 기존 가상 머신에 배포할 수 있습니다. *workspaceId* 및 *workspaceKey*를 Log Analytics의 항목으로 바꿉니다. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>오류 코드 및 해당 의미

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 9 | 조기 호출 사용 설정 | [Azure Linux 에이전트를 사용 가능한 최신 버전으로 업데이트](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)합니다. |
| 10 | VM이 Log Analytics 작업 영역에 이미 연결됨 | VM을 확장 스키마에 지정된 작업 영역에 연결하려면 공용 설정에서 stopOnMultipleConnections를 false로 설정하거나 이 속성을 제거합니다. 이 VM은 연결된 각 작업 영역에 대해 한 번만 비용이 청구됩니다. |
| 11 | 확장에 잘못된 구성이 제공됨 | 이전 예제에 따라 배포에 필요한 모든 속성 값을 설정합니다. |
| 12 | dpkg 패키지 관리자가 잠겨 있음 | 컴퓨터의 모든 dpkg 업데이트 작업이 완료되었는지 확인하고 다시 시도합니다. |
| 17 | OMS 패키지 설치 오류 | 
| 19 | OMI 패키지 설치 오류 | 
| 20 | SCX 패키지 설치 실패 |
| 51 | 이 확장이 VM의 운영 체제에서 지원되지 않음 | |
| 55 | Microsoft Operations Management Suite 서비스에 연결할 수 없음 | 시스템에서 인터넷에 액세스할 수 있는지 또는 유효한 HTTP 프록시가 제공되었는지 확인합니다. 또한 작업 영역 ID가 올바른지 확인합니다. |

추가 문제 해결 정보는 [MS-Agent-for-Linux 문제 해결 가이드](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md)에서 확인할 수 있습니다.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
