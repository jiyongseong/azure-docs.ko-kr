---
title: Azure Batch의 컨테이너 워크로드 | Microsoft Docs
description: Azure Batch에서 컨테이너 이미지의 응용 프로그램을 실행하는 방법을 알아봅니다.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885879"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch에서 컨테이너 응용 프로그램 실행

Azure Batch를 사용하면 Azure에서 많은 수의 일괄 처리 계산 작업을 실행하고 확장할 수 있습니다. 지금까지는 Batch 작업이 Batch 풀의 VM(Virtual Machines)에서 직접 실행되었지만 이제는 Docker 컨테이너의 작업을 실행하도록 Batch 풀을 설정할 수 있습니다. 이 문서에서는 Batch .NET SDK를 사용하여 컨테이너 작업의 실행을 지원하는 계산 노드 풀을 만드는 방법과 풀에서 컨테이너 작업을 실행하는 방법을 보여 줍니다.

컨테이너를 사용하면 환경 및 종속 파일을 관리할 필요없이 Batch 작업을 실행하여 응용 프로그램을 쉽게 실행할 수 있습니다. 컨테이너는 응용 프로그램을 다양한 환경에서 실행될 수 있는 이식이 가능한 일체형 경량 단위로 배포합니다. 예를 들어, 컨테이너를 로컬로 빌드 및 테스트한 다음, Azure의 레지스트리 또는 다른 위치로 컨테이너 이미지를 업로드할 수 있습니다. 컨테이너 배포 모델은 응용 프로그램을 호스트하는 위치에 관계없이 응용 프로그램의 런타임 환경이 항상 올바르게 설치 및 구성되도록 합니다. Batch의 컨테이너 기반 작업은 리소스 파일 및 출력 파일 관리, 응용 프로그램 패키지 등 컨테이너가 아닌 작업의 기능을 활용할 수도 있습니다. 

이 문서에서는 사용자가 Docker 컨테이너 개념과 .NET SDK를 사용하여 Batch 풀 및 작업을 만드는 방법에 익숙하다고 가정합니다. 클라이언트 응용 프로그램에서 [DotNetTutorial 샘플](batch-dotnet-get-started.md)과 비슷한 코드 조각을 사용할 수 있으며 이러한 코드 조각은 Batch에서 컨테이너 응용 프로그램을 지원하는 데 필요한 코드 예제에 해당합니다.


## <a name="prerequisites"></a>필수 조건

* **SDK 버전**: Batch SDK에서 지원하는 컨테이너 이미지의 버전은 다음과 같습니다.
    * Batch REST API 버전 2017-09-01.6.0
    * Batch .NET SDK 버전 8.0.0
    * Batch Python SDK 버전 4.0
    * Batch Java SDK 버전 3.0
    * Batch Node.js SDK 버전 3.0

* **계정**: Azure 구독에서 Batch 계정을 만들고, 필요에 따라 Azure Storage 계정도 만들어야 합니다.

* **지원되는 VM 이미지**: 컨테이너는 다음 섹션, “지원되는 가상 머신 이미지”에 자세히 설명된 이미지의 Virtual Machine 구성으로 만든 풀에서만 지원됩니다. 사용자 지정 이미지를 제공하는 경우 응용 프로그램은 컨테이너 기반 워크로드를 실행하기 위해 [Azure AD(Azure Active Directory) 인증](batch-aad-auth.md)을 사용해야 합니다. 


## <a name="supported-virtual-machine-images"></a>지원되는 가상 머신 이미지

컨테이너 워크로드에 대한 VM 계산 노드 풀을 만들려면 지원되는 Windows 또는 Linux 이미지를 사용해야 합니다.

### <a name="windows-images"></a>Windows 이미지

Windows 컨테이너 워크로드의 경우, Batch는 현재 Windows에서 Docker를 실행하는 VM에서 만든 사용자 지정 이미지를 지원합니다. 또는 Azure Marketplace의 컨테이너 이미지를 Windows Server 2016 Datacenter에 사용할 수 있습니다. 이 이미지는 `batch.node.windows amd64` 노드 에이전트 SKU ID와 호환됩니다. 지원되는 컨테이너 유형은 현재 Docker로 제한됩니다.

### <a name="linux-images"></a>Linux 이미지

Linux 컨테이너 워크로드의 경우 Batch는 현재 Linux 배포 버전 Ubuntu 16.04 LTS 또는 CentOS 7.3에서 Docker를 실행하는 VM에서 만든 사용자 지정 이미지만 지원합니다. 자체 사용자 지정 Linux 이미지를 제공하려는 경우 [관리되는 사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](batch-custom-images.md)의 지침을 참조하세요.

