---
title: "Cloud Services 역할 구성 XPath 참고 자료 | Microsoft Docs"
description: "클라우드 서비스 역할 구성에서 설정을 환경 변수로 노출하기 위해 사용할 수 있는 다양한 XPath 설정입니다."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e71adbca34390bda3a7d4067742ffb3a28201449
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2017
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>XPath를 사용하여 역할 구성 설정을 환경 변수로 노출
클라우드 서비스 작업자 또는 웹 역할 서비스 정의 파일에서 런타임 구성 값을 환경 변수로 노출할 수 있습니다. 다음 XPath 값(API 값에 해당)을 사용할 수 있습니다.

이러한 XPath 값을 [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) 라이브러리를 통해서도 사용할 수 있습니다. 

## <a name="app-running-in-emulator"></a>앱이 에뮬레이터에서 실행 중임
앱이 에뮬레이터에서 실행 중임을 나타냅니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| 코드 |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>배포 ID
인스턴스의 배포 ID를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| 코드 |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>역할 ID
인스턴스의 현재 역할 ID를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| 코드 |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>도메인 업데이트
인스턴스의 업데이트 도메인을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| 코드 |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>장애 도메인
인스턴스의 장애 도메인을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| 코드 |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>역할 이름
인스턴스의 역할 이름을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| 코드 |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>구성 설정
지정된 구성 설정의 값을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| 코드 |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>로컬 저장소 경로
인스턴스에 대한 로컬 저장소 경로를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| 코드 |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>로컬 저장소 크기
인스턴스의 로컬 저장소 크기를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| 코드 |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>끝점 프로토콜
인스턴스의 끝점 프로토콜을 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| 코드 |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>끝점 IP
지정된 끝점의 IP 주소를 가져옵니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| 코드 |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>끝점 포트
인스턴스의 끝점 포트를 검색합니다.

| 형식 | 예 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| 코드 |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>예제
다음은 [@emulated xpath 값](#app-running-in-emulator)으로 설정된 환경 변수 `TestIsEmulated`를 사용하여 시작 작업을 만드는 작업자 역할의 예입니다. 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>다음 단계
[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 파일에 대해 자세히 알아봅니다.

[ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) 패키지를 만듭니다.

역할에 대해 [원격 데스크톱](cloud-services-role-enable-remote-desktop-new-portal.md) 을 사용하도록 설정합니다.

