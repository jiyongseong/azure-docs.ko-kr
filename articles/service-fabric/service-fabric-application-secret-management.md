---
title: Azure Service Fabric 응용 프로그램 암호 관리 | Microsoft Docs
description: Service Fabric 응용 프로그램에서 암호 값을 보호하는 방법에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: vturecek
ms.openlocfilehash: fa79d50d6ef2899dcaf4116dcfe8ac7fae077959
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-secrets-in-service-fabric-applications"></a>Service Fabric 응용 프로그램에서 비밀 관리
이 가이드에서는 서비스 패브릭 응용 프로그램에서 비밀을 관리하는 단계를 안내합니다. 저장소 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다.

[Azure Key Vault][key-vault-get-started]는 인증서에 대한 안전한 저장소 위치이자 Azure의 Service Fabric 클러스터에 설치된 인증서를 가져오는 수단으로 사용됩니다. Azure에 배포하지 않는 경우 서비스 패브릭 응용 프로그램의 비밀을 관리하기 위해 주요 자격 증명 모음을 사용할 필요가 없습니다. 하지만 응용 프로그램에서 비밀을 *사용* 하는 것은 클라우드 플랫폼에 구애를 받지 않으므로 그 어디에 호스트된 클러스터에도 응용 프로그램을 배포할 수 있습니다. 

## <a name="obtain-a-data-encipherment-certificate"></a>데이터 암호화 인증서 가져오기
데이터 암호화 인증서는 서비스에 포함된 Settings.xml의 구성 값을 암호화하고 해독하는 용도로만 엄격하게 사용되며 암호화 텍스트의 인증에는 사용되지 않습니다. 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서 키 사용에는 데이터 암호화(10)가 포함되어야 하며, 서버 인증 또는 클라이언트 인증은 포함되면 안 됩니다. 
  
  예를 들어 PowerShell을 사용하여 자체 서명된 인증서를 만들 때 `KeyUsage` 플래그가 `DataEncipherment`로 설정되어야 합니다.
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>클러스터에 인증서 설치
클러스터의 각 노드에 이 인증서를 설치해야 합니다. 이 인증서는 런타임에 서비스의 Settings.xml에 저장된 값을 해독하는 데 사용됩니다. 설정 지침은 [Azure Resource Manager를 사용하여 클러스터를 만드는 방법][service-fabric-cluster-creation-via-arm]을 참조하세요. 

## <a name="encrypt-application-secrets"></a>응용 프로그램 비밀 암호화
응용 프로그램을 배포할 때 인증서를 사용하여 비밀 값을 암호화하고 서비스의 Settings.xml 구성 파일에 삽입합니다. 서비스 패브릭 SDK는 비밀 암호화 및 암호 해독 기능이 기본적으로 제공됩니다. 작성 시 비밀 값을 암호화한 후 서비스 코드에서 프로그래밍 방식으로 해독하여 읽을 수 있습니다. 

다음 PowerShell 명령은 비밀을 암호화하는 데 사용됩니다. 이 명령은 값을 암호화합니다. 암호화 텍스트에 서명하지 **않습니다**. 클러스터에 설치된 것과 동일한 암호화 인증서를 사용하여 비밀 값의 암호 텍스트를 생성해야 합니다.

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

그 결과로 얻게 되는 Base-64 인코딩 문자열에는 암호 텍스트와 암호화에 사용된 인증서에 대한 정보가 모두 포함되어 있습니다.  Base-64로 인코딩된 문자열은 서비스의 `IsEncrypted` 특성이 `true`로 설정된 Settings.xml 구성 파일의 매개 변수에 삽입할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>응용 프로그램 비밀을 응용 프로그램 인스턴스에 삽입
여러 환경에 배포할 때에는 배포를 최대한 자동화하는 것이 좋습니다. 빌드 환경에서 비밀 암호화를 수행하고 응용 프로그램 인스턴스를 만들 때 암호화된 비밀을 매개 변수로 제공하면 배포를 자동화할 수 있습니다.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Settings.xml에 재정의 가능한 매개 변수 사용
Settings.xml 구성 파일은 응용 프로그램 생성 시 제공할 수 있는 재정의 가능한 매개 변수를 허용합니다. 매개 변수 값을 입력하는 대신 `MustOverride` 특성을 사용합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Settings.xml의 값을 재정의하려면 ApplicationManifest.xml에서 서비스의 재정의 매개 변수를 선언합니다.

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

이제 응용 프로그램의 인스턴스를 만들 때 이 값을 *응용 프로그램 매개 변수* 로 지정할 수 있습니다. 응용 프로그램 인스턴스 만들기를 PowerShell로 스크립팅하거나 C#으로 작성하면 빌드 프로세스에 쉽게 통합할 수 있습니다.

PowerShell을 사용하면 매개 변수가 `New-ServiceFabricApplication` 명령에 [해시 테이블](https://technet.microsoft.com/library/ee692803.aspx)로 제공됩니다.

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

C#을 사용하면 응용 프로그램 매개 변수가 `ApplicationDescription`에 `NameValueCollection`로 지정됩니다.

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-secrets-from-service-code"></a>서비스 코드에서 비밀 해독
암호를 암호화하는 데 사용되는 암호화로 암호를 해독하여 Settings.xml 파일에서 암호화된 값을 읽을 수 있습니다. 서비스 패브릭의 서비스는 기본적으로 Windows의 네트워크 서비스에서 실행되며 추가 설정 없이는 노드에 설치된 인증서에 액세스할 수 없습니다.

데이터 암호화 인증서를 사용할 때에는 네트워크 서비스 또는 서비스가 실행되고 있는 사용자 계정이 인증서의 개인 키에 액세스할 수 있는지 확인해야 합니다. 서비스 패브릭이 서비스에 대한 액세스 권한을 자동으로 부여하도록 구성하면 자동으로 처리됩니다. 이 구성은 ApplicationManifest.xml에서 인증서의 사용자 및 보안 정책을 정의하여 수행할 수 있습니다. 다음은 지문을 통해 정의된 인증서에 대한 읽기 권한을 네트워크 서비스 계정에 부여하는 예입니다.

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Windows의 인증서 저장소 스냅인에서 인증서 지문을 복사하는 경우 지문 문자열의 시작 부분에 보이지 않는 문자가 배치됩니다. 이 보이지 않는 문자는 지문으로 인증서를 찾으려 할 때 오류를 일으킬 수 있으므로 이 추가 문자를 삭제합니다.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>서비스 코드에 응용 프로그램 암호 사용
구성 패키지의 Settings.xml에서 구성 값에 액세스할 수 있는 API를 사용하면 `IsEncrypted` 특성이 `true`로 설정된 값을 간단하게 해독할 수 있습니다. 암호화된 텍스트에는 암호화에 사용된 인증서 정보가 포함되어 있으므로 수동으로 인증서를 찾을 필요가 없습니다. 서비스가 실행되고 있는 노드에 인증서를 설치하기만 하면 됩니다. 간단하게 `DecryptValue()` 메서드를 호출하여 원래 비밀 값을 검색합니다.

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>다음 단계
[응용 프로그램 및 서비스 보안](service-fabric-application-and-service-security.md)에 대한 자세한 정보

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
