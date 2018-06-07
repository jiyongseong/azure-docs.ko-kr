---
title: Azure Service Fabric 응용 프로그램 배포 | Microsoft Docs
description: PowerShell을 사용하여 Service Fabric에서 응용 프로그램을 배포 및 제거하는 방법
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 0fa7bd1135c099f853b9a3bb66661c0a57a0f7eb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="deploy-and-remove-applications-using-powershell"></a>PowerShell을 사용하여 응용 프로그램 배포 및 제거
> [!div class="op_single_selector"]
> * [리소스 관리자](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

일단 [응용 프로그램 형식이 패키지화되면][10] Azure Service Fabric 클러스터에 배포될 준비가 된 것입니다. 배포에는 다음 세 단계가 포함됩니다.

1. 이미지 저장소에 응용 프로그램 패키지를 업로드합니다.
2. 이미지 저장소 상대 경로에 응용 프로그램 유형을 등록합니다.
3. 응용 프로그램 인스턴스를 만듭니다.

배포한 응용 프로그램이 더 이상 필요하지 않게 되면 응용 프로그램 인스턴스 및 해당 응용 프로그램 유형을 삭제할 수 있습니다. 클러스터에서 응용 프로그램을 완전히 제거하려면 다음 단계를 수행합니다.

1. 실행 중인 응용 프로그램 인스턴스를 제거(또는 삭제)합니다.
2. 더 이상 필요하지 않은 경우 응용 프로그램 유형을 등록 취소합니다.
3. 이미지 저장소에서 응용 프로그램 패키지를 제거합니다.

로컬 개발 클러스터에서 Visual Studio를 사용하여 응용 프로그램을 배포 및 디버그하는 경우 이전의 모든 단계는 PowerShell 스크립트를 통해 자동으로 처리됩니다.  이 스크립트는 응용 프로그램 프로젝트의 *Scripts* 폴더에 있습니다. 이 문서에서는 Visual Studio 외부에서 동일한 작업을 수행할 수 있도록 스크립트에서 수행하는 작업에 대한 배경을 설명합니다. 

응용 프로그램을 배포하는 또 다른 방법은 외부 프로비전을 사용하는 것입니다. 응용 프로그램 패키지는 [`sfpkg`으로 패키지](service-fabric-package-apps.md#create-an-sfpkg)되거나 외부 저장소에 업로드될 수 있습니다. 이 경우 이미지 저장소에 업로드할 필요가 없습니다. 배포에는 다음 단계가 필요합니다.

1. `sfpkg`를 외부 저장소에 업로드합니다. 외부 저장소는 REST http 또는 https 끝점을 노출하는 어떤 저장소도 될 수 있습니다.
2. 외부 다운로드 URI 및 응용 프로그램 유형 정보를 사용하여 응용 프로그램 유형을 등록합니다.
2. 응용 프로그램 인스턴스를 만듭니다.

간단히 정리하기 위해 응용 프로그램 인스턴스를 제거하고 응용 프로그램 유형을 등록 취소합니다. 패키지가 이미지 저장소에 복사되지 않았으므로 정리할 임시 위치가 없습니다. 외부 저장소에서 프로비전하는 방식은 Service Fabric 버전 6.1부터 사용할 수 있습니다.

>[!NOTE]
> Visual Studio는 현재 외부 프로비전을 지원하지 않습니다.

 
## <a name="connect-to-the-cluster"></a>클러스터에 연결
이 문서에서는 PowerShell 명령을 실행하기에 앞서 언제나 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)를 사용하여 Service Fabric 클러스터에 연결하는 것으로 시작합니다. 로컬 개발 클러스터에 연결하려면 다음을 실행합니다.

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Azure Active Directory, X509 인증서 또는 Windows Active Directory를 사용하여 보안된 원격 클러스터 또는 클러스터에 연결하는 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요.

## <a name="upload-the-application-package"></a>응용 프로그램 패키지 업로드
응용 프로그램 패키지를 업로드하면 내부 서비스 패브릭 구성 요소에 의해 액세스할 수 있는 위치에 배치됩니다.
로컬로 응용 프로그램 패키지를 확인하려는 경우 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet을 사용합니다.

[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 명령으로 응용 프로그램 패키지를 클러스터 이미지 저장소에 업로드합니다.

Visual Studio 2015에서 *MyApplication*이라는 응용 프로그램을 빌드하고 패키지한다고 가정해 보겠습니다. 기본적으로 ApplicationManifest.xml에 나열된 응용 프로그램 유형 이름은 "MyApplicationType"입니다.  필요한 응용 프로그램 매니페스트, 서비스 매니페스트 및 코드/구성/데이터 패키지가 포함된 응용 프로그램 패키지는 *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*에 있습니다. 

다음 명령은 응용 프로그램 패키지의 내용을 나열합니다.

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

응용 프로그램 패키지가 크거나 파일이 많은 경우 [압축할 수 있습니다](service-fabric-package-apps.md#compress-a-package). 압축은 파일의 크기와 수를 줄입니다.
부작용은 응용 프로그램 유형 등록 및 등록 취소가 빠르다는 것입니다. 업로드 시간은 현재 느려질 수 있습니다. 패키지를 압축하는 시간이 포함되는 경우 특히 그렇습니다. 

패키지를 압축하려면 동일한 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 명령을 사용합니다. 압축은 업로드와 별도로 `SkipCopy` 플래그를 사용하거나 업로드 작업과 함께 수행할 수 있습니다. 압축된 패키지에 압축을 적용하면 아무런 변화가 없습니다.
압축된 패키지를 풀려면 `UncompressPackage` 스위치와 함께 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 명령을 사용합니다.

다음 cmdlet은 패키지를 이미지 저장소에 복사하지 않고 압축합니다. 이제 패키지에 `Code` 및 `Config` 패키지의 압축 파일이 포함됩니다. 응용 프로그램 및 서비스 매니페스트는 많은 내부 작업(특정 유효성 검사를 위한 패키지 공유, 응용 프로그램 유형 이름 및 버전 추출 등)에 필요하기 때문에 압축되지 않습니다. 매니페스트를 압축하면 이러한 작업이 비효율적으로 수행됩니다.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

대형 응용 프로그램 패키지의 경우 압축하는 데 시간이 걸립니다. 최상의 결과를 얻으려면 빠른 SSD 드라이브를 사용합니다. 압축된 패키지의 압축 시간과 크기도 패키지 내용에 따라 다릅니다.
예를 들어 압축 시간과 함께 초기 크기 및 압축된 패키지 크기를 보여 주는 일부 패키지의 압축 통계가 있습니다.

|초기 크기(MB)|파일 수|압축 시간|압축된 패키지 크기(MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

압축된 패키지는 필요에 따라 하나 또는 여러 개의 Service Fabric 클러스터에 업로드할 수 있습니다. 배포 메커니즘은 압축된 패키지와 압축되지 않은 패키지에 대해 모두 동일합니다. 압축된 패키지는 클러스터 이미지 저장소에 저장됩니다. 응용 프로그램을 실행하기 전에 패키지는 노드에서 압축되지 않습니다.


다음 예제에서는 패키지를 이미지 저장소의 "MyApplicationV1"이라는 폴더에 업로드합니다.

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

*-ApplicationPackagePathInImageStore* 매개 변수를 지정하지 않으면 응용 프로그램 패키지가 이미지 저장소의 “Debug” 폴더에 복사됩니다.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage**는 PowerShell 세션이 Service Fabric 클러스터에 연결되는 경우 해당 이미지 저장소 연결 문자열을 자동으로 검색합니다. 5.6보다 오래된 Service Fabric 버전에서는 **-ImageStoreConnectionString** 인수를 명시적으로 제공해야 합니다.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>서비스 패브릭 SDK PowerShell 모듈의 일부인 **Get ImageStoreConnectionStringFromClusterManifest** cmdlet는 이미지 저장소 연결 문자열을 가져오는 데 사용됩니다.  SDK 모듈을 가져오려면 다음을 실행합니다.
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>이미지 저장소 및 이미지 저장소 연결 문자열에 대한 보충 정보는 [이미지 저장소 연결 문자열 이해](service-fabric-image-store-connection-string.md)를 참조하세요.
>
>
>

패키지를 업로드하는 데 걸리는 시간은 여러 요소에 따라 다릅니다. 이러한 요소 중 일부는 패키지의 파일 수, 패키지 크기 및 파일 크기입니다. 원본 컴퓨터와 Service Fabric 클러스터 간의 네트워크 속도 업로드 시간에 영향을 줍니다. [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)의 기본 시간 제한은 30분입니다.
설명된 요소에 따라 시간 제한을 늘려야 할 수 있습니다. 복사 호출에서 패키지를 압축하는 경우 압축 시간도 고려해야 합니다.



## <a name="register-the-application-package"></a>응용 프로그램 패키지 등록
응용 프로그램 매니페스트에 선언된 응용 프로그램 형식과 버전은 응용 프로그램 패키지를 등록할 때 사용할 수 있게 됩니다. 시스템은 이전 단계에서 업로드된 패키지를 읽고, 패키지를 확인하며, 처리된 패키지를 내부 시스템 위치에 복사합니다.  

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet을 실행하여 클러스터에서 응용 프로그램 유형을 등록하고 배포할 수 있도록 합니다.

### <a name="register-the-application-package-copied-to-image-store"></a>이미지 저장소에 복사된 응용 프로그램 패키지 등록
패키지가 이전에 이미지 저장소에 복사되었으면 등록 작업은 이미지 저장소에 상대 경로를 지정합니다.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
Register application type succeeded
```

“MyApplicationV1”은 응용 프로그램 패키지가 있는 이미지 저장소의 폴더입니다. 이름이 "MyApplicationType"이고 버전이 "1.0.0"인(둘 다 응용 프로그램 매니페스트에 있음) 응용 프로그램 유형이 이제 클러스터에 등록됩니다.

### <a name="register-the-application-package-copied-to-an-external-store"></a>외부 저장소에 복사된 응용 프로그램 패키지 등록
Service Fabric 버전 6.1부터, 프로비전 기능은 외부 저장소에서 패키지를 다운로드하도록 지원합니다. 다운로드 URI는 HTTP 또는 HTTPS 프로토콜을 사용하여 응용 프로그램 패키지를 다운로드할 수 있는 [`sfpkg` 응용 프로그램 패키지](service-fabric-package-apps.md#create-an-sfpkg)의 경로를 나타냅니다. 패키지는 이전에 이 외부 위치에 업로드되었을 것입니다. URI는 Service Fabric이 파일을 다운로드할 수 있도록 읽기 액세스를 허용해야 합니다. `sfpkg` 파일 확장명은 ".sfpkg"여야 합니다. 프로비전 작업은 응용 프로그램 매니페스트에 나오는 응용 프로그램 유형 정보를 포함해야 합니다.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 명령은 시스템에서 응용 프로그램 패키지를 성공적으로 등록한 후에만 반환합니다. 등록에 걸리는 시간은 응용 프로그램 패키지의 크기 및 콘텐츠에 따라 다릅니다. **-TimeoutSec** 매개 변수는 필요한 경우 더 긴 제한 시간을 제공합니다(기본 제한 시간은 60초).

대형 응용 프로그램 패키지가 있거나 시간 제한이 발생하는 경우 **-Async** 매개 변수를 사용합니다. 이 명령은 클러스터가 register 명령을 수락할 때 반환됩니다. 등록 작업은 필요에 따라 계속됩니다.
[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 명령은 응용 프로그램 유형 버전과 해당 등록 상태를 나열합니다. 이 명령을 사용하여 등록이 완료된 시기를 확인할 수 있습니다.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>이미지 저장소에서 응용 프로그램 패키지 제거
패키지가 이미지 저장소에 복사된 경우 응용 프로그램이 성공적으로 등록된 후 임시 위치에서 제거해야 합니다. 이미지 저장소에서 응용 프로그램 패키지를 삭제하면 시스템 리소스가 해제됩니다. 사용되지 않는 응용 프로그램 패키지를 그대로 두면 디스크 저장소를 소비하고 응용 프로그램 성능 문제로 이어집니다.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>응용 프로그램 만들기
[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet을 사용하여 성공적으로 등록된 모든 응용 프로그램 유형 버전에서 응용 프로그램을 인스턴스화할 수 있습니다. 각 응용 프로그램의 이름은 반드시 *“fabric:”* 체계로 시작하고 각 응용 프로그램 인스턴스에 대해 고유해야 합니다. 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 모든 기본 서비스도 만들어집니다.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
등록된 응용 프로그램 형식의 주어진 어떤 버전에 대해서도 여러 개의 응용 프로그램 인스턴스를 만들 수 있습니다. 각 응용 프로그램 인스턴스는 자체 작업 디렉터리 및 프로세스와는 별도로 실행됩니다.

클러스터에서 실행 중인 명명된 응용 프로그램과 서비스를 확인하려면 [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 및 [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdlet을 실행합니다.

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>응용 프로그램 제거
응용 프로그램 인스턴스가 더 이상 필요하지 않은 경우 [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet을 사용하여 해당 응용 프로그램 인스턴스를 이름별로 영구적으로 제거할 수 있습니다. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps)은 응용 프로그램에 속한 모든 서비스를 자동으로 제거하고, 해당 서비스 상태를 모두 영구적으로 제거합니다. 

> [!WARNING]
> 이 작업은 되돌릴 수 없으며 응용 프로그램 상태는 복구할 수 없습니다.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>응용 프로그램 유형 등록 취소
특정 버전의 응용 프로그램 유형이 더 이상 필요하지 않으면 [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet을 사용하여 해당 응용 프로그램 유형을 등록 취소해야 합니다. 사용하지 않는 응용 프로그램 유형을 등록 취소하면 응용 프로그램 유형 파일을 제거하여 이미지 저장소에서 사용하는 저장 공간을 해제합니다. 이미지 저장소에 복사를 사용한 경우, 응용 프로그램 유형을 등록 취소해도 이미지 저장소 임시 위치에 복사된 응용 프로그램 패키지는 제거되지 않습니다. 응용 프로그램 형식은 이에 대해 인스턴스화된 응용 프로그램이나 이를 참조하는 보류 중인 응용 프로그램이 없는 한 등록 취소할 수 있습니다.

[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps)을 실행하여 현재 클러스터에 등록된 응용 프로그램 유형을 확인합니다.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

[Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps)을 실행하여 특정 응용 프로그램 유형의 등록을 취소합니다.

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>문제 해결
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage가 ImageStoreConnectionString을 요청함
서비스 패브릭 SDK 환경은 이미 올바른 기본 설정값을 가지고 있습니다. 하지만 필요한 경우 모든 명령에 대한 ImageStoreConnectionString은 서비스 패브릭 클러스터가 사용 중인 값과 일치해야 합니다. [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) 및 Get-ImageStoreConnectionStringFromClusterManifest 명령을 사용하여 검색된 클러스터 매니페스트에서 ImageStoreConnectionString을 찾을 수 있습니다.

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

서비스 패브릭 SDK PowerShell 모듈의 일부인 **Get ImageStoreConnectionStringFromClusterManifest** cmdlet는 이미지 저장소 연결 문자열을 가져오는 데 사용됩니다.  SDK 모듈을 가져오려면 다음을 실행합니다.

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString은 클러스터 매니페스트에 있습니다.

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

이미지 저장소 및 이미지 저장소 연결 문자열에 대한 보충 정보는 [이미지 저장소 연결 문자열 이해](service-fabric-image-store-connection-string.md)를 참조하세요.

### <a name="deploy-large-application-package"></a>대형 응용 프로그램 패키지 배포
문제: 대형 응용 프로그램 패키지(GB 단위)에 대한 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 시간이 초과되었습니다.
다음을 시도해 보세요.
- [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 명령에 `TimeoutSec` 매개 변수를 사용하여 더 긴 시간 제한을 지정합니다. 기본적으로 시간 제한은 30분입니다.
- 원본 컴퓨터와 클러스터 간의 네트워크 연결을 확인합니다. 연결 속도가 느린 경우 네트워크 연결 상태가 좋은 컴퓨터를 사용하는 것이 좋습니다.
클라이언트 컴퓨터가 클러스터가 아닌 다른 지역에 있는 경우 해당 클러스터와 가깝거나 동일한 지역에 있는 클라이언트 컴퓨터를 사용하는 것이 좋습니다.
- 외부 제한에 도달하고 있는지 확인합니다. 예를 들어 Azure 저장소를 사용하도록 이미지 저장소를 구성한 경우 업로드가 제한될 수 있습니다.

문제: 패키지 업로드가 성공적으로 완료되었지만 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 시간이 초과되었습니다. 다음을 시도해 보세요.
- 이미지 저장소에 복사하기 전에 [패키지를 압축합니다](service-fabric-package-apps.md#compress-a-package).
압축하면 파일의 크기와 수가 줄어들므로 Service Fabric에서 수행해야 하는 트래픽과 작업량도 줄어듭니다. 업로드 작업이 느려질 수 있지만(특히 압축 시간이 포함되는 경우), 응용 프로그램 유형을 더 빠르게 등록 및 등록 취소할 수 있습니다.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)에 `TimeoutSec` 매개 변수를 사용하여 더 긴 시간 제한을 지정합니다.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)에 `Async` 스위치를 지정합니다. 명령은 클러스터가 명령을 수락하고 응용 프로그램 형식의 등록을 비동기적으로 계속하는 경우 반환합니다. 이러한 이유로 더 긴 시간 제한을 지정할 필요가 없습니다. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 명령은 성공적으로 등록된 모든 응용 프로그램 유형 버전과 해당 등록 상태를 나열합니다. 이 명령을 사용하여 등록이 완료된 시기를 확인할 수 있습니다.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>많은 파일이 있는 응용 프로그램 패키지 배포
문제: 많은 파일(1,000개 단위)이 있는 응용 프로그램 패키지에 대한 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 시간이 초과되었습니다.
다음을 시도해 보세요.
- 이미지 저장소에 복사하기 전에 [패키지를 압축합니다](service-fabric-package-apps.md#compress-a-package). 압축하면 파일의 수가 줄어듭니다.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)에 `TimeoutSec` 매개 변수를 사용하여 더 긴 시간 제한을 지정합니다.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)에 `Async` 스위치를 지정합니다. 명령은 클러스터가 명령을 수락하고 응용 프로그램 형식의 등록을 비동기적으로 계속하는 경우 반환합니다.
이러한 이유로 더 긴 시간 제한을 지정할 필요가 없습니다. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 명령은 성공적으로 등록된 모든 응용 프로그램 유형 버전과 해당 등록 상태를 나열합니다. 이 명령을 사용하여 등록이 완료된 시기를 확인할 수 있습니다.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>다음 단계
[응용 프로그램 패키지 작성](service-fabric-package-apps.md)

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

[서비스 패브릭 상태 소개](service-fabric-health-introduction.md)

[서비스 패브릭 서비스 진단 및 문제 해결](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭에서 응용 프로그램 모델링](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
