---
title: Azure Cloud Services 정의 WorkerRole 스키마 | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 96131a0bb928da7e22f3e26449c8b2279457d03f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Azure Cloud Services 정의 WorkerRole 스키마
Azure 작업자 역할은 일반화된 개발에 유용하고 웹 역할에 대한 백그라운드 처리를 수행할 수 있는 역할입니다.

서비스 정의 파일의 기본 확장명은 .csdef입니다.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>작업자 역할에 대한 기본 서비스 정의 스키마.
작업자 역할을 포함하는 서비스 정의 파일의 기본 형식은 다음과 같습니다.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]" "/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>”>
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>스키마 요소
서비스 정의 파일은 이 토픽의 후속 섹션에서 자세히 설명하는 이러한 요소를 포함합니다.

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[설정](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[끝점](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificates](#Certificates)

[인증서](#Certificate)

[Imports](#Imports)

[가져오기](#Import)

[런타임](#Runtime)

[환경](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[변수](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[시작](#Startup)

[Task](#Task)

[콘텐츠](#Contents)

[콘텐츠](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a> WorkerRole
`WorkerRole` 요소는 일반화된 개발에 유용하고 웹 역할에 대한 백그라운드 처리를 수행할 수 있는 역할을 설명합니다. 서비스는 0개 이상의 작업자 역할을 포함할 수 있습니다.

다음 표에서는 `WorkerRole` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 작업자 역할의 이름입니다. 역할의 이름은 고유해야 합니다.|
|enableNativeCodeExecution|부울|선택 사항입니다. 기본값은 `true`이며, 네이티브 코드 실행 및 완전 신뢰는 기본적으로 활성화됩니다. 이 특성을 `false`에 설정하여 작업자 역할에 네이티브 코드 실행을 사용하지 않도록 설정하고, 대신 Azure 부분 신뢰를 사용합니다.|
|vmsize|string|선택 사항입니다. 이 역할에 할당된 가상 머신의 크기를 변경하려면 이 값을 설정합니다. 기본값은 `Small`입니다. 가능한 가상 머신 크기와 해당 특성의 목록은 [Cloud Services에 대한 Virtual Machine 크기](cloud-services-sizes-specs.md)를 참조합니다.|

##  <a name="ConfigurationSettings"></a> ConfigurationSettings
`ConfigurationSettings` 요소는 작업자 역할에 대한 구성 설정의 컬렉션을 설명합니다. 이 요소는 `Setting` 요소의 부모입니다.

##  <a name="Setting"></a> Setting
`Setting` 요소는 역할의 인스턴스에 대한 구성 설정을 지정하는 이름 및 값 쌍을 설명합니다.

다음 표에서는 `Setting` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 구성 설정에 대한 고유한 이름입니다.|

역할에 대한 구성 설정은 서비스 정의 파일에 선언되고 서비스 구성 파일에 설정된 이름 및 값 쌍입니다.

##  <a name="LocalResources"></a> LocalResources
`LocalResources` 요소는 작업자 역할에 대한 로컬 저장소 리소스의 컬렉션을 설명합니다. 이 요소는 `LocalStorage` 요소의 부모입니다.

##  <a name="LocalStorage"></a> LocalStorage
`LocalStorage` 요소는 런타임에서 서비스에 대한 파일 시스템 공간을 제공하는 로컬 저장소 리소스를 식별합니다. 한 역할은 0개 이상의 로컬 저장소 리소스를 정의할 수 있습니다.

> [!NOTE]
>  `LocalStorage` 요소는 `WorkerRole` 요소의 자식으로 표시될 수 있으며, Azure SDK의 이전 버전과의 호환성을 지원합니다.

다음 표에서는 `LocalStorage` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 로컬 저장소의 고유한 이름입니다.|
|cleanOnRoleRecycle|부울|선택 사항입니다. 역할이 다시 시작될 때 로컬 저장소를 정리해야 하는지 여부를 나타냅니다. 기본값은 `true`입니다.|
|sizeInMb|int|선택 사항입니다. 로컬 저장소에 할당할 원하는 저장소 공간의 양(MB)입니다. 지정하지 않으면 할당되는 기본 저장소 공간은 100MB입니다. 할당될 수 있는 저장소 공간의 최소 크기는 1MB입니다.<br /><br /> 로컬 리소스의 최대 크기는 가상 머신 크기에 따라 다릅니다. 자세한 내용은 [Cloud Services를 위한 Virtual Machine 크기](cloud-services-sizes-specs.md)를 참조하세요.|

로컬 저장소 리소스에 할당된 디렉터리 이름은 이름 특성에 제공된 값에 해당합니다.

##  <a name="Endpoints"></a> Endpoints
`Endpoints` 요소는 역할에 대한 입력(외부), 내부 및 인스턴스 입력 끝점의 컬렉션을 설명합니다. 이 요소는 `InputEndpoint`, `InternalEndpoint` 및 `InstanceInputEndpoint` 요소의 부모입니다.

입력 및 내부 끝점은 개별적으로 할당됩니다. 서비스에는 입력, 내부 및 인스턴스 입력 끝점이 총 25개가 있을 수 있으며, 서비스에 허용된 25개의 역할에 할당할 수 있습니다. 예를 들어 역할이 5개라면 역할당 5개 입력 끝점을 할당하거나, 25개 입력 끝점을 단일 역할에 할당하거나, 1개 입력 끝점을 25개 역할에 각각 할당할 수 있습니다.

> [!NOTE]
>  배포된 각 역할은 각 역할에 하나의 인스턴스가 필요합니다. 구독에 대한 기본 프로비전은 20개의 코어로 제한되므로 역할의 인스턴스도 20개로 제한됩니다. 응용 프로그램에 프로비전에서 제공된 것보다 더 많은 인스턴스가 필요한 경우 [대금 청구, 구독 관리 및 할당량 지원](https://azure.microsoft.com/support/options/)에서 할당량 증가에 대한 자세한 내용을 참조하세요.

##  <a name="InputEndpoint"></a> InputEndpoint
`InputEndpoint` 요소는 작업자 역할에 대한 외부 끝점을 설명합니다.

HTTP, HTTPS, UDP 및 TCP 끝점의 조합인 여러 끝점을 정의할 수 있습니다. 입력 끝점에 대해 선택한 임의의 포트 번호를 지정할 수 있으나, 서비스에서 각 역할에 지정한 포트 번호는 고유해야 합니다. 예를 들어 역할에서 HTTP에 포트 80을 사용하고, HTTPS에 포트 443을 사용하도록 지정한 경우 두 번째 역할이 HTTP에 포트 8080을, HTTPS에 포트 8043을 사용하도록 지정할 수도 있습니다.

다음 표에서는 `InputEndpoint` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 외부 끝점에 대한 고유한 이름입니다.|
|protocol|string|필수 사항입니다. 외부 끝점에 대한 전송 프로토콜입니다. 작업자 역할의 경우 가능한 값은 `HTTP`, `HTTPS`, `UDP` 또는 `TCP`입니다.|
|포트|int|필수 사항입니다. 외부 끝점에 대한 포트입니다. 선택한 임의의 포트 번호를 지정할 수 있으나, 서비스에서 각 역할에 지정한 포트 번호는 고유해야 합니다.<br /><br /> 사용 가능한 값은 1에서 65535(포함) 사이입니다(Azure SDK 버전 1.7 이상).|
|인증서|string|HTTPS 끝점에 필요합니다. `Certificate` 요소에 정의된 인증서의 이름입니다.|
|localPort|int|선택 사항입니다. 끝점에서 내부 연결에 사용되는 포트를 지정합니다. `localPort` 특성은 끝점의 외부 포트를 역할의 내부 포트로 매핑합니다. 이는 역할이 외부로 노출되는 구성 요소와는 다른, 포트의 내부 구성 요소와 통신해야 하는 시나리오에서 유용합니다.<br /><br /> 지정하지 않으면 `localPort`의 값은 `port` 특성과 동일합니다. 런타임 API를 사용하여 검색할 수 있는 할당되지 않은 포트를 자동으로 할당하려면 `localPort`의 값을 “*”로 설정합니다.<br /><br /> 사용 가능한 값은 1에서 65535(포함) 사이입니다(Azure SDK 버전 1.7 이상).<br /><br /> `localPort` 특성은 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.|
|ignoreRoleInstanceStatus|부울|선택 사항입니다. 이 특성의 값을 `true`로 설정하면 서비스의 상태는 무시되고 끝점이 부하 분산 장치에서 제거되지 않습니다. 이 값을 `true`로 설정하면 서비스의 사용 중인 인스턴스를 디버깅할 때 유용합니다. 기본값은 `false`입니다. **참고:** 역할이 준비 상태가 아닌 경우 끝점이 트래픽을 계속 받을 수 있습니다.|
|loadBalancerProbe|string|선택 사항입니다. 입력 끝점과 연결된 부하 분산 장치 프로브의 이름입니다. 자세한 내용은 [LoadBalancerProbe 스키마](schema-csdef-loadbalancerprobe.md)를 참조하세요.|

##  <a name="InternalEndpoint"></a> InternalEndpoint
`InternalEndpoint` 요소는 작업자 역할에 대한 내부 끝점을 설명합니다. 내부 끝점은 서비스 내에서 실행되는 다른 역할 인스턴스에서만 사용할 수 있으며, 서비스 외부의 클라이언트에는 사용할 수 없습니다. 작업자 역할에는 HTTP, UDP 또는 TCP 내부 끝점이 최대 5개까지 있을 수 있습니다.

다음 표에서는 `InternalEndpoint` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 내부 끝점에 대한 고유한 이름입니다.|
|protocol|string|필수 사항입니다. 내부 끝점에 대한 전송 프로토콜입니다. 가능한 값은 `HTTP`, `TCP`, `UDP` 및 `ANY`입니다.<br /><br /> `ANY`의 값은 모든 프로토콜, 모든 포트가 허용되도록 지정합니다.|
|포트|int|선택 사항입니다. 끝점에서 내부 부하 분산된 연결에 사용되는 포트입니다. 부하 분산된 끝점은 포트 두 개를 사용합니다. 공용 IP 주소에 사용되는 포트 및 개인 IP 주소에 사용되는 포트입니다. 일반적으로 이러한 포트는 동일하게 설정되어 있지만 다른 포트를 사용하도록 선택할 수 있습니다.<br /><br /> 사용 가능한 값은 1에서 65535(포함) 사이입니다(Azure SDK 버전 1.7 이상).<br /><br /> `Port` 특성은 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.|

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
`InstanceInputEndpoint` 요소는 작업자 역할에 대한 인스턴스 입력 끝점을 설명합니다. 인스턴스 입력 끝점은 부하 분산 장치에서 포트 전달을 사용하여 특정 역할 인스턴스와 연결됩니다. 각 인스턴스 입력 끝점은 가능한 포트 범위에서 특정 포트에 매핑됩니다. 이 요소는 `AllocatePublicPortFrom` 요소의 부모입니다.

`InstanceInputEndpoint` 요소는 Azure SDK 버전 1.7 이상이어야 사용할 수 있습니다.

다음 표에서는 `InstanceInputEndpoint` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 끝점의 고유한 이름입니다.|
|localPort|int|필수 사항입니다. 부하 분산 장치에서 전달되어 들어오는 트래픽을 수신하기 위해 모든 역할 인스턴스가 수신 대기하는 내부 포트를 지정합니다. 가능한 값은 1에서 65535(포함) 사이입니다.|
|protocol|string|필수 사항입니다. 내부 끝점에 대한 전송 프로토콜입니다. 가능한 값은 `udp` 또는 `tcp`입니다. http/https 기반 트래픽에 `tcp`를 사용합니다.|

##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
`AllocatePublicPortFrom` 요소는 외부 고객이 각 인스턴스 입력 끝점에 액세스하는 데 사용할 수 있는 공용 포트 범위를 설명합니다. 공용(VIP) 포트 번호는 이 범위에서 할당되며, 테넌트를 배포 및 업데이트하는 동안 각 개인 역할 인스턴스 끝점에 할당됩니다. 이 요소는 `FixedPortRange` 요소의 부모입니다.

`AllocatePublicPortFrom` 요소는 Azure SDK 버전 1.7 이상이어야 사용할 수 있습니다.

##  <a name="FixedPort"></a> FixedPort
`FixedPort` 요소는 끝점에서 부하 분산된 연결 사용을 설정하는 내부 끝점에 대한 포트를 지정합니다.

`FixedPort` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

다음 표에서는 `FixedPort` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|포트|int|필수 사항입니다. 내부 끝점에 대한 포트입니다. 이는 동일한 포트에 `FixedPortRange` 최소값 및 최대값을 설정한 것과 같은 결과를 얻을 수 있습니다.<br /><br /> 사용 가능한 값은 1에서 65535(포함) 사이입니다(Azure SDK 버전 1.7 이상).|

##  <a name="FixedPortRange"></a> FixedPortRange
`FixedPortRange` 요소는 내부 끝점 또는 인스턴스 입력 끝점에 할당되는 포트 범위를 지정하며, 끝점에서 부하 분산된 연결에 사용되는 포트를 설정합니다.

> [!NOTE]
>  `FixedPortRange` 요소는 존재하는 요소에 따라 다르게 작동합니다. `FixedPortRange` 요소가 `InternalEndpoint` 요소에 있는 경우 역할이 실행되는 모든 가상 머신에 대한 최소 및 최대 특성 범위 내에서 부하 분산 장치의 모든 포트를 엽니다. `FixedPortRange` 요소가 `InstanceInputEndpoint` 요소에 있는 경우 역할을 실행하는 각 가상 머신에 대한 최소 및 최대 특성 범위 내에서 하나의 포트만 엽니다.

`FixedPortRange` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

다음 표에서는 `FixedPortRange` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|min|int|필수 사항입니다. 범위 내의 최소 포트입니다. 사용 가능한 값은 1에서 65535(포함) 사이입니다(Azure SDK 버전 1.7 이상).|
|max|string|필수 사항입니다. 범위 내의 최대 포트입니다. 사용 가능한 값은 1에서 65535(포함) 사이입니다(Azure SDK 버전 1.7 이상).|

##  <a name="Certificates"></a> Certificates
`Certificates` 요소는 작업자 역할에 대한 인증서의 컬렉션을 설명합니다. 이 요소는 `Certificate` 요소의 부모입니다. 역할에는 연결된 인증서가 여러 개 있을 수 있습니다. 인증서 요소 사용에 대한 자세한 내용은 [인증서로 서비스 정의 파일 수정](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)을 참조하세요.

##  <a name="Certificate"></a> Certificate
`Certificate` 요소는 작업자 역할과 연결된 인증서를 설명합니다.

다음 표에서는 `Certificate` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 이 인증서의 이름으로, HTTPS `InputEndpoint` 요소와 연결된 경우 참조하는 데 사용됩니다.|
|storeLocation|string|필수 사항입니다. 이 인증서를 로컬 컴퓨터에서 찾을 수 있는 인증서 저장소의 위치입니다. 가능한 값은 `CurrentUser` 및 `LocalMachine`입니다.|
|storeName|string|필수 사항입니다. 이 인증서가 로컬 컴퓨터에 상주하는 인증서 저장소의 이름입니다. 가능한 값은 기본 제공 저장소 이름 `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook` 또는 사용자 지정 저장소 이름을 포함합니다. 사용자 지정 저장소 이름을 지정하는 경우 저장소는 자동으로 만들어집니다.|
|permissionLevel|string|선택 사항입니다. 역할 프로세스에 부여되는 액세스 권한을 지정합니다. 관리자 권한 프로세스만 개인 키에 액세스할 수 있도록 하려면 `elevated` 권한을 지정합니다. `limitedOrElevated` 권한을 사용하면 모든 역할 프로세스가 개인 키에 액세스할 수 있습니다. 가능한 값은 `limitedOrElevated` 또는 `elevated`입니다. 기본값은 `limitedOrElevated`입니다.|

##  <a name="Imports"></a> Imports
`Imports` 요소는 게스트 운영 체제에 구성 요소를 추가하는 작업자 역할의 가져오기 모듈 컬렉션을 설명합니다. 이 요소는 `Import` 요소의 부모입니다. 이 요소는 선택 사항이며 역할은 런타임 블록을 하나만 가질 수 있습니다.

`Imports` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

##  <a name="Import"></a> Import
`Import` 요소는 게스트 운영 체제에 추가할 모듈을 지정합니다.

`Import` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

다음 표에서는 `Import` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|moduleName|string|필수 사항입니다. 가져올 모듈의 이름입니다. 유효한 가져오기 모듈은 다음과 같습니다.<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> RemoteAccess 및 RemoteForwarder 모듈을 사용하면 원격 데스크톱 연결에 대한 역할 인스턴스를 구성할 수 있습니다. 자세한 내용은 [원격 데스크톱 연결 활성화](cloud-services-role-enable-remote-desktop-new-portal.md)를 참조하세요.<br /><br /> 진단 모듈을 사용하면 역할 인스턴스에 대한 진단 데이터를 수집할 수 있습니다.|

##  <a name="Runtime"></a> Runtime
`Runtime` 요소는 Azure 호스트 프로세스의 런타임 환경을 제어하는 작업자 역할에 대한 환경 변수 설정의 컬렉션을 설명합니다. 이 요소는 `Environment` 요소의 부모입니다. 이 요소는 선택 사항이며 역할은 런타임 블록을 하나만 가질 수 있습니다.

`Runtime` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

다음 표에서는 `Runtime` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|executionContext|string|선택 사항입니다. 역할 프로세스가 시작되는 컨텍스트를 지정합니다. 기본 컨텍스트는 `limited`입니다.<br /><br /> -   `limited` - 프로세스가 관리자 권한 없이 시작됩니다.<br />-   `elevated` - 프로세스가 관리자 권한으로 시작됩니다.|

##  <a name="Environment"></a> Environment
`Environment` 요소는 작업자 역할에 대한 환경 변수 설정의 컬렉션을 설명합니다. 이 요소는 `Variable` 요소의 부모입니다. 역할에는 환경 변수 설정이 여러 개 있을 수 있습니다.

##  <a name="Variable"></a> Variable
`Variable` 요소는 게스트 운영 체제에서 설정하는 환경 변수를 지정합니다.

`Variable` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

다음 표에서는 `Variable` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|이름|string|필수 사항입니다. 설정할 환경 변수의 이름입니다.|
|값|string|선택 사항입니다. 환경 변수에 대해 설정하는 값입니다. 값 특성 또는 `RoleInstanceValue` 요소를 포함해야 합니다.|

##  <a name="RoleInstanceValue"></a> RoleInstanceValue
`RoleInstanceValue` 요소는 변수 값을 검색하는 xPath를 지정합니다.

다음 표에서는 `RoleInstanceValue` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|xpath|string|선택 사항입니다. 인스턴스에 대한 배포 설정의 위치 경로입니다. 자세한 내용은 [XPath를 사용한 구성 변수](cloud-services-role-config-xpath.md)를 참조하세요.<br /><br /> 값 특성 또는 `RoleInstanceValue` 요소를 포함해야 합니다.|

##  <a name="EntryPoint"></a> EntryPoint
`EntryPoint` 요소는 역할에 대한 진입점을 지정합니다. 이 요소는 `NetFxEntryPoint` 요소의 부모입니다. 이러한 요소를 사용하면 역할 진입점으로 동작하도록 기본 WaWorkerHost.exe 이외의 응용 프로그램을 지정할 수 있습니다.

`EntryPoint` 요소는 Azure SDK 버전 1.5 이상이어야 사용할 수 있습니다.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
`NetFxEntryPoint` 요소는 역할에 실행하는 프로그램을 지정합니다.

> [!NOTE]
>  `NetFxEntryPoint` 요소는 Azure SDK 버전 1.5 이상이어야 사용할 수 있습니다.

다음 표에서는 `NetFxEntryPoint` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|assemblyName|string|필수 사항입니다. 진입점을 포함하는 어셈블리의 경로 및 파일 이름입니다. 경로는 **\\%ROLEROOT%\Approot** 폴더에 상대적입니다(`commandLine`에 **\\%ROLEROOT%\Approot**를 지정하지 않음, 가정됨). **%ROLEROOT%** 는 Azure에서 유지 관리되는 환경 변수이며, 사용자 역할에 대한 루트 폴더 위치를 나타냅니다. **\\%ROLEROOT%\Approot** 폴더는 사용자의 역할에 대한 응용 프로그램 폴더를 나타냅니다.|
|targetFrameworkVersion|string|필수 사항입니다. 어셈블리가 작성되는 .NET Framework의 버전입니다. 예: `targetFrameworkVersion="v4.0"`|

##  <a name="ProgramEntryPoint"></a> ProgramEntryPoint
`ProgramEntryPoint` 요소는 역할에 실행하는 프로그램을 지정합니다. `ProgramEntryPoint` 요소를 사용하면 .NET 어셈블리에 기반하지 않는 프로그램 진입점을 지정할 수 있습니다.

> [!NOTE]
>  `ProgramEntryPoint` 요소는 Azure SDK 버전 1.5 이상이어야 사용할 수 있습니다.

다음 표에서는 `ProgramEntryPoint` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|commandLine|string|필수 사항입니다. 실행할 프로그램의 경로, 파일 이름 및 명령줄 인수입니다. 경로는 **%ROLEROOT%\Approot** 폴더에 상대적입니다(명령줄에 **%ROLEROOT%\Approot**를 지정하지 않음, 가정됨). **%ROLEROOT%** 는 Azure에서 유지 관리되는 환경 변수이며, 사용자 역할에 대한 루트 폴더 위치를 나타냅니다. **%ROLEROOT%\Approot** 폴더는 사용자의 역할에 대한 응용 프로그램 폴더를 나타냅니다.<br /><br /> 프로그램이 종료되면, 역할이 재활용되므로 일반적으로 단지 시작해서 한정된 작업을 실행하는 프로그램이 되는 대신 계속 실행되도록 프로그램을 설정합니다.|
|setReadyOnProcessStart|부울|필수 사항입니다. 명령줄 프로그램이 시작되었음을 알리는 신호를 보내길 역할 인스턴스에서 대기하는지 여부를 지정합니다. 이 때 이 값은 `true`로 설정해야 합니다. 값을 `false`로 설정하면 나중에 사용하도록 예약됩니다.|

##  <a name="Startup"></a> Startup
`Startup` 요소는 역할이 시작될 때 실행하는 작업의 컬렉션을 설명합니다. 이 요소는 `Variable` 요소의 부모일 수 있습니다. 역할 시작 작업 사용에 대한 자세한 내용은 [시작 작업을 구성하는 방법](cloud-services-startup-tasks.md)을 참조하세요. 이 요소는 선택 사항이며 역할은 시작 블록을 하나만 가질 수 있습니다.

다음 표에서는 `Startup` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|우선 순위|int|내부 전용입니다.|

##  <a name="Task"></a> Task
`Task` 요소는 역할이 시작될 때 발생하는 시작 작업을 지정합니다. 시작 작업은 이러한 설치 소프트웨어 구성 요소를 실행하거나 다른 응용 프로그램을 실행하기 위해 역할을 준비하는 작업을 수행하는 데 사용할 수 있습니다. 작업은 `Startup` 요소 블록 내 표시되는 순서대로 실행됩니다.

`Task` 요소는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

다음 표에서는 `Task` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|commandLine|string|필수 사항입니다. 실행하는 명령이 포함된 CMD 파일과 같은 스크립트입니다. 시작 명령 및 일괄 처리 파일은 ANSI 형식으로 저장해야 합니다. 파일의 시작 부분에 바이트 순서 표시자를 설정한 파일 형식은 올바르게 처리되지 않습니다.|
|executionContext|string|스크립트가 실행되는 컨텍스트를 지정합니다.<br /><br /> -   `limited` [기본값] – 프로세스를 호스팅하는 역할과 동일한 권한으로 실행합니다.<br />-   `elevated` – 관리자 권한으로 실행합니다.|
|taskType|string|명령의 실행 동작을 지정합니다.<br /><br /> -   `simple` [기본값] – 시스템이 다른 작업이 시작되기 전에 작업이 종료되기를 기다립니다.<br />-   `background` – 시스템이 작업이 종료되기를 기다리지 않습니다.<br />-   `foreground` – 백그라운드와 유사하게, 모든 포그라운드 작업이 종료될 때까지 역할 제외는 다시 시작되지 않습니다.|

##  <a name="Contents"></a> Contents
`Contents` 요소는 작업자 역할에 대한 콘텐츠의 컬렉션을 설명합니다. 이 요소는 `Content` 요소의 부모입니다.

`Contents` 요소는 Azure SDK 버전 1.5 이상이어야 사용할 수 있습니다.

##  <a name="Content"></a> Content
`Content` 요소는 Azure 가상 컴퓨터로 복사되는 콘텐츠의 원본 위치와 복사되는 대상 경로를 정의합니다.

`Content` 요소는 Azure SDK 버전 1.5 이상이어야 사용할 수 있습니다.

다음 표에서는 `Content` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|destination|string|필수 사항입니다. Azure 가상 컴퓨터에서 콘텐츠가 배치되는 위치입니다. 이 위치는 **%ROLEROOT%\Approot** 폴더에 상대적입니다.|

이 요소는 `SourceDirectory` 요소의 부모 요소입니다.

##  <a name="SourceDirectory"></a> SourceDirectory
`SourceDirectory` 요소는 콘텐츠가 복사되는 로컬 디렉터리를 정의합니다. 이 요소를 사용하여 Azure 가상 머신으로 복사할 로컬 콘텐츠를 지정합니다.

`SourceDirectory` 요소는 Azure SDK 버전 1.5 이상이어야 사용할 수 있습니다.

다음 표에서는 `SourceDirectory` 요소의 특성을 설명합니다.

| 특성 | type | 설명 |
| --------- | ---- | ----------- |
|경로|string|필수 사항입니다. 콘텐츠가 Azure 가상 컴퓨터에 복사되는 로컬 디렉터리의 상대 또는 절대 경로입니다. 디렉터리 경로의 환경 변수 확장이 지원됩니다.|

## <a name="see-also"></a>참고 항목
[Cloud Service(클래식) 정의 스키마](schema-csdef-file.md)