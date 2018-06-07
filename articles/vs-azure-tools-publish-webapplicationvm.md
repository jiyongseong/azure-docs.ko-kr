---
title: Publish-WebApplicationVM | Microsoft Docs
description: 가상 머신에 웹 응용 프로그램을 배포하는 방법을 알아봅니다. 없는 경우 이 스크립트는 Azure 구독에 필요한 리소스를 만듭니다.
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 3fdd11387096d95359fb5f578ca64720f2182c45
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publish-WebApplicationVM (Windows PowerShell 스크립트)
가상 머신에 웹 응용 프로그램을 배포합니다. 없는 경우 스크립트는 Azure 구독에 필요한 리소스를 만듭니다.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>구성
배포의 세부 정보를 설명하는 JSON 구성 파일에 대한 경로입니다.

| Aliases | 없음 |
| --- | --- |
| Required? |true |
| Position |named |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

### <a name="subscriptionname"></a>SubscriptionName
가상 머신을 만들려는 Azure 구독의 이름입니다.

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position |named |
| 기본값 |구독 파일의 첫 번째 구독을 사용합니다. |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

### <a name="webdeploypackage"></a>WebDeployPackage
가상 컴퓨터에 게시하는 웹 배포 패키지에 대한 경로입니다. Visual Studio에서 웹 게시 마법사를 사용하여 이 패키지를 만들 수 있습니다. [방법: Visual Studio에서 웹 배포 패키지 만들기](https://msdn.microsoft.com/library/dd465323.aspx)를 참조하세요.

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position |named |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

### <a name="allowuntrusted"></a>AllowUntrusted
True인 경우 신뢰할 수 있는 루트 인증 기관에서 서명되지 않은 인증서 사용을 허용합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position |named |
| 기본값 |false |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

### <a name="vmpassword"></a>VMPassword
가상 머신 계정에 대한 자격 증명입니다. 예: -VMPassword @{Name = "admin"; Password = "password"}

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position |named |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Azure에서 SQL 데이터베이스에 대한 자격 증명입니다. 예: -DatabaseServerPassword @{Name = "admin"; Password = "password"}

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position |named |
| 기본값 |없음 |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
True이면 스크립트에서 출력 스트림으로 메시지를 프린트합니다.

| Aliases | 없음 |
| --- | --- |
| Required? |false |
| Position |named |
| 기본값 |false |
| Accept Pipeline Input? |false |
| Accept Wildcard Characters? |false |

## <a name="remarks"></a>설명
스크립트를 사용하여 개발 및 테스트 환경을 만드는 방법에 대한 전체 설명은 [Windows PowerShell 스크립트를 사용하여 개발 및 테스트 환경에 게시](vs-azure-tools-publishing-using-powershell-scripts.md)를 참조하세요.

JSON 구성 파일은 배포될 내용의 세부 정보를 지정합니다. 프로젝트를 만들 때 지정한 정보(예: 가상 머신의 이름, 선호도 그룹, VHD 이미지, 크기)를 포함합니다. 또한 가상 컴퓨터의 끝점, 프로비전할 데이터베이스(있는 경우), 웹 배포 매개 변수를 포함합니다. 다음 코드에서는 JSON 구성 파일을 예로 보여줍니다.

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

프로비전한 내용을 변경하도록 JSON 구성 파일을 편집할 수 있습니다. 가상 머신 및 클라우드 서비스는 필요하지만 데이터베이스 섹션은 선택 사항입니다.

