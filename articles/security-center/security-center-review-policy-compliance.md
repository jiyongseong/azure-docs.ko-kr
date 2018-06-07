---
title: Azure REST API를 사용한 보안 센터 정책 준수 검토 | Microsoft Docs
description: 현재의 보안 센터 정책 준수를 검토하려면 Azure REST API를 사용하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077768"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>REST API를 사용하여 보안 센터 정책 준수 검토

보안 센터는 정의된 보안 정책에 대해 Azure 리소스의 유효성을 정기적으로 검사합니다. 보안 센터는 사용자의 응용 프로그램에서 규정 준수를 검토할 수 있도록 REST API를 제공하며 서비스를 직접 쿼리하거나 다른 응용 프로그램으로 JSON 결과를 가져올 수 있습니다. 

여기에서 구독과 연결된 모든 Azure 리소스에서 권장 사항의 현재 집합을 검색하는 것을 배웁니다.

권장 사항의 현재 집합을 검색하려면:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>요청 빌드  

`{subscription-id}` 매개 변수는 필수이며 정책을 정의하는 Azure 구독에 대한 구독 ID를 포함해야 합니다. 구독이 여러 개인 경우 [여러 구독으로 작업](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)을 참조합니다.  

`api-version` 매개 변수는 필수 사항입니다. 이때 이러한 엔드포인트는 `api-version=2015-06-01-preview`에 대해서만 지원됩니다. 

다음과 같은 헤더가 필요합니다. 

|요청 헤더|설명|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [액세스 토큰](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |  

## <a name="response"></a>response  

상태 코드 200(OK)은 Azure 리소스 보호를 위해 권장되는 작업 목록이 포함된 성공적인 응답에 대해 반환됩니다.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

**값**의 각 항목은 권장 사항을 나타냅니다.

|응답 속성|설명|
|----------------|----------|
|**state** | 권장 사항이 `active` 또는 `resolved`인지 나타냅니다. |
|**creationTimeUtc** | 권장 사항이 만들어진 때를 보여주는 UTC 날짜 및 시간입니다. |
|**lastStateChangeUtc** | 해당되는 경우 마지막 상태 변경의 UTV 날짜 및 시간입니다. |
|**securityTaskParameters** | 권장 사항에 대해 자세히 설명하고 속성은 기본 권장 사항에 따라 달라집니다. |
||
  
현재 지원되는 권장 사항은 [보안 권장 사항 구현](https://docs.microsoft.com/azure/security-center/security-center-recommendations)을 참조합니다.

다른 상태 코드는 오류 조건을 나타냅니다. 이런 경우 응답 개체에는 요청이 실패한 이유를 해명하는 설명이 포함됩니다.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>예제 응답  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

이 응답에서는 두 가지 권장 사항을 보여주며, 목록의 각 항목은 특정 권장 사항에 해당합니다. 첫 번째는 Linux 가상 머신에 저장소를 암호화하도록 권장하며, 두 번째는 SQL 서버에 대한 감사를 사용하도록 제안합니다.

권장 사항은 사용하도록 설정한 정책에 따라 달라집니다. 현재 사용 가능한 권장 사항 등을 자세히 알려면 [Azure Security Center에서 보안 권장 사항 관리](https://docs.microsoft.com/azure/security-center/security-center-recommendations)를 참조하세요.


## <a name="see-also"></a>참고 항목  
- [보안 정책 설정](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Azure 보안 리소스 공급자 REST API](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Azure REST API 시작하기](https://docs.microsoft.com/rest/api/azure/)   
- [Azure 보안 센터 PowerShell 모듈](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