Docker 지원을 위해 [Docker CE(Community Edition)](https://www.docker.com/community-edition) 또는 [Docker EE(Enterprise Edition)](https://www.docker.com/enterprise-edition)를 설치합니다.

Azure NC의 GPU 성능 또는 NV VM 크기를 활용하려면 이미지에 NVIDIA 드라이버를 설치해야 합니다. 또한 NVIDIA GPU용 Docker 엔진 유틸리티인 [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)를 설치하고 실행해야 합니다.

Azure RDMA 네트워크에 액세스하려면 RDMA 가능 VM 크기(예: A8, A9, H16r, H16mr 또는 NC24r)를 사용합니다. 필요한 RDMA 드라이버가 Azure Marketplace의 CentOS 7.3 HPC 및 Ubuntu 16.04 LTS 이미지에 설치됩니다. MPI 워크로드를 실행하기 위한 추가 구성이 필요할 수 있습니다. [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요.


## <a name="limitations"></a>제한 사항

* Batch는 Linux 풀에서 실행되는 컨테이너에 대해서만 RDMA 지원을 제공합니다.


## <a name="authenticate-using-azure-active-directory"></a>Azure Active Directory를 사용하여 인증

사용자 지정 VM 이미지를 사용하여 Batch 풀을 만드는 경우 클라이언트 응용 프로그램은 Azure AD 통합 인증을 사용하여 인증을 받아야 합니다(공유 키 인증은 작동하지 않음). 응용 프로그램을 실행하기 전에 Azure AD에 등록하여 ID를 설정하고 해당 사용 권한을 다른 응용 프로그램으로 지정해야 합니다.

또한 사용자 지정 VM 이미지를 사용할 경우 VM 이미지에 액세스하기 위해 응용 프로그램에 IAM 액세스 제어 권한을 부여해야 합니다. Azure Portal에서 **모든 리소스**를 클릭하고 컨테이너 이미지를 선택한 후 이미지 페이지의 **액세스 제어(IAM)** 섹션에서 **추가**를 클릭합니다. **권한 추가** 페이지에서 **역할**을 지정하고 **다음에 대한 액세스 할당**에서 **Azure AD 사용자, 그룹 또는 응용 프로그램**을 선택하고 **선택**에 응용 프로그램 이름을 입력합니다.

응용 프로그램에서 Batch 클라이언트를 만들 때 Azure AD 인증 토큰을 전달합니다. Batch .NET SDK를 사용하여 개발하는 경우 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명된 대로 [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)을 사용합니다.


## <a name="reference-a-vm-image-for-pool-creation"></a>풀 생성을 위해 VM 이미지 참조

응용 프로그램 코드에서 풀의 계산 노드를 만드는 데 사용할 VM 이미지에 대한 참조를 제공합니다. [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) 개체를 만들어 이 작업을 수행합니다. 다음 방법 중 하나로 사용할 이미지를 지정할 수 있습니다.

* 사용자 지정 이미지를 사용하는 경우 가상 머신 이미지에 대한 Azure Resource Manager 리소스 식별자를 제공합니다. 이미지의 식별자는 다음 예제와 같은 경로 형식을 갖습니다.

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Azure Portal에서 이 이미지 ID를 가져오려면 **모든 리소스**를 열고 사용자 지정 이미지를 선택한 후 이미지 페이지의 **개요** 섹션에서 **리소스 ID**에 해당 경로를 복사합니다.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) 이미지를 사용하는 경우 [가상 머신 이미지 목록](batch-linux-nodes.md#list-of-virtual-machine-images)에 나열된 이미지의 게시자, 제품 유형, SKU 및 버전을 설명하는 매개 변수 그룹을 제공합니다.

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Batch 풀에 대한 컨테이너 구성

Batch 풀을 사용하여 컨테이너 워크로드를 실행하려면 해당 풀의 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 개체에서 [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 설정을 지정해야 합니다.

다음 예제와 같이 프리페치된 컨테이너 이미지를 사용하거나 사용하지 않고 컨테이너 지원 풀을 만들 수 있습니다. 끌어오기(또는 프리페치) 프로세스를 사용하면 Docker 허브 또는 인터넷의 다른 컨테이너 레지스트리에서 컨테이너 이미지를 미리 로드할 수 있습니다. 컨테이너 이미지를 프리페치할 때의 장점은 작업을 처음 실행하기 시작할 때 컨테이너 이미지가 다운로드될 때까지 기다릴 필요가 없다는 것입니다. 컨테이너 구성은 풀이 생성될 때 컨테이너 이미지를 VM으로 끌어옵니다. 그러면 풀에서 실행되는 작업은 컨테이너 이미지 및 컨테이너 실행 옵션 목록을 참조할 수 있습니다.



### <a name="pool-without-prefetched-container-images"></a>프리페치된 컨테이너 이미지 없는 풀

프리페치된 컨테이너 이미지 없이 컨테이너 지원 풀을 구성하려면 다음 예제와 같이 `ContainerConfiguration` 및 `VirtualMachineConfiguration` 개체를 정의합니다. 이 예제와 다음 예제에서는 Docker 엔진이 설치된 사용자 지정 Ubuntu 16.04 LTS 이미지를 사용하고 있다고 가정합니다.

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>컨테이너 구성을 위해 이미지 프리페치

컨테이너 이미지를 풀에 프리페치하려면 컨테이너 이미지 목록(`containerImageNames`)을 `ContainerConfiguration`에 추가하고 이미지 목록에 이름을 지정합니다. 다음 예제에서는 사용자 지정 Ubuntu 16.04 LTS 이미지를 사용하고 있으며 [Docker 허브](https://hub.docker.com)에서 TensorFlow 이미지를 프리페치한다고 가정합니다. 이 예에는 풀 노드의 VM 호스트에서 실행되는 시작 작업이 포함되어 있습니다. 예를 들어 컨테이너에서 액세스할 수 있는 파일 서버를 탑재하려는 경우 호스트에서 시작 작업을 실행할 수 있습니다.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>개인 컨테이너 레지스트리에서 이미지 프리페치

개인 컨테이너 레지스트리 서버에서 인증을 받아 컨테이너 이미지를 프리페치할 수도 있습니다. 다음 예제에서는 `ContainerConfiguration` 및 `VirtualMachineConfiguration` 개체가 사용자 지정 Ubuntu 16.04 LTS 이미지를 사용하며 개인 Azure Container Registry에서 개인 TensorFlow 이미지를 프리페치한다고 가정합니다.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>작업(task)에 대한 컨테이너 설정

계산 노드에서 컨테이너 작업을 실행하려면 작업 실행 옵션, 사용할 이미지 및 레지스트리와 같은 컨테이너별 설정을 지정해야 합니다.

작업 클래스의 `ContainerSettings` 속성을 사용하여 컨테이너별 설정을 구성합니다. 이러한 설정은 [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) 클래스에 의해 정의됩니다.

컨테이너 이미지에 대해 작업(task)를 실행하는 경우 [클라우드 작업(task)](/dotnet/api/microsoft.azure.batch.cloudtask) 및 [작업(Job) 관리자 작업(task)](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)에 컨테이너 설정이 필요합니다. 그러나 [시작 태스크](/dotnet/api/microsoft.azure.batch.starttask), [작업(Job) 준비 작업(task)](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) 및 [작업(Job) 관리자 작업(task)](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)에는 컨테이너 설정이 필요하지 않습니다(즉, 컨테이너 컨텍스트 내에서 또는 노드에서 직접 실행될 수 있음).

컨테이너 설정을 구성하면 `AZ_BATCH_NODE_ROOT_DIR`(노드에서 Azure Batch 디렉터리의 루트) 아래의 모든 디렉터리가 재귀적으로 컨테이너에 매핑되고, 모든 작업 환경 변수가 컨테이너에 매핑되고, 작업 명령줄이 컨테이너에서 실행됩니다.

[컨테이너 구성을 위해 이미지 프리페치](#prefetch-images-for-container-configuration)의 코드 예제에서는 시작 태스크에 대한 컨테이너 구성을 지정하는 방법을 보여 줍니다. 다음 코드 예제에서는 클라우드 작업에 대한 컨테이너 구성을 지정하는 방법을 보여 줍니다.

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>다음 단계

* [Shipyard recipes](https://github.com/Azure/batch-shipyard/tree/master/recipes)를 통해 Azure Batch에 컨테이너 작업을 쉽게 배포할 수 있는 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 도구 키트도 참조하세요.

* Linux에서 Docker CE를 설치 및 사용하는 방법에 대한 자세한 내용은 [Docker](https://docs.docker.com/engine/installation/) 설명서를 참조하세요.

* 사용자 지정 이미지 사용에 대한 자세한 내용은 [관리되는 사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](batch-custom-images.md)를 참조하세요.
