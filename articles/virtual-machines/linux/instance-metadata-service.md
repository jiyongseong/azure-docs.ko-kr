---
title: Azure Instance Metadata Service | Microsoft Docs
description: Linux VM의 계산, 네트워크 및 예정된 유지 관리 이벤트에 대한 정보를 가져오는 RESTful 인터페이스입니다.
services: virtual-machines-linux
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: e57470e108faf68cecca703b2200acf357d2f721
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057906"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service


Azure Instance Metadata Service는 가상 머신을 관리 및 구성하는 데 사용할 수 있는 가상 머신 인스턴스를 실행하는 방법에 대한 정보를 제공합니다.
여기에는 SKU, 네트워크 구성, 예정된 유지 관리 이벤트 등의 정보가 포함됩니다. 사용 가능한 정보 유형에 대한 자세한 내용은 [메타데이터 범주](#instance-metadata-data-categories)를 참조하세요.

Azure의 Instance Metadata Service는 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)를 통해 생성된 IaaS VM에 액세스할 수 있는 REST 끝점입니다. 이 끝점은 VM 내부에서만 액세스할 수 있는 잘 알려진 라우팅이 불가능한 IP 주소(`169.254.169.254`) 에서 사용할 수 있습니다.

> [!IMPORTANT]
> 이 서비스는 Azure 지역에서 **일반 공급**됩니다.  정기적으로 업데이트를 받아 가상 컴퓨터 인스턴스에 대한 새 정보를 노출합니다. 이 페이지에는 사용 가능한 최신 [데이터 범주](#instance-metadata-data-categories)가 반영되어 있습니다.

## <a name="service-availability"></a>서비스 가용성
이 서비스는 일반 공급되는 Azure 지역에서 사용할 수 있습니다. 모든 API 버전을 모든 Azure 지역에서 사용할 수 있는 것은 아닙니다.

영역                                        | 가용성                                 | 지원되는 버전
-----------------------------------------------|-----------------------------------------------|-----------------
[일반 공급되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/)     | 일반 공급   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 일반 공급 | 2017-04-02,2017-08-01
[Azure 중국](https://www.azure.cn/)                                                           | 일반 공급 | 2017-04-02,2017-08-01
[Azure 독일](https://azure.microsoft.com/overview/clouds/germany/)                    | 일반 공급 | 2017-04-02,2017-08-01

이 표는 서비스 업데이트가 있거나 지원되는 새 버전을 사용할 수 있을 때 업데이트됩니다.

Instance Metadata Service를 평가하려면 위 지역의 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) 또는 [Azure Portal](http://portal.azure.com)에서 VM을 만들고 아래 예제를 따릅니다.

## <a name="usage"></a>사용

### <a name="versioning"></a>버전 관리
인스턴스 메타데이터 서비스에는 버전이 있습니다. 버전은 필수이며 글로벌 Azure에서 최신 버전은 `2017-12-01`입니다. 현재 지원되는 버전은 (2017-04-02, 2017-08-01,2017-12-01)입니다.

> [!NOTE] 
> 예약된 이벤트의 이전 미리 보기 릴리스는 api-version으로 {최신 버전}을 지원했습니다. 이 형식은 더 이상 지원되지 않으며 향후 사용되지 않을 예정입니다.

새로운 버전이 추가되더라도 스크립트가 특정 데이터 형식에 종속성이 있는 경우 이전 버전에 여전히 액세스할 수 있습니다. 그러나 이전 미리 보기 버전(2017-03-01)은 서비스가 일반 공급되면 사용하지 못할 수 있습니다.

### <a name="using-headers"></a>헤더 사용
Instance Metadata Service를 쿼리할 때 요청이 실수로 리디렉션되지 않도록 `Metadata: true` 헤더를 제공해야 합니다.

### <a name="retrieving-metadata"></a>메타데이터 검색

인스턴스 메타데이터는 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)를 사용하여 생성/관리되는 VM을 실행하는 데 사용할 수 있습니다. 다음 요청을 사용하여 가상 머신 인스턴스의 모든 데이터 범주에 액세스합니다.

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> 모든 인스턴스 메타데이터 쿼리는 대/소문자를 구분합니다.

### <a name="data-output"></a>데이터 출력
기본적으로 Instance Metadata Service는 JSON 형식(`Content-Type: application/json`)의 데이터를 반환합니다. 그러나 다른 API는 요청된 경우 다른 형식으로 데이터를 반환합니다.
다음 표는 API에서 지원할 수 있는 다른 데이터 형식에 대한 참조입니다.

API | 기본 데이터 형식 | 다른 형식
--------|---------------------|--------------
/instance | json : | 텍스트
/scheduledevents | json : | 없음

기본이 아닌 응답 형식에 액세스하려면 요청된 형식을 요청의 querystring 매개 변수로 지정합니다. 예:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>보안
Instance Metadata Service 끝점은 라우팅이 불가능한 IP 주소에서 실행 중인 가상 머신 인스턴스 내부에서만 액세스할 수 있습니다. 또한 `X-Forwarded-For` 헤더가 포함된 모든 요청은 서비스에 의해 거부됩니다.
실제 요청이 의도치 않은 리디렉션의 일환이 아니라 직접적으로 의도된 것이라는 것을 확인하기 위해 요청에 `Metadata: true` 헤더가 포함되어야 합니다. 

### <a name="error"></a>오류
찾을 수 없는 데이터 요소 또는 형식이 잘못된 요청이 있으면 Instance Metadata Service는 표준 HTTP 오류를 반환합니다. 예:

HTTP 상태 코드 | 이유
----------------|-------
200 정상 |
400 잘못된 요청 | 누락된 `Metadata: true` 헤더
404 찾을 수 없음 | 요청된 요소가 없음 
405 메서드를 사용할 수 없음 | `GET` 및 `POST` 요청만 지원됨
429 요청이 너무 많음 | API는 현재 초당 최대 5개의 쿼리를 지원함
500 서비스 오류     | 잠시 후 다시 시도하세요.

### <a name="examples"></a>예

> [!NOTE] 
> 모든 API 응답은 JSON 문자열입니다. 다음 예제 응답은 모두 가독성을 높이기 위해 적절히 인쇄되었습니다.

#### <a name="retrieving-network-information"></a>네트워크 정보 검색

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**응답**

> [!NOTE] 
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>공용 IP 주소 검색

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>인스턴스에 대한 모든 메타데이터 검색

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**응답**

> [!NOTE] 
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows Virtual Machine에서 메타데이터 검색

**요청**

인스턴스 메타데이터는 Powershell 유틸리티 `curl`을 통해 Windows에서 검색할 수 있습니다. 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

또는 `Invoke-RestMethod` cmdlet을 통해 검색할 수 있습니다.
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**응답**

> [!NOTE] 
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>인스턴스 메타데이터 데이터 범주
다음 데이터 범주는 Instance Metadata Service를 통해 사용할 수 있습니다.

Data | 설명 | 도입된 버전 
-----|-------------|-----------------------
location | VM을 실행하는 Azure 지역 | 2017-04-02 
이름 | VM의 이름 | 2017-04-02
offer | VM 이미지에 대한 정보를 제공합니다. 이 값은 Azure 이미지 갤러리에서 배포된 이미지에 대해서만 표시됩니다. | 2017-04-02
publisher | VM 이미지 게시자 | 2017-04-02
sku | VM 이미지에 해당하는 SKU | 2017-04-02
버전 | VM 이미지의 버전 | 2017-04-02
osType | Linux 또는or Windows | 2017-04-02
platformUpdateDomain |  VM을 실행 중인 [업데이트 도메인](manage-availability.md) | 2017-04-02
platformFaultDomain | VM을 실행 중인 [장애 도메인](manage-availability.md) | 2017-04-02
vmId | VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmSize | [VM 크기](sizes.md) | 2017-04-02
subscriptionId | Virtual Machine에 대한 Azure 구독 | 2017-08-01
tags | Virtual Machine에 대한 [태그](../../azure-resource-manager/resource-group-using-tags.md)  | 2017-08-01
resourceGroupName | Virtual Machine에 대한 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md) | 2017-08-01
placementGroupId | 가상 머신 확장 집합의 [배치 그룹](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
vmScaleSetName | 가상 머신 확장 집합의 [가상 머신 확장 집합 이름](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
영역 | 가상 머신의 [가용성 영역](../../availability-zones/az-overview.md) | 2017-12-01 
ipv4/privateIpAddress | VM의 로컬 IPv4 주소 | 2017-04-02
ipv4/publicIpAddress | VM의 공용 IPv4 주소 | 2017-04-02
subnet/address | VM의 서브넷 주소 | 2017-04-02 
subnet/prefix | 서브넷 접두사, 예:24 | 2017-04-02 
ipv6/ipaddress | VM의 로컬 IPv6 주소 | 2017-04-02 
macAddress | VM MAC 주소 | 2017-04-02 
scheduledevents | [예정된 이벤트](scheduled-events.md) 참조 | 2017-08-01
ID | (미리 보기) 관리 서비스 ID [액세스 토큰 획득](../../active-directory/managed-service-identity/how-to-use-vm-token.md)을 참조하세요. | 2018-02-01 

## <a name="example-scenarios-for-usage"></a>사용법을 위한 예제 시나리오  

### <a name="tracking-vm-running-on-azure"></a>Azure에서 실행 중인 VM 추적

서비스 공급자는 소프트웨어를 실행 중인 VM의 수를 추적하거나 VM의 고유성을 추적해야 하는 에이전트가 있어야 합니다. VM의 고유 ID를 가져오려면 Instance Metadata Service의 `vmId` 필드를 사용합니다.

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**응답**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>장애/업데이트 도메인 기반 컨테이너, 데이터 파티션 배치 

특정 시나리오의 경우 다양한 데이터 복제본 배치가 매우 중요합니다. 예를 들어 [HDFS 복제본 배치](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) 또는 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)를 통한 컨테이너 배치를 위해서는 VM을 실행 중인 `platformFaultDomain` 및 `platformUpdateDomain`을 알아야 합니다.
인스턴스에 대해 [가용성 영역](../../availability-zones/az-overview.md)을 활용하여 이러한 결정을 내릴 수도 있습니다. Instance Metadata Service를 통해 이 데이터를 직접 쿼리할 수 있습니다.

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**응답**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>지원 사례 중 VM에 대한 자세한 정보 얻기 

서비스 공급자는 지원 요청을 받을 수 있으며 이 때 VM에 대해 자세한 정보를 알아야 하는 경우가 있습니다. 고객에게 계산 메타데이터를 공유하도록 요청하면 지원 전문가가 Azure에서 VM의 종류에 대해 알 수 있도록 기본 정보가 제공될 수 있습니다. 

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**응답**

> [!NOTE] 
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>VM 내의 서로 다른 언어를 사용하여 메타데이터 서비스를 호출하는 예 

언어 | 예제 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
파이썬   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
자바       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>FAQ
1. `400 Bad Request, Required metadata header not specified` 오류가 발생했습니다. 무슨 의미인가요?
   * Instance Metadata Service에서는 `Metadata: true` 헤더를 요청에 포함시켜 전달해야 합니다. REST 호출에서 이 헤더를 전달하면 Instance Metadata Service에 액세스가 허용됩니다. 
2. VM에 대한 계산 정보를 구할 수 없는 이유가 무엇인가요?
   * 현재 Instance Metadata Service는 Azure Resource Manager를 사용하여 만든 인스턴스만 지원합니다. 나중에 클라우드 서비스 VM에 대한 지원을 추가할 수 있습니다.
3. 잠시 전에 Azure Resource Manager를 통해 내 Virtual Machine을 만들었습니다. 계산 메타데이터 정보가 왜 표시되지 않나요?
   * 2016년 9월 이후에 생성된 VM의 경우 계산 메타데이터를 표시하려면 [태그](../../azure-resource-manager/resource-group-using-tags.md)를 추가하세요. 이전 VM(2016년 9월 전에 생성된)의 경우 메타데이터를 새로 고치도록 VM에 확장 또는 데이터 디스크를 추가/제거하세요.
4. 새 버전 2017-08-01용으로 채워진 데이터 중 일부만 표시됩니다.
   * 2016년 9월 이후에 생성된 VM의 경우 계산 메타데이터를 표시하려면 [태그](../../azure-resource-manager/resource-group-using-tags.md)를 추가하세요. 이전 VM(2016년 9월 전에 생성된)의 경우 메타데이터를 새로 고치도록 VM에 확장 또는 데이터 디스크를 추가/제거하세요.
5. `500 Internal Server Error` 오류가 발생하는 이유가 무엇인가요?
   * 지수 백오프 시스템을 기반으로 요청을 다시 시도하세요. 문제가 지속되면 Azure 지원에 문의하세요.
6. 추가 질문/의견은 어디에 공유하나요?
   * http://feedback.azure.com에 대한 의견을 보내주세요.
7. Virtual Machine Scale Set 인스턴스에 작동하나요?
   * 예, 메타데이터 서비스는 확장 집합 인스턴스에 사용할 수 있습니다. 
8. 서비스에 대한 지원을 받으려면 어떻게 하나요?
   * 서비스에 대한 지원을 받으려면 Azure Portal에서 긴 다시 시도 후 메타데이터 받을 수 없는 VM에 대한 지원 문제를 만듭니다. 

   ![인스턴스 메타데이터 지원](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>다음 단계

- [예약된 이벤트](scheduled-events.md)에 대해 자세히 알아봅니다.

