---
title: 계산 노드에 응용 프로그램 패키지 설치 - Azure Batch | Microsoft Docs
description: Azure Batch의 응용 프로그램 패키지 기능을 사용하여 Batch 계산 노드에 설치할 여러 응용 프로그램 및 버전을 간편하게 관리하세요.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/06/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f982e859892965379b7ffb08e15dd1cf51b9801f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>응용 프로그램을 배포하여 Batch 응용 프로그램 패키지에서 노드 계산

Azure Batch의 응용 프로그램 패키지 기능은 풀의 계산 노드에 태스크 응용 프로그램 및 해당 배포를 간편하게 관리할 수 있는 방법을 제공합니다. 응용 프로그램 패키지를 사용하여 지원 파일을 비롯하여 태스크에서 실행하는 여러 응용 프로그램 버전을 업로드하고 관리할 수 있습니다. 풀의 계산 노드에 이러한 응용 프로그램을 하나 이상 자동으로 배포할 수 있습니다.

이 문서에서는 Azure portal을 사용하여 응용 프로그램 패키지를 업로드하고 관리하는 방법을 알아봅니다. [Batch .NET][api_net] 라이브러리를 사용하여 풀의 계산 노드에 설치하는 방법을 알아봅니다.

> [!NOTE]
> 
> 응용 프로그램 패키지는 2017년 7월 5일 이후에 만들어진 모든 Batch 풀에서 지원됩니다. 2016년 3월 10일에서 2017년 7월 5일 사이에 만들어진 Batch 풀에서는 Cloud Service 구성을 사용하여 풀을 만든 경우에만 이러한 패키지가 지원됩니다. 2016년 3월 10일 이전에 만들어진 Batch 풀은 응용 프로그램 패키지를 지원하지 않습니다.
>
> 응용 프로그램 패키지를 만들고 관리하는 API는 [Batch Management .NET][api_net_mgmt] 라이브러리의 일부입니다. 계산 노드에서 응용 프로그램 패키지를 설치하는 API는 [Batch .NET][api_net] 라이브러리의 일부입니다. 다른 언어의 경우 상응하는 기능을 Batch API에서 제공합니다. 
>
> 여기서 설명하는 응용 프로그램 패키지 기능은 이전 버전의 서비스에서 사용할 수 있는 Batch 앱 기능을 대체합니다.
> 
> 

