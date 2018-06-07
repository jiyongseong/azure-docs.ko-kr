---
title: Azure Resource Manager를 사용한 배포 작업 | Microsoft Docs
description: 포털, PowerShell, Azure CLI 및 REST API를 사용하여 Azure Resource Manager 배포 작업을 확인하는 방법을 설명합니다.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 04/23/2018
ms.author: tomfitz
ms.openlocfilehash: 523ea3bf5d41231ab3281f9d8eb1fac8c3dfb55f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 배포 작업 보기

Azure 포털을 통해 배포에 대한 작업을 볼 수 있습니다. 배포 중에 오류가 나타날 때 작업을 보는 데 가장 많은 관심을 가질 수 있으므로 이 문서에서는 실패한 작업을 보는 것에 대해 중점적으로 설명합니다. 포털은 쉽게 오류를 찾고 잠재적 해결 방법을 확인할 수 있는 인터페이스를 제공합니다.

감사 로그 또는 배포 작업을 확인하여 배포 문제를 해결할 수 있습니다. 이 문서에서는 두 가지 방법을 모두 보여줍니다. 특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="portal"></a>포털
배포 작업을 확인하려면 다음 단계를 사용합니다.

1. 배포에 관련된 리소스 그룹에 대해 마지막 배포의 상태를 확인합니다. 이 상태를 선택하여 자세한 내용을 확인할 수 있습니다.
   
    ![배포 상태](./media/resource-manager-deployment-operations/deployment-status.png)
2. 최근 배포 기록이 표시됩니다. 실패한 배포를 선택합니다.
   
    ![배포 상태](./media/resource-manager-deployment-operations/select-deployment.png)
3. 배포에 실패한 이유에 대한 설명을 보려면 링크를 선택하세요. 아래 이미지에서 DNS 레코드는 고유하지 않습니다.  
   
    ![실패한 배포 보기](./media/resource-manager-deployment-operations/view-error.png)
   
    이 오류 메시지는 문제 해결을 시작하는 데 충분합니다. 그러나 어떤 작업이 완료되었는지에 대한 추가 정보가 필요한 경우 다음 단계에 표시된 대로 작업을 볼 수 있습니다.
4. 모든 배포 작업을 볼 수 있습니다. 보다 자세한 정보를 확인하려면 원하는 작업을 선택합니다.
   
    ![작업 보기](./media/resource-manager-deployment-operations/view-operations.png)
   
    이 경우 저장소 계정, 가상 네트워크 및 가용성 집합이 성공적으로 만들어진 것을 확인할 수 있습니다. 공용 IP 주소가 실패했고 다른 리소스를 시도하지 않았습니다.
5. **이벤트**를 선택하여 배포에 대한 이벤트를 볼 수 있습니다.
   
    ![이벤트 보기](./media/resource-manager-deployment-operations/view-events.png)
6. 배포에 대한 모든 이벤트가 표시되면 하나 이상의 세부 정보를 선택합니다. 상관 관계 ID도 확인합니다. 이 값은 배포 문제를 해결하기 위해 기술 지원부와 협력할 때 유용할 수 있습니다.
   
    ![이벤트 보기](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. 배포의 전반적인 상태를 가져오려면 **Get-AzureRmResourceGroupDeployment** 명령을 사용합니다. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   또는 실패한 배포에 대해서만 결과를 필터링할 수 있습니다.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. 각 배포에는 여러 작업이 포함되어 있습니다. 각 작업은 배포 프로세스의 단계를 나타냅니다. 배포에서 무엇이 잘못 되었는지 검색하려면 일반적으로 배포 작업에 대한 세부 정보를 확인해야 합니다. **Get-AzureRmResourceGroupDeploymentOperation**을 사용하여 작업의 상태를 확인할 수 있습니다.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    여러 작업이 각각 다음과 같은 형식으로 반환됩니다.

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. 실패한 작업에 대한 자세한 정보를 얻으려면 상태가 **Failed** 인 작업에 대한 속성을 검색합니다.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    모든 실패한 작업이 각각 다음과 같은 형식으로 반환됩니다.

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    작업의 serviceRequestId 및 trackingId를 확인하세요. serviceRequestId는 기술 지원과 함께 배포 문제를 해결할 때 유용할 수 있습니다. 특정 작업에 집중하기 위해 trackingId는 다음 단계에서 사용할 예정입니다.
4. 특정 실패한 작업에 대한 상태 메시지를 얻으려면 다음 명령을 사용합니다.

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    반환하는 내용은 다음과 같습니다.

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Azure의 모든 배포 작업에는 요청 및 응답 콘텐츠가 포함됩니다. 요청 콘텐츠는 배포하는 동안 Azure에 보낸 콘텐츠입니다(예: VM, OS 디스크 및 기타 리소스). 응답 콘텐츠는 Azure가 배포 요청에서 다시 보낸 콘텐츠입니다. 배포하는 동안 **DeploymentDebugLogLevel** 매개 변수를 사용하여 요청 및/또는 응답을 로그에 보존하도록 지정할 수 있습니다. 

  로그에서 해당 정보를 가져오고 다음 PowerShell 명령을 사용하여 로컬에 저장합니다.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. **azure group deployment show** 명령을 사용하여 배포의 전반적인 상태를 가져옵니다.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. 반환되는 값 중 하나는 **correlationId**입니다. 이 값은 관련 이벤트를 추적하는 데 사용되며 기술 지원과 함께 배포 문제를 해결할 때 유용할 수 있습니다.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. 배포의 작업을 보려면 다음을 사용합니다.

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST (영문)

1. [템플릿 배포에 대한 정보 가져오기](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) 작업을 사용하여 배포에 대한 정보를 가져옵니다.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    응답에서 **provisioningState** , **correlationId** 및 **error** 요소에 특히 유의합니다. **correlationId** 는 관련 이벤트를 추적하는 데 사용되며 기술 지원과 함께 배포 문제를 해결할 때 유용할 수 있습니다.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. [모든 템플릿 배포 작업 나열](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List)을 사용하여 배포 작업에 대한 정보를 가져오세요. 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    응답에는 배포 중에 **debugSetting** 속성에 지정하는 설정을 기준으로 요청 및/또는 응답 정보가 포함됩니다.

  ```json
  {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
  }
  ```


## <a name="next-steps"></a>다음 단계
* 특정 배포 오류에 대한 도움말은 [Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생한 일반적인 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* 활동 로그를 사용하여 다른 유형의 작업을 모니터링하는 방법에 대해 알아보려면 [활동 로그를 보고 Azure 리소스 관리](resource-group-audit.md)를 참조하세요.
* 실행하기 전에 배포의 유효성을 검사하려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

