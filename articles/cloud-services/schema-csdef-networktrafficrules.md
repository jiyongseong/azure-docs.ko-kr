---
title: Azure Cloud Services 정의 NetworkTrafficRules 스키마 | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 779d3b42aeab04bb93756439a0482f32ade6557e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services 정의 NetworkTrafficRules 스키마
`NetworkTrafficRules` 노드는 역할이 서로 통신하는 방법을 지정하는 서비스 정의 파일의 선택적 요소입니다. 특정 역할의 내부 끝점에 액세스할 수 있는 역할을 제한합니다. `NetworkTrafficRules`는 독립 실행형 요소가 아닙니다. 서비스 정의 파일에 두 개 이상의 역할과 결합됩니다.

서비스 정의 파일의 기본 확장명은 .csdef입니다.

> [!NOTE]
>  `NetworkTrafficRules` 노드는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>네트워크 트래픽 규칙에 대한 기본 서비스 정의 스키마
네트워크 트래픽 정의를 포함하는 서비스 정의 파일의 기본 형식은 다음과 같습니다.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>스키마 요소
서비스 정의 파일의 `NetworkTrafficRules` 노드는 이 토픽의 후속 섹션에서 자세히 설명하는 이러한 요소를 포함합니다.

[NetworkTrafficRules 요소](#NetworkTrafficRules)

[OnlyAllowTrafficTo 요소](#OnlyAllowTrafficTo)

[Destinations 요소](#Destinations)

[RoleEndpoint 요소](#RoleEndpoint)

[AllowAllTraffic 요소](#AllowAllTraffic)

[WhenSource 요소](#WhenSource)

[FromRole 요소](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules 요소
`NetworkTrafficRules` 요소는 다른 역할의 끝점과 통신할 수 있는 역할을 지정합니다. 서비스에는 하나의 `NetworkTrafficRules` 정의가 포함될 수 있습니다.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo 요소
`OnlyAllowTrafficTo` 요소는 대상 끝점의 컬렉션 및 대상 끝점과 통신할 수 있는 역할을 설명합니다. 여러 `OnlyAllowTrafficTo` 노드를 지정할 수 있습니다.

##  <a name="Destinations"></a> Destinations 요소
`Destinations` 요소는 통신할 수 있는 RoleEndpoints의 컬렉션을 설명합니다.

##  <a name="RoleEndpoint"></a> RoleEndpoint 요소
`RoleEndpoint` 요소는 역할에서 통신을 허용하는 끝점을 설명합니다. 역할에 둘 이상의 끝점이 있는 경우 `RoleEndpoint` 요소를 여러 개 지정할 수 있습니다.

| 특성      | type     | 설명 |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | 필수 사항입니다. 트래픽을 허용하는 끝점의 이름입니다.|
| `roleName`     | `string` | 필수 사항입니다. 통신을 허용하는 웹 역할의 이름입니다.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic 요소
`AllowAllTraffic` 요소는 모든 역할이 `Destinations` 노드에 정의된 끝점과 통신하도록 허용하는 규칙입니다.

##  <a name="WhenSource"></a> WhenSource 요소
`WhenSource` 요소는 `Destinations` 노드에 정의된 끝점과 통신할 수 있는 역할의 컬렉션을 설명합니다.

| 특성 | type     | 설명 |
| --------- | -------- | ----------- |
| `matches` | `string` | 필수 사항입니다. 통신을 허용할 때 적용할 규칙을 지정합니다. 현재 유효한 값은 `AnyRule`뿐입니다.|
  
##  <a name="FromRole"></a> FromRole 요소
`FromRole` 요소는 `Destinations` 노드에 정의된 끝점과 통신할 수 있는 역할을 지정합니다. 끝점과 통신할 수 있는 역할이 둘 이상인 경우 `FromRole` 요소를 여러 개 지정할 수 있습니다.

| 특성  | type     | 설명 |
| ---------- | -------- | ----------- |
| `roleName` | `string` | 필수 사항입니다. 통신을 허용하는 역할에 대한 이름입니다.|

## <a name="see-also"></a>참고 항목
[Cloud Service(클래식) 정의 스키마](schema-csdef-file.md)