## <a name="application-package-requirements"></a>응용 프로그램 패키지 요구 사항
응용 프로그램 패키지를 사용하려면 Batch 계정에 [Azure Storage 계정을 연결](#link-a-storage-account)해야 합니다.

## <a name="about-applications-and-application-packages"></a>응용 프로그램 및 응용 프로그램 패키지 정보
Azure Batch 내에서 *응용 프로그램* 이란 풀의 계산 노드에 자동으로 다운로드할 수 있는 버전이 지정된 이진 파일의 집합을 말합니다. *응용 프로그램 패키지*는 이러한 이진 파일의 *특정 집합*을 말하며, 지정된 응용 프로그램 *버전*을 나타냅니다.

![응용 프로그램 및 응용 프로그램 패키지에 대한 개략적인 다이어그램][1]

### <a name="applications"></a>응용 프로그램
Batch에서 응용 프로그램은 하나 이상의 응용 프로그램을 포함하거나 응용 프로그램에 대한 구성 옵션을 지정합니다. 예를 들어 응용 프로그램은 계산 노드에 설치할 기본 응용 프로그램 패키지 버전, 응용 프로그램 패키지를 업데이트할지 아니면 삭제할 것인지를 지정할 수 있습니다.

### <a name="application-packages"></a>응용 프로그램 패키지
응용 프로그램 패키지는 태스크가 응용 프로그램을 실행하는 데 필요한 응용 프로그램 이진 파일 및 지원 파일이 포함된 .zip 파일입니다. 각 응용 프로그램 패키지는 응용 프로그램의 특정 버전을 나타냅니다.

풀 및 태스크 수준에서 응용 프로그램 패키지를 지정할 수 있습니다. 풀 또는 태스크를 만들 때 이러한 패키지 및 버전(선택 사항)을 하나 이상 지정할 수 있습니다.

* **풀 응용 프로그램 패키지** 는 *모든* 노드에 배포됩니다. 응용 프로그램은 노드가 풀을 조인하고 재부팅되거나 이미지를 다시 설치할 때 배포됩니다.
  
    풀 응용 프로그램 패키지는 풀에 있는 모든 노드가 작업의 태스크를 실행할 때 적합합니다. 풀을 만들 때 하나 이상의 응용 프로그램 패키지를 지정할 수 있으며 기존 풀의 패키지를 추가하거나 업데이트할 수 있습니다. 기존 풀의 응용 프로그램 패키지를 업데이트하는 경우 새 패키지를 설치하려면 해당 노드를 다시 설치해야 합니다.
* **태스크 응용 프로그램 패키지** 는 태스크의 명령줄을 실행하기 바로 전에 태스크를 실행하도록 예약된 계산 노드에만 배포됩니다. 지정된 응용 프로그램 패키지 및 버전이 노드에 이미 있는 경우 다시 배포되지 않으며 기존 패키지가 사용됩니다.
  
    태스크 응용 프로그램 패키지는 공유 풀 환경에서 유용하며 여기서는 다른 작업이 하나의 풀에서 실행되고 작업이 완료될 때 풀이 삭제되지 않습니다. 작업에 있는 태스크가 풀에 있는 노드보다 적은 경우 태스크를 실행하는 노드에만 응용 프로그램을 배포하므로 태스크 응용 프로그램 패키지는 데이터 전송을 최소화할 수 있습니다.
  
    태스크 응용 프로그램 패키지를 활용할 수 있는 다른 시나리오는 큰 응용 프로그램을 실행하지만 태스크 수는 적은 작업입니다. 예를 들어 전처리 또는 병합 응용 프로그램이 대규모인 전처리 단계나 병합 태스크는 태스크 응용 프로그램 패키지를 활용할 수 있습니다.

> [!IMPORTANT]
> Batch 계정 내 응용 프로그램 및 응용 프로그램 패키지 수와 응용 프로그램 패키지의 최대 크기에 대한 제한이 있습니다. 이러한 제한에 대한 자세한 내용은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md) 을 참조하세요.
> 
> 

### <a name="benefits-of-application-packages"></a>응용 프로그램 패키지의 이점
응용 프로그램 패키지는 Batch 솔루션의 코드를 단순화하고, 태스크에서 실행하는 응용 프로그램을 관리하는 데 필요한 오버헤드를 낮출 수 있습니다.

