---
title: Azure Service Fabric 응용 프로그램 배포 | Microsoft Docs
description: FabricClient API를 사용하여 Service Fabric에서 응용 프로그램을 배포 및 제거합니다.
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
ms.openlocfilehash: 50c487e6bad2a009b4f719d44b129ba860432e28
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>FabricClient를 사용하여 응용 프로그램 배포 및 제거
> [!div class="op_single_selector"]
> * [리소스 관리자](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

일단 [응용 프로그램 형식이 패키지화되면][10] Azure Service Fabric 클러스터에 배포될 준비가 된 것입니다. 배포에는 다음 세 단계가 포함됩니다.

1. 이미지 저장소에 응용 프로그램 패키지 업로드
2. 응용 프로그램 형식 등록
3. 이미지 저장소에서 응용 프로그램 패키지 제거
4. 응용 프로그램 인스턴스 만들기

응용 프로그램을 배포하고 인스턴스가 클러스터에서 실행되면 응용 프로그램 인스턴스와 해당 응용 프로그램 형식을 삭제할 수 있습니다. 클러스터에서 응용 프로그램을 완전히 제거하려면 다음 단계를 수행합니다.

1. 실행 중인 응용 프로그램 인스턴스 제거(또는 삭제)
2. 더 이상 필요하지 않은 경우 응용 프로그램 유형 등록 취소

로컬 개발 클러스터에서 Visual Studio를 사용하여 응용 프로그램을 배포 및 디버그하는 경우 이전의 모든 단계는 PowerShell 스크립트를 통해 자동으로 처리됩니다.  이 스크립트는 응용 프로그램 프로젝트의 *Scripts* 폴더에 있습니다. 이 문서에서는 Visual Studio 외부에서 동일한 작업을 수행할 수 있도록 스크립트에서 수행하는 작업에 대한 배경을 설명합니다. 
 
## <a name="connect-to-the-cluster"></a>클러스터에 연결
이 문서의 코드 예제 중 하나를 실행하기 전에 [FabricClient](/dotnet/api/system.fabric.fabricclient) 인스턴스를 만들어 클러스터에 연결합니다. 로컬 개발 클러스터 또는 원격 클러스터나 Azure Active Directory, X509 인증서 또는 Windows Active Directory를 사용하여 보안된 클러스터에 연결하는 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis)을 참조하세요. 로컬 개발 클러스터에 연결하려면 다음을 실행합니다.

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>응용 프로그램 패키지 업로드
Visual Studio에서 *MyApplication*이라는 응용 프로그램을 빌드하고 패키지한다고 가정해 보겠습니다. 기본적으로 ApplicationManifest.xml에 나열된 응용 프로그램 유형 이름은 "MyApplicationType"입니다.  필요한 응용 프로그램 매니페스트, 서비스 매니페스트 및 코드/구성/데이터 패키지가 포함된 응용 프로그램 패키지는 *C:\Users\&lt;username&gt;\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Debug*에 있습니다.

