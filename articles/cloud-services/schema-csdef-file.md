---
title: Azure Cloud Services 정의 스키마(.csdef 파일) | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: d5e086a952e18e477177634e5c197c27d4a5cc5f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services 정의 스키마(.csdef 파일)
서비스 정의 파일은 응용 프로그램에 대한 서비스 모델을 정의합니다. 파일은 클라우드 서비스에 사용할 수 있는 역할에 대한 정의가 포함되며, 서비스 끝점을 지정하고, 서비스에 대한 구성 설정을 설정합니다. 구성 설정 값은 [Cloud Service(클래식) 구성 스키마](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35)에 설명된 대로 서비스 구성 파일에서 설정됩니다.

기본적으로 Azure 진단 구성 스키마 파일은 `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` 디렉터리에 설치됩니다. `<version>`을 [Azure SDK](http://www.windowsazure.com/develop/downloads/)의 설치된 버전으로 바꿉니다.

서비스 정의 파일의 기본 확장명은 .csdef입니다.

## <a name="basic-service-definition-schema"></a>기본 서비스 정의 스키마
서비스 정의 파일은 `ServiceDefinition` 요소를 하나 포함해야 합니다. 서비스 정의는 하나 이상의 역할(`WebRole` 또는 `WorkerRole`) 요소를 포함해야 합니다. 단일 정의에 정의된 역할을 최대 25개 포함할 수 있으며 역할 유형과 함께 사용할 수 있습니다. 또한 서비스 정의는 특정 내부 끝점과 통신할 수 있는 역할을 제한하는 선택적 `NetworkTrafficRules` 요소를 포함합니다. 서비스 정의는 고객이 정의한 끝점의 상태 프로브를 포함하는 선택적 `LoadBalancerProbes` 요소를 포함합니다.

서비스 정의 파일의 기본 형식은 다음과 같습니다.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>스키마 정의
다음 토픽에서는 스키마를 설명합니다.

- [LoadBalancerProbe 스키마](schema-csdef-loadbalancerprobe.md)
- [WebRole 스키마](schema-csdef-webrole.md)
- [WorkerRole 스키마](schema-csdef-workerrole.md)
- [NetworkTrafficRules 스키마](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> ServiceDefinition 요소
`ServiceDefinition` 요소는 서비스 정의 파일의 최상위 요소입니다.

다음 표에서는 `ServiceDefinition` 요소의 특성을 설명합니다.

| 특성               | 설명 |
| ----------------------- | ----------- |
| 이름                    |필수 사항입니다. 서비스의 이름입니다. 이름은 서비스 계정 내에서 고유해야 합니다.|
| topologyChangeDiscovery | 선택 사항입니다. 토폴로지 유형 변경 알림을 지정합니다. 가능한 값은 다음과 같습니다.<br /><br /> -   `Blast` - 업데이트를 모든 역할 인스턴스에 최대한 빨리 보냅니다. 옵션을 선택한 경우 역할은 시작하지 않고 토폴로지 업데이트를 처리할 수 있어야 합니다.<br />-   `UpgradeDomainWalk` - 이전 인스턴스가 업데이트를 성공적으로 수락한 후 업데이트를 각 역할 인스턴스에 순차적으로 보냅니다.|
| schemaVersion           | 선택 사항입니다. 서비스 정의 스키마의 버전을 지정합니다. 스키마 버전을 사용하면 SDK가 둘 이상의 버전이 동시에 설치된 경우 Visual Studio에서 스키마 유효성 검사에 사용할 올바른 SDK 도구를 선택할 수 있습니다.|
| upgradeDomainCount      | 선택 사항입니다. 이 서비스의 역할이 할당되는 업그레이드 도메인의 수를 지정합니다. 서비스를 배포할 때 역할 인스턴스가 업그레이드 도메인에 할당됩니다. 자세한 내용은 [클라우드 서비스 역할 및 배포 업데이트](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment)를 참조하세요.<br /><br /> 최대 20개의 업그레이드 도메인을 지정할 수 있습니다. 지정하지 않은 경우 업그레이드 도메인의 기본값은 5입니다.|