응용 프로그램 패키지를 사용하면 풀의 시작 태스크가 노드에 설치할 개별 리소스 파일의 긴 목록을 지정할 필요가 없습니다. Azure Storage 또는 노드에서 응용 프로그램 파일의 여러 버전을 수동으로 관리할 필요가 없습니다. Storage 계정의 파일에 대한 액세스 권한을 제공하기 위해 [SAS URL](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 을 생성할 필요가 없습니다. Batch는 Azure Storage와 함께 백그라운드에서 작동하여 응용 프로그램 패키지를 저장하고 계산 노드에 배포합니다.

> [!NOTE] 
> 리소스 파일 및 환경 변수를 포함한 시작 태스크의 전체 크기는 32768자 이하여야 합니다. 시작 태스크가 이 제한을 초과하는 경우 응용 프로그램 패키지 사용은 또 다른 옵션입니다. 리소스 파일을 포함하는 압축된 보관 파일을 만들어 Blob으로 Azure Storage에 업로드한 다음 시작 태스크의 명령줄에서 압축을 풀 수도 있습니다. 
>
>

## <a name="upload-and-manage-applications"></a>응용 프로그램 업로드 및 관리
[Azure portal][portal] 또는 Batch Management API를 사용하여 Batch 계정에서 응용 프로그램 패키지를 관리합니다. 다음 섹션에서는 먼저 Storage 계정을 연결하는 방법을 보여 준 다음 응용 프로그램 및 패키지를 추가하고 포털에서 관리하는 방법을 설명합니다.

### <a name="link-a-storage-account"></a>Storage 계정 연결
응용 프로그램 패키지를 사용하려면 먼저 [Azure Storage 계정](batch-api-basics.md#azure-storage-account)을 Batch 계정에 연결해야 합니다. Storage 계정을 아직 구성하지 않은 경우 Batch 계정에서 **응용 프로그램** 타일을 처음으로 클릭하면 Azure Portal에서 경고를 표시합니다.



![Azure Portal의 '저장소 계정이 구성되지 않음' 경고][9]

Batch 서비스는 연결된 Storage 계정을 사용하여 응용 프로그램 패키지를 저장합니다. 계정 두 개를 연결한 후에 Batch는 연결된 Storage 계정에 저장된 패키지를 계산 노드에 자동으로 배포할 수 있습니다. Storage 계정을 Batch 계정에 연결하려면 **경고** 창에서 **Storage 계정**을 클릭한 다음, **Storage 계정**을 다시 클릭합니다.

![Azure portal에서 저장소 계정 블레이드 선택][10]

*특별히* Batch 계정과 함께 사용할 Storage 계정을 만들었으면 여기에서 선택하는 것이 좋습니다. Storage 계정을 만든 후에 **Storage 계정** 창을 사용하여 Batch 계정에 연결할 수 있습니다.

> [!NOTE] 
> 현재 [방화벽 규칙](../storage/common/storage-network-security.md)이 구성된 Azure Storage 계정에는 응용 프로그램 패키지를 사용할 수 없습니다.
> 

Batch 서비스는 Azure Storage를 사용하여 응용 프로그램 패키지를 블록 Blob으로 저장합니다. 블록 Blob 데이터에 대한 [요금이 정상적으로 청구][storage_pricing]됩니다. 비용을 최소화할 수 있도록 응용 프로그램 패키지의 크기와 숫자에 신경 쓰고, 사용하지 않는 패키지를 주기적으로 제거해 주세요.
> 
> 

### <a name="view-current-applications"></a>현재 응용 프로그램 보기
Batch 계정의 응용 프로그램을 보려면 **Batch 계정**이 표시되는 동안 왼쪽의 **응용 프로그램** 메뉴 항목을 클릭합니다.

![응용 프로그램 타일][2]

이 메뉴 옵션을 선택하면 **응용 프로그램** 창이 열립니다.

![응용 프로그램 나열][3]

이 창은 계정의 각 응용 프로그램 ID 및 다음 속성을 표시합니다.

* **패키지**: 이 응용 프로그램과 연결된 버전 번호입니다.
* **기본 버전**: 풀에 대해 응용 프로그램을 지정할 때 버전을 표시하지 않으면 설치되는 응용 프로그램 버전입니다. 이 설정은 선택 사항입니다.
* **업데이트 허용**: 패키지의 업데이트, 삭제 및 추가 기능을 허용하는지 여부를 지정하는 값입니다. 이 값을 **아니요**로 설정하면 응용 프로그램에 패키지 업데이트 및 삭제를 사용할 수 없습니다. 새 응용 프로그램 패키지 버전만 추가할 수 있습니다. 기본값은 **예**입니다.

### <a name="view-application-details"></a>응용 프로그램 세부 정보 보기
응용 프로그램에 대한 세부 정보를 확인하려면 **응용 프로그램** 창에서 응용 프로그램을 선택합니다.

![응용 프로그램 세부 정보][4]

응용 프로그램 세부 정보에서 응용 프로그램에 대해 다음 설정을 구성할 수 있습니다.

* **업데이트 허용**: 해당 응용 프로그램 패키지를 업데이트하거나 삭제할 수 있는지를 지정합니다. 이 문서에서 아래의 "응용 프로그램 패키지 업데이트 또는 삭제"를 참조하세요.
* **기본 버전**: 계산 노드에 배포할 기본 응용 프로그램 패키지를 지정합니다.
* **표시 이름**: Batch를 통해 고객에게 제공하는 서비스 UI처럼, Batch 솔루션이 응용 프로그램에 대한 정보를 표시할 때 사용할 수 있는 친숙한 이름을 지정합니다.

### <a name="add-a-new-application"></a>새 응용 프로그램 추가
새 응용 프로그램을 만들려면 응용 프로그램 패키지를 추가하고 고유한 새 응용 프로그램 ID를 지정합니다. 새 응용 프로그램 ID를 사용하여 추가하는 첫 번째 응용 프로그램 패키지도 새 응용 프로그램을 만듭니다.

**응용 프로그램** > **추가**를 클릭합니다.

![Azure portal의 새 응용 프로그램 블레이드][5]

**새 응용 프로그램** 창은 새 응용 프로그램 및 응용 프로그램 패키지의 설정을 지정하는 다음 필드를 제공합니다.

**응용 프로그램 ID**

이 필드는 새 응용 프로그램의 ID를 지정하며, 표준 Azure Batch ID 유효성 검사 규칙을 따릅니다. 응용 프로그램 ID를 제공하는 규칙은 다음과 같습니다.

* Windows 노드에서 ID에는 영숫자, 하이픈 및 밑줄의 모든 조합을 사용할 수 있습니다. Linux 노드에서는 영숫자와 밑줄만 허용됩니다.
* 포함할 수 있는 최대 문자는 64자입니다.
* Batch 계정 내에서 고유해야 합니다.
* 대/소문자를 유지하고 대/소문자를 구분하지 않습니다.

**버전**

이 필드는 사용자가 업로드하는 응용 프로그램 패키지의 버전을 지정합니다. 버전 문자열은 다음 유효성 검사 규칙을 따릅니다.

* Windows 노드에서 버전 문자열에는 영숫자, 하이픈, 밑줄 및 마침표의 모든 조합을 사용할 수 있습니다. Linux 노드에서는 버전 문자열에 영숫자와 밑줄만 사용할 수 있습니다.
* 포함할 수 있는 최대 문자는 64자입니다.
* 응용 프로그램 내에서 고유해야 합니다.
* 대/소문자를 유지하고 대/소문자를 구분하지 않습니다.

**응용 프로그램 패키지**

이 필드는 응용 프로그램 실행에 필요한 응용 프로그램 이진 파일 및 모든 지원 파일을 포함하는 .zip 파일을 지정합니다. **파일 선택** 상자 또는 폴더 아이콘을 클릭하여 응용 프로그램의 파일이 포함된 .zip 파일을 찾고 선택합니다.

파일을 선택했으면 **확인** 을 클릭하여 Azure Storage에 업로드를 시작합니다. 업로드 작업이 완료되면 포털에서 알림을 표시합니다. 업로드하는 파일의 크기 및 네트워크 연결 속도에 따라 작업 시간이 달라질 수 있습니다.

> [!WARNING]
> 업로드 작업이 완료되기 전에 **새 응용 프로그램** 창을 닫지 않습니다. 만약 닫으면 업로드 프로세스가 중지됩니다.
> 
> 

### <a name="add-a-new-application-package"></a>새 응용 프로그램 패키지 추가
기존 응용 프로그램에 대한 응용 프로그램 패키지 버전을 추가하려면 **응용 프로그램** 창에서 응용 프로그램을 선택하고, **패키지** > **추가**를 클릭합니다.

![Azure portal의 응용 프로그램 패키지 추가 블레이드][8]

여기서 볼 수 있듯이 필드가 **새 응용 프로그램** 창의 필드와 일치하지만 **응용 프로그램 ID** 상자는 비활성화되어 있습니다. 새 응용 프로그램에서 수행한 대로 새 패키지의 **버전**을 지정하고, **응용 프로그램 패키지** .zip 파일을 찾은 다음 **확인**을 클릭하여 패키지를 업로드합니다.

### <a name="update-or-delete-an-application-package"></a>응용 프로그램 패키지 업데이트 또는 삭제
기존 응용 프로그램 패키지를 업데이트하거나 삭제하려면 해당 응용 프로그램의 세부 정보를 연 다음, **패키지**를 클릭하고, 수정할 응용 프로그램 패키지 행에서 **...(줄임표)** 를 클릭하여 수행할 작업을 선택합니다.

![Azure portal에서 패키지 업데이트 또는 삭제][7]

**업데이트**

**업데이트**를 클릭하면 **패키지 업데이트** 창이 표시됩니다. 이 창은 **새 응용 프로그램 패키지** 창과 비슷하지만, 패키지 선택 필드만 활성화되므로 업로드할 새 ZIP 파일을 지정할 수 있습니다.

![Azure portal의 패키지 업데이트 블레이드][11]

**삭제**

**삭제**를 클릭하면 패키지 버전을 삭제할 것인지 묻는 메시지가 나타나고, Batch가 Azure Storage에서 패키지를 삭제합니다. 응용 프로그램의 기본 버전을 삭제하면 해당 응용 프로그램의 **기본 버전** 설정이 제거됩니다.

![응용 프로그램 삭제][12]

## <a name="install-applications-on-compute-nodes"></a>계산 노드에 응용 프로그램 설치
Azure Portal을 사용하여 응용 프로그램 패키지를 관리하는 방법을 살펴보았으며 응용 프로그램 패키지를 계산 노드에 배포하고 Batch 작업을 사용하여 실행하는 방법에 대해 설명할 수 있습니다.

### <a name="install-pool-application-packages"></a>풀 응용 프로그램 패키지 설치
풀의 모든 계산 노드에 응용 프로그램 패키지를 설치하려면 풀에 대해 응용 프로그램 패키지 *참조* 를 하나 이상 지정해야 합니다. 노드가 풀에 조인되거나 재부팅 또는 이미지로 다시 설치되는 경우 풀에 대해 지정하는 응용 프로그램 패키지는 각 계산 노드에 설치됩니다.

Batch .NET에서, 새 풀을 만들 때 또는 기존 풀에 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]를 하나 이상 지정합니다. [ApplicationPackageReference][net_pkgref] 클래스는 풀의 계산 노드에 설치할 응용 프로그램 ID 및 버전을 지정합니다.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 어떤 이유로든 응용 프로그램 패키지 배포가 실패하고 Batch 서비스에서 노드를 [사용할 수 없음][net_nodestate]으로 표시하면 해당 노드에서 실행되도록 예약된 작업이 없는 것입니다. 이 경우에 노드를 **다시 시작** 하여 패키지 배포를 다시 시작해야 합니다. 또한 노드를 다시 시작하면 노드에서 작업을 다시 예약할 수 있습니다.
> 
> 

### <a name="install-task-application-packages"></a>태스크 응용 프로그램 패키지 설치
풀과 마찬가지로 태스크에 대해 응용 프로그램 패키지 *참조* 를 지정합니다. 노드에서 실행하도록 태스크가 예약된 경우 태스크의 명령줄이 실행되기 바로 전에 패키지가 다운로드 및 추출됩니다. 지정된 패키지 및 버전이 노드에 이미 설치되어 있는 경우 패키지는 다운로드되지 않으며 기존 패키지가 사용됩니다.

태스크 응용 프로그램 패키지를 설치하려면 태스크의 [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] 속성을 구성합니다.

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>설치된 응용 프로그램 실행
풀 또는 태스크에 대해 지정한 패키지가 노드의 `AZ_BATCH_ROOT_DIR` 내에 있는 명명된 디렉터리에 다운로드되어 추출됩니다. 또한 Batch는 명명된 디렉터리에 대한 경로가 포함된 환경 변수를 생성합니다. 태스크 명령줄은 노드에서 응용 프로그램을 참조할 때 이 환경 변수를 사용합니다. 

Windows 노드에서는 변수가 다음과 같은 형식입니다.

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux 노드에서는 형식이 약간 다릅니다. 마침표(.), 하이픈(-) 및 숫자 기호(#)가 환경 변수에서 밑줄로 표시됩니다. 또한 응용 프로그램 ID의 대/소문자는 유지됩니다. 예: 

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` 및 `version`은 배포를 위해 지정한 응용 프로그램 및 패키지 버전에 해당하는 값입니다. 예를 들어 Windows 노드에 *blender* 응용 프로그램의 2.7 버전을 설치하도록 지정하면 태스크 명령줄은 다음 환경 변수를 사용하여 해당 파일에 액세스합니다.

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux 노드에서는 환경 변수를 다음 형식으로 지정합니다. 마침표(.), 하이픈(-) 및 숫자 기호(#)를 밑줄로 평면화하고 응용 프로그램 ID의 대/소문자를 유지합니다.

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

응용 프로그램 패키지를 업로드할 때 계산 노드에 배포할 기본 버전을 지정할 수 있습니다. 응용 프로그램의 기본 버전을 지정하면 응용 프로그램 참조 시 버전 접미사를 생략할 수 있습니다. [응용 프로그램 업로드 및 관리](#upload-and-manage-applications)에서 표시된 것처럼 Azure Portal의 **응용 프로그램** 창에서 기본 응용 프로그램 버전을 지정할 수 있습니다.

예를 들어 *blender* 응용 프로그램에 대해 기본 버전으로 "2.7"을 설정한 경우 태스크가 다음 환경 변수를 참조하면 Windows 노드에서 버전 2.7을 실행합니다.

`AZ_BATCH_APP_PACKAGE_BLENDER`

다음 코드 조각은 기본 버전의 *blender* 응용 프로그램을 실행하는 태스크 명령줄의 예를 보여 줍니다.

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 계산 노드 환경 설정에 대한 자세한 내용은 [Batch 기능 개요](batch-api-basics.md)의 [태스크 환경 설정](batch-api-basics.md#environment-settings-for-tasks)을 참조하세요.
> 
> 

## <a name="update-a-pools-application-packages"></a>풀의 응용 프로그램 패키지 업데이트
기존 풀이 이미 응용 프로그램 패키지를 통해 구성된 경우 해당 풀에 대해 새 패키지를 지정할 수 있습니다. 풀에 대한 새 패키지 참조를 지정하면 다음이 적용됩니다.

* Batch 서비스는 풀에 조인하는 모든 새 노드와 다시 부팅되거나 이미지로 다시 설치되는 기존 노드에 새로 지정된 패키지를 설치합니다.
* 패키지 참조를 업데이트할 때 이미 풀에 있는 Compute 노드는 새 응용 프로그램 패키지를 자동으로 설치하지 않습니다. 이러한 계산 노드를 재부팅하거나 이미지로 다시 설치하여 새 패키지를 받아야 합니다.
* 새 패키지가 배포될 때 만들어진 환경 변수는 새 응용 프로그램 패키지 참조를 반영합니다.

이 예에서는 기존 풀의 *blender* 응용 프로그램 2.7 버전이 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] 중 하나로 구성되었습니다. 풀의 노드를 2.76b 버전으로 업데이트하려면 새 버전을 사용하여 [ApplicationPackageReference][net_pkgref]를 지정하고 변경 내용을 커밋합니다.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

새 버전이 구성되었으므로 Batch 서비스는 풀에 조인하는 모든 *새* 노드에 2.76b 버전을 설치합니다. *이미* 풀에 있는 노드에 2.76b 버전을 설치하려면 노드를 재부팅하거나 이미지로 다시 설치합니다. 다시 부팅된 노드는 이전 패키지 배포의 파일을 보존합니다.

## <a name="list-the-applications-in-a-batch-account"></a>Batch 계정의 응용 프로그램 나열
[ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] 메서드를 사용하여 Batch 계정의 응용 프로그램 및 응용 프로그램 패키지를 나열할 수 있습니다.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>마무리
응용 프로그램 패키지를 사용하면 고객이 작업에 대한 응용 프로그램을 선택하고 Batch 지원 서비스를 통해 작업을 처리할 때 사용할 정확한 버전을 지정할 수 있습니다. 또한 고객이 서비스에서 자신의 고유한 응용 프로그램을 업로드 및 추적하는 기능을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 또한 [Batch REST API][api_rest]는 응용 프로그램 패키지로 작업하도록 지원합니다. 예를 들어 REST API를 사용하여 설치할 패키지를 지정하는 방법에 대한 자세한 내용은 [계정에 풀 추가][rest_add_pool]에서 [applicationPackageReferences][rest_add_pool_with_packages] 요소를 참조하세요. Batch REST API를 사용하여 응용 프로그램 정보를 얻는 방법에 대한 자세한 내용은 [응용 프로그램][rest_applications]을 참조하세요.
* 프로그래밍 방식으로 [Batch 관리 .NET으로 Azure Batch 계정 및 할당량 관리](batch-management-dotnet.md)를 수행하는 방법을 알아보세요. [Batch 관리.NET][api_net_mgmt] 라이브러리는 Batch 응용 프로그램 또는 서비스에 대해 계정 만들기 및 삭제 기능을 활성화할 수 있습니다.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "응용 프로그램 패키지에 대한 개략적인 다이어그램"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure portal의 응용 프로그램 타일"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure portal의 응용 프로그램 블레이드"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure portal의 응용 프로그램 세부 정보 블레이드"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure portal의 새 응용 프로그램 블레이드"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure portal의 패키지 업데이트 또는 삭제 드롭다운"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure portal의 새 응용 프로그램 패키지 블레이드"
[9]: ./media/batch-application-packages/app_pkg_09.png "연결된 Storage 계정 없음 경고"
[10]: ./media/batch-application-packages/app_pkg_10.png "포털에서 저장소 계정 블레이드 선택"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure portal의 패키지 업데이트 블레이드"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure portal의 패키지 삭제 확인 대화 상자"
