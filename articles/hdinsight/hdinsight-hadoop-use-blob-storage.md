---
title: HDFS 호환 가능 Azure Storage에서 데이터 쿼리 - Azure HDInsight | Microsoft Docs
description: Azure Storage 및 Azure Data Lake Store에서 데이터를 쿼리하고 분석을 위해 결과를 저장하는 방법을 알아봅니다.
keywords: Blob 저장소, HDFS, 구조적 데이터, 비구조적 데이터, Data Lake Store, Hadoop 입력, Hadoop 출력, Hadoop 저장소, HDFS 입력, HDFS 출력, HDFS 저장소, WASB Azure
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1d2e65f2-16de-449e-915f-3ffbc230f815
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 3430e71a45eb92af9881f4f13d414cddd8b6076a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201050"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에서 Azure Storage 사용

HDInsight 클러스터에서 데이터를 분석하기 위해 Azure Storage, Azure Data Lake Store 또는 양 쪽 모두에 데이터를 저장할 수 있습니다. 두 가지 저장소 옵션을 사용하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미합니다. 상대 경로를 확인하기 위해 사용할 수 있습니다. HDInsight 클러스터를 만드는 과정에서 Azure Storage에서 Blob 컨테이너를 기본 파일 시스템으로 지정하거나 HDInsight 3.5를 통해 몇 가지 예외를 제외하고 Azure Storage 또는 Azure Data Lake Store를 기본 파일 시스템으로 선택할 수 있습니다. 기본 및 연결된 저장소로 Data Lake Store 사용에 대한 지원 가능성은 [HDInsight 클러스터에 대한 가용성](./hdinsight-hadoop-use-data-lake-store.md#availabilities-for-hdinsight-clusters)을 참조하세요.

이 문서에서는 Azure Storage가 HDInsight 클러스터에서 작동하는 방식에 대해 알아봅니다. Data Lake Store가 HDInsight 클러스터에서 작동하는 방식에 대해 알아보려면 [Azure HDInsight 클러스터에서 Azure Data Lake Store 사용](hdinsight-hadoop-use-data-lake-store.md)을 참조하세요. HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

Azure Storage는 HDInsight와 매끄럽게 통합되는 강력한 범용 저장소 솔루션입니다. HDInsight는 Azure Storage의 Blob 컨테이너를 클러스터의 기본 파일 시스템으로 사용합니다. HDFS(Hadoop Distributed File System) 인터페이스를 통해 HDInsight의 전체 구성 요소 집합을 Blob로 저장된 구조적 또는 비구조적 데이터에 대해 직접 작동할 수 있습니다.

> [!WARNING]
> Azure Storage 계정을 만들 때 사용할 수 있는 몇 가지 옵션이 있습니다. 다음 표에서는 HDInsight에 지원되는 옵션에 대한 정보를 제공합니다.
> 
> | Storage 계정 유형 | 저장소 계층 | HDInsight에서 지원됨 |
> | ------- | ------- | ------- |
> | 범용 Storage 계정 | Standard | __예__ |
> | &nbsp; | Premium | 아니오 |
> | Blob Storage 계정 | 핫 | 아니오 |
> | &nbsp; | 쿨 | 아니오 |

기본 Blob 컨테이너는 비즈니스 데이터를 저장하는 데 사용하지 않는 것이 좋습니다. 저장소 비용을 줄이기 위해 사용한 후에는 매번 기본 Blob 컨테이너를 삭제하는 것이 좋습니다. 기본 컨테이너에는 응용 프로그램 및 시스템 로그가 포함되어 있습니다. 컨테이너를 삭제하기 전에 이러한 로그를 검색해야 합니다.

여러 클러스터에 대해 하나의 Blob 컨테이너를 공유하는 것은 지원되지 않습니다.

## <a name="hdinsight-storage-architecture"></a>HDInsight 저장소 아키텍처
다음 다이어그램은 Azure Storage 사용의 HDInsight 저장소 아키텍처의 추상 보기를 제공합니다.

![Hadoop 클러스터는 HDFS API를 사용하여 Blob Storage의 구조적 및 비구조적 데이터에 액세스하고 저장합니다.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage 아키텍처")

HDInsight는 컴퓨터 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 정규화된 URI를 사용하여 이 파일 시스템에 액세스할 수 있습니다. 예를 들어:

    hdfs://<namenodehost>/<path>

또한 HDInsight를 통해 Azure Storage에 저장된 데이터에 액세스할 수 있습니다. 구문은 다음과 같습니다:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

다음은 HDInsight 클러스터와 Azure Storage 계정을 사용하는 경우 고려 사항입니다.

* **클러스터에 연결된 저장소 계정의 컨테이너:** 계정 이름과 키는 만들기 중 클러스터와 연결되므로 사용자는 이러한 컨테이너의 Blob에 대한 모든 권한을 보유합니다.

* **클러스터에 연결되지 않은 저장소 계정의 공용 컨테이너 또는 공용 Blob:** 컨테이너의 Blob에 대한 읽기 전용 권한을 가집니다.
  
  > [!NOTE]
  > 공용 컨테이너를 사용하면 해당 컨테이너에서 사용할 수 있는 모든 Blob 목록 및 컨테이너 메타데이터를 가져올 수 있습니다. 공용 Blob을 사용하면 정확한 URL을 아는 경우에만 Blob에 액세스할 수 있습니다. 자세한 내용은 <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">컨테이너 및 Blob에 대한 액세스 제한</a>을 참조하십시오.
  > 
  > 
* **클러스터에 연결되지 않은 저장소 계정의 개인 컨테이너:** WebHCat 작업을 제출할 때 저장소 계정을 정의하지 않는 경우 컨테이너의 Blob에 액세스할 수 없습니다. 이것은 문서 뒷부분에 설명되어 있습니다.

만들기 프로세스에서 정의된 저장소 계정과 해당 키는 클러스터 노드의 %HADOOP_HOME%/conf/core-site.xml에 저장됩니다. HDInsight의 기본 동작은 core-site.xml 파일에 정의된 저장소 계정을 사용하는 것입니다. [Ambari](./hdinsight-hadoop-manage-ambari.md)를 사용하여 이 설정을 수정할 수 있습니다.

Hive, MapReduce, Hadoop 스트리밍 및 Pig를 비롯한 여러 WebHCat 작업은 저장소 계정 및 메타데이터 설명을 포함할 수 있습니다. (현재 메타데이터가 아닌 저장소 계정이 있는 Pig에서 작동합니다.) 자세한 내용은 [대체 Storage 계정 및 Metastore와 HDInsight 클러스터 사용](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)을 참조하세요.

구조적 및 비구조적 데이터에 대한 Blob을 사용할 수 있습니다. Blob 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 슬래쉬 문자(/)를 사용할 수 있습니다. 예를 들어 Blob의 키 이름을 *input/log1.txt*로 지정할 수 있습니다. 실제로 *input* 디렉터리는 없지만 키 이름에 슬래쉬 문자가 있으므로 파일 경로처럼 보입니다.

## <a id="benefits"></a>Azure Storage의 이점
계산 클러스터 및 저장소 리소스의 공동 배치에 따른 암시적 성능 비용은 계산 클러스터를 Azure 지역 내의 저장소 계정 리소스 근처에 만드는 방식으로 완화됩니다. 여기서 고속 네트워크 환경은 계산 노드가 Azure Storage 내에 있는 데이터에 효율적으로 액세스하도록 합니다.

HDFS 대신 Azure Storage에 데이터를 저장할 경우 몇 가지 이점이 있습니다:

* **데이터 다시 사용 및 공유:** HDFS의 데이터는 계산 클러스터 내에 있습니다. 계산 클러스터에 액세스할 수 있는 응용 프로그램만 HDFS API를 통해 데이터를 사용할 수 있습니다. Azure Storage의 데이터는 HDFS API 또는 [Blob Storage REST API][blob-storage-restAPI]를 통해 액세스할 수 있습니다. 따라서 데이터의 생성과 소비에 더 많은 응용 프로그램(기타 HDInsight 클러스터 포함)과 도구를 사용할 수 있습니다.
* **데이터 보관:** Azure Storage에 데이터를 저장하면 계산에 사용된 HDInsight 클러스터를 사용자 데이터 손실 없이 안전하게 삭제할 수 있습니다.
* **데이터 저장 비용:** 데이터를 장기간 저장하는 경우 DFS에 저장하는 것이 Azure Storage에 저장하는 것보다 비용이 많이 드는데, 이는 계산 클러스터의 비용이 Azure Storage의 비용보다 비싸기 때문입니다. 또한 계산 클러스터를 생성할 때마다 데이터를 다시 로드할 필요가 없기 때문에 데이터 로드 비용도 절약됩니다.
* **탄력적인 확장:** HDFS는 확장된 파일 시스템을 제공하지만, 확장은 클러스터에 대해 만드는 노드의 수에 의해 결정됩니다. 규모를 변경하는 것이 Azure Storage에서 자동으로 수행되는 탄력적인 확장 기능에 의존하는 것보다 더 복잡한 프로세스가 될 수 있습니다.
* **지역에서 복제:** Azure Storage를 지역에서 복제할 수 있습니다. 이 경우 지리적 복구와 데이터 중복 기능이 제공되지만, 지역에서 복제된 위치에 대한 장애 조치(Failover)는 성능에 심각한 영향을 미치게 되며 추가 비용을 발생시킬 수 있습니다. 따라서 지역에서 복제 기능은 추가 비용을 감수할 만큼 가치 있는 데이터에 한해서만 현명하게 사용하는 것이 좋습니다.

특정 MapReduce 작업과 패키지는 Azure Storage에 전혀 저장하고 싶지 않을 만한 중간 결과를 생성할 수 있습니다. 그런 경우에는 로컬 HDFS에 데이터를 저장하도록 선택할 수 있습니다. 실제로 HDInsight는 Hive 작업 및 기타 프로세스에서 생성되는 이러한 중간 결과 중 일부에 DFS를 사용합니다.

> [!NOTE]
> 대부분의 HDFS 명령(예를 들어, <b>ls</b>, <b>copyFromLocal</b> 및 <b>mkdir</b>)은 여전히 예상대로 작동합니다. <b>fschk</b> 및 <b>dfsadmin</b> 등 기본 HDFS 구현(DFS라고 함)에 특정된 명령만이 Azure Storage에서 다르게 동작합니다.
> 
> 

## <a name="create-blob-containers"></a>Blob 컨테이너 만들기
Blob을 사용하려면 먼저 [Azure Storage 계정][azure-storage-create]을 만듭니다. 이 작업의 일부로 저장소 계정이 만들어지는 Azure 지역을 지정합니다. 클러스터와 저장소 계정은 동일한 지역에서 호스팅되어야 합니다. Hive Metastore SQL Server 데이터베이스 및 Oozie Metastore SQL Server 데이터베이스도 동일한 지역에 위치해야 합니다.

어디에 있든, 만들어진 각 Blob은 Azure Storage 계정의 일부 컨테이너에 속합니다. 이 컨테이너는 HDInsight 외부에 생성된 기존 Blob일 수도 있고 HDInsight 클러스터용으로 생성된 컨테이너일 수도 있습니다.

기본 Blob 컨테이너는 작업 기록 및 로그와 같은 클러스터 특정 정보를 저장합니다. 여러 HDInsight 클러스터의 기본 Blob 컨테이너를 공유하지 마세요. 이 경우 작업 기록이 손상될 수 있습니다. 각 클러스터에 다른 컨테이너를 사용하고 기본 저장소 계정 대신 모든 관련 클러스터 배포에 지정된 연결 저장소 계정에서 공유 데이터를 배치하는 것이 좋습니다. 연결된 저장소 계정에 대한 자세한 내용은 [HDInsight 클러스터 만들기][hdinsight-creation]를 참조하세요. 그러나 원래 HDInsight 클러스터를 삭제한 후에 기본 저장소 컨테이너를 다시 사용할 수 있습니다. HBase 클러스터의 경우 삭제된 HBase 클러스터에서 사용되는 기본 Blob 컨테이너를 사용하여 새 HBase 클러스터를 만듦으로써 HBase 테이블 스키마 및 데이터를 실제로 보존할 수 있습니다.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Azure 포털 사용
포털에서 HDInsight 클러스터를 만드는 경우 저장소 계정 세부 정보를 제공할 수 있는 옵션(아래 그림 참조)이 있습니다. 추가 저장소 계정을 클러스터와 연결할지 여부를 지정할 수도 있으며, 연결하려는 경우, Data Lake Store 또는 다른 Azure Storage Blob을 추가 저장소로 선택할 수 있습니다.

![HDInsight Hadoop 만들기 데이터 원본](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

> [!WARNING]
> HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.


### <a name="use-azure-powershell"></a>Azure PowerShell 사용
[Azure PowerShell을 설치하고 구성한][powershell-install] 경우 Azure PowerShell 프롬프트에서 다음을 사용하여 저장소 계정 및 컨테이너를 만들 수 있습니다.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-cli"></a>Azure CLI 사용

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

[Azure CLI를 설치 및 구성한](../cli-install-nodejs.md)경우, 다음 명령을 사용하여 저장소 계정 및 컨테이너를 사용할 수 있습니다.

    azure storage account create <storageaccountname> --type LRS

> [!NOTE]
> `--type` 매개 변수는 저장소 계정이 복제된 방식을 나타냅니다. 자세한 내용은 [Azure Storage 복제](../storage/storage-redundancy.md)를 참조하세요. ZRS에서 페이지 Blob, 파일, 테이블 또는 큐를 지원하지 않으므로 ZRS를 사용하지 마세요.
> 
> 

저장소 계정에 생성된 지리적 지역을 지정하라는 메시지가 표시됩니다. HDInsight 클러스터를 만들려는 동일한 지역에 저장소 계정을 만들어야 합니다.

저장소 계정을 만든 후 다음 명령을 사용하여 저장소 계정 키를 검색합니다.

    azure storage account keys list <storageaccountname>

컨테이너를 만들려면 다음 명령을 사용합니다.

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="address-files-in-azure-storage"></a>Azure Storage에서 파일 주소 지정
HDInsight에서 Azure Storage의 파일에 액세스하기 위한 URI 구성표는 다음과 같습니다.

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

URI 체계는 암호화되지 않은 액세스(*wasb:* 접두사가 있음)와 SSL로 암호화된 액세스(*wasbs*가 있음)를 제공합니다. Azure의 동일한 지역에 있는 데이터에 액세스하는 경우에도 가능하면 *wasbs*를 사용하는 것이 좋습니다.

&lt;BlobStorageContainerName&gt;은 Azure Storage에서 Blob 컨테이너의 이름을 식별합니다.
&lt;StorageAccountName&gt;은 Azure Storage 계정 이름을 식별합니다. FQDN(정규화된 도메인 이름)이 필요합니다.

&lt;BlobStorageContainerName&gt;과 &lt;StorageAccountName&gt;이 둘 다 지정되지 않은 경우 기본 파일 시스템이 사용됩니다. 기본 파일 시스템의 파일에 대해서는 상대 경로나 절대 경로를 사용할 수 있습니다. 예를 들어, HDInsight 클러스터와 함께 제공되는 *hadoop-mapreduce-examples.jar* 파일을 가리킬 때 다음 중 하나를 사용할 수 있습니다:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> HDInsight 버전 2.1 및 1.6 클러스터에서는 파일 이름이<i>hadoop-examples.jar</i>입니다.
> 
> 

&lt;경로&gt;는 파일 또는 디렉터리 HDFS 경로 이름입니다. Azure Storage의 컨테이너는 단지 키-값 저장소이므로 실제 계층적 파일 시스템이 없습니다. Blob 키 내부의 슬래쉬 문자(/)는 디렉터리 구분기호로 해석됩니다. 예를 들어 *hadoop-mapreduce-examples.jar* 의 Blob 이름은 다음과 같습니다.

    example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> HDInsight 외부에서 blob를 작업할 때 대부분의 유틸리티는 WASB 형식을 인식하지 않으며 대신 `example/jars/hadoop-mapreduce-examples.jar`과 같은 기본 경로 형식을 예상합니다.
> 
> 

## <a name="access-blobs"></a>Blob 액세스 


### <a name="access-blobs-using-azure-powershell"></a> Azure PowerShell 사용
> [!NOTE]
> 이 섹션의 명령은 PowerShell을 사용하여 blob에 저장된 데이터에 액세스하는 기본 예제를 제공합니다. HDInsight와의 작업에 대해 사용자 지정되는 더 완전한 기능의 예는 [HDInsight 도구](https://github.com/Blackmist/hdinsight-tools)를 참조하세요.
> 
> 

다음 명령을 사용하여 Blob 관련 cmdlet을 나열합니다:

    Get-Command *blob*

![Blob 관련 PowerShell cmdlet의 목록입니다.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>파일 업로드
[HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요.

#### <a name="download-files"></a>파일 다운로드
다음 스크립트를 실행하면 블록 Blob이 현재 폴더로 다운로드됩니다. 스크립트를 실행하기 전에 디렉터리를 쓰기 권한이 있는 폴더로 변경하십시오.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Connect-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

리소스 그룹 이름 및 클러스터 이름을 제공할 때 다음 코드를 사용할 수 있습니다.

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force


#### <a name="delete-files"></a>파일 삭제
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

#### <a name="list-files"></a>파일 나열
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>정의되지 않은 저장소 계정을 사용하여 Hive 쿼리 실행
이 샘플에서는 만들기 프로세스 중에 정의되지 않은 저장소 계정에서 폴더를 나열하는 방법을 보여 줍니다.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

### <a name="use-azure-cli"></a>Azure CLI 사용
다음 명령을 사용하여 Blob 관련 명령을 나열합니다.

    azure storage blob

**파일을 업로드하기 위해 Azure CLI를 사용하는 예제**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**파일을 다운로드하기 위해 Azure CLI를 사용하는 예제**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**파일을 삭제하기 위해 Azure CLI를 사용하는 예제**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**파일을 나열하기 위해 Azure CLI를 사용하는 예제**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="use-additional-storage-accounts"></a>추가 저장소 계정 사용

HDInsight 클러스터를 만드는 동안 클러스터와 연결할 Azure Storage 계정을 지정합니다. 만들기 프로세스 중이나 클러스터를 만든 후에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

> [!WARNING]
> HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight로 HDFS 호환 Azure Storage를 사용하는 방법을 알아보았습니다. 이제 장기적이고 확장성 있는 보관 데이터 취득 솔루션을 구축할 수 있으며, 저장된 구조적 및 비구조적 데이터 내부의 정보를 활용하는 데 HDInsight를 사용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)
* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [Azure Storage 공유 액세스 서명을 사용하여 HDInsight에서 데이터 액세스 제한][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