응용 프로그램 패키지를 업로드하면 내부 Service Fabric 구성 요소에 의해 액세스할 수 있는 위치에 배치됩니다. Service Fabric은 응용 프로그램 패키지를 등록하는 동안 응용 프로그램 패키지를 확인합니다. 단, 로컬로 응용 프로그램 패키지를 확인하려는 경우(예: 업로드하기 전에) [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet을 사용합니다.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API는 응용 프로그램 패키지를 클러스터 이미지 저장소에 업로드합니다. 

응용 프로그램 패키지가 크거나 파일이 많은 경우 [압축](service-fabric-package-apps.md#compress-a-package)한 다음, PowerShell을 사용하여 이미지 저장소에 복사할 수 있습니다. 압축은 파일의 크기와 수를 줄입니다.

이미지 저장소 및 이미지 저장소 연결 문자열에 대한 보충 정보는 [이미지 저장소 연결 문자열 이해](service-fabric-image-store-connection-string.md)를 참조하세요.

## <a name="register-the-application-package"></a>응용 프로그램 패키지 등록
응용 프로그램 매니페스트에 선언된 응용 프로그램 형식과 버전은 응용 프로그램 패키지를 등록할 때 사용할 수 있게 됩니다. 시스템은 이전 단계에서 업로드된 패키지를 읽고, 패키지를 확인하며, 처리된 패키지를 내부 시스템 위치에 복사합니다.  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API는 클러스터에 응용 프로그램 형식을 등록한 후 배포에 사용할 수 있도록 합니다.

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API는 성공적으로 등록된 모든 응용 프로그램 형식에 대한 정보를 제공합니다. 이 API를 사용하여 등록이 완료된 시기를 확인할 수 있습니다.

## <a name="remove-an-application-package-from-the-image-store"></a>이미지 저장소에서 응용 프로그램 패키지 제거
응용 프로그램이 성공적으로 등록된 후에는 응용 프로그램 패키지를 제거하는 것이 좋습니다.  이미지 저장소에서 응용 프로그램 패키지를 삭제하면 시스템 리소스가 해제됩니다.  사용되지 않는 응용 프로그램 패키지를 그대로 두면 디스크 저장소를 소비하고 응용 프로그램 성능 문제로 이어집니다. [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API를 사용하여 이미지 저장소에서 응용 프로그램 패키지를 사용합니다.

## <a name="create-an-application-instance"></a>응용 프로그램 인스턴스 만들기
[CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API를 사용하여 성공적으로 등록된 모든 응용 프로그램 형식에서 응용 프로그램을 인스턴스화할 수 있습니다. 각 응용 프로그램의 이름은 반드시 *"fabric:"* 체계로 시작하고 각 응용 프로그램 인스턴스에 대해 고유해야 합니다(클러스터 내). 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 모든 기본 서비스도 만들어집니다.

등록된 응용 프로그램 형식의 주어진 어떤 버전에 대해서도 여러 개의 응용 프로그램 인스턴스를 만들 수 있습니다. 각 응용 프로그램 인스턴스는 자체 작업 디렉터리 및 프로세스 집합과는 별도로 실행됩니다.

클러스터에서 실행 중인 명명된 응용 프로그램과 서비스를 확인하려면 [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) 및 [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API를 실행합니다.

## <a name="create-a-service-instance"></a>서비스 인스턴스 만들기
[CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API를 사용하여 서비스 형식에서 서비스를 인스턴스화할 수 있습니다.  서비스가 응용 프로그램 매니페스트에서 기본 서비스로 선언되면 응용 프로그램이 인스턴스화될 때 해당 서비스도 인스턴스화됩니다.  이미 인스턴스화된 서비스에 대해 [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API를 호출하면 FabricErrorCode.ServiceAlreadyExists 값의 오류 코드를 포함하는 FabricException 형식의 예외를 반환하게 됩니다.

## <a name="remove-a-service-instance"></a>서비스 인스턴스 제거
서비스 인스턴스가 더 이상 필요하지 않을 경우 [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API를 호출하여 실행 중인 응용 프로그램 인스턴스에서 제거할 수 있습니다.  

> [!WARNING]
> 이 작업은 되돌릴 수 없으며 서비스 상태는 복구할 수 없습니다.

## <a name="remove-an-application-instance"></a>응용 프로그램 인스턴스 제거
응용 프로그램 인스턴스가 더 이상 필요하지 않은 경우 [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API를 사용하여 이름별로 영구적으로 제거할 수 있습니다. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync)는 모든 서비스 상태를 영구적으로 제거하고 해당 응용 프로그램에 속한 모든 서비스를 자동으로 제거합니다.

> [!WARNING]
> 이 작업은 되돌릴 수 없으며 응용 프로그램 상태는 복구할 수 없습니다.

## <a name="unregister-an-application-type"></a>응용 프로그램 유형 등록 취소
특정 버전의 응용 프로그램 형식이 더 이상 필요하지 않으면 [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API를 사용하여 해당 응용 프로그램 형식의 버전을 등록 취소해야 합니다. 사용하지 않는 응용 프로그램 형식의 버전을 등록 취소하면 이미지 저장소에서 사용하는 저장 공간이 해제됩니다. 응용 프로그램 형식의 버전은 해당 응용 프로그램 형식의 버전에 대해 인스턴스화된 응용 프로그램이나 해당 응용 프로그램 형식의 버전을 참조하는 보류 중인 응용 프로그램이 없는 한 등록 취소할 수 있습니다.

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
문제: 대형 응용 프로그램 패키지(GB 단위)에 대한 [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API 시간이 초과되었습니다.
다음을 시도해 보세요.
- [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) 메서드에 `timeout` 매개 변수를 사용하여 더 긴 시간 제한을 지정합니다. 기본적으로 시간 제한은 30분입니다.
- 원본 컴퓨터와 클러스터 간의 네트워크 연결을 확인합니다. 연결 속도가 느린 경우 네트워크 연결 상태가 좋은 컴퓨터를 사용하는 것이 좋습니다.
클라이언트 컴퓨터가 클러스터가 아닌 다른 지역에 있는 경우 해당 클러스터와 가깝거나 동일한 지역에 있는 클라이언트 컴퓨터를 사용하는 것이 좋습니다.
- 외부 제한에 도달하고 있는지 확인합니다. 예를 들어 Azure 저장소를 사용하도록 이미지 저장소를 구성한 경우 업로드가 제한될 수 있습니다.

문제: 패키지 업로드가 성공적으로 완료되었지만 [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API 시간이 초과되었습니다. 다음을 시도해 보세요.
- 이미지 저장소에 복사하기 전에 [패키지를 압축합니다](service-fabric-package-apps.md#compress-a-package).
압축하면 파일의 크기와 수가 줄어들므로 Service Fabric에서 수행해야 하는 트래픽과 작업량도 줄어듭니다. 업로드 작업이 느려질 수 있지만(특히 압축 시간이 포함되는 경우), 응용 프로그램 유형을 더 빠르게 등록 및 등록 취소할 수 있습니다.
- [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API에 `timeout` 매개 변수를 사용하여 더 긴 시간 제한을 지정합니다.

### <a name="deploy-application-package-with-many-files"></a>많은 파일이 있는 응용 프로그램 패키지 배포
문제: 많은 파일(1,000개 단위)이 있는 응용 프로그램 패키지에 대한 [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync)가 시간이 초과되었습니다.
다음을 시도해 보세요.
- 이미지 저장소에 복사하기 전에 [패키지를 압축합니다](service-fabric-package-apps.md#compress-a-package). 압축하면 파일의 수가 줄어듭니다.
- [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync)에 `timeout` 매개 변수를 사용하여 더 긴 시간 제한을 지정합니다.

## <a name="code-example"></a>코드 예제
다음 예제에서는 응용 프로그램 패키지를 이미지 저장소로 복사하고, 응용 프로그램 형식을 프로비전하고, 응용 프로그램 인스턴스를 만들고, 서비스 인스턴스를 만들고, 응용 프로그램 형식의 프로비전을 취소하고, 이미지 저장소에서 응용 프로그램 패키지를 삭제합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>다음 단계
[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

[서비스 패브릭 상태 소개](service-fabric-health-introduction.md)

[서비스 패브릭 서비스 진단 및 문제 해결](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭에서 응용 프로그램 모델링](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
