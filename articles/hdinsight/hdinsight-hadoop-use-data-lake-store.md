---
title: Azure HDInsight에서 Hadoop과 함께 Data Lake Store 사용 | Microsoft Docs
description: Azure Data Lake Store에서 데이터를 쿼리하고 분석을 위해 결과를 저장하는 방법을 알아봅니다.
keywords: Blob Storage, hdfs, 구조화된 데이터, 구조화되지 않은 데이터, Data Lake Store
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 0d0fb9bad8c6120100ae3ee766aea7620dd6105f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201760"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Data Lake Store 사용

HDInsight 클러스터에서 데이터를 분석하기 위해 [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 또는 양 쪽 모두에 데이터를 저장할 수 있습니다. 두 가지 저장소 옵션을 사용하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

이 문서에서는 Data Lake Store가 HDInsight 클러스터에서 작동하는 방식에 대해 알아봅니다. Azure Storage가 HDInsight 클러스터에서 작동하는 방식에 대해 알아보려면 [Azure HDInsight 클러스터에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

> [!NOTE]
> Data Lake Store는 항상 보안 채널을 통해 액세스되기 때문에 `adls` 파일 시스템 구성표 이름이 없습니다. 항상 `adl`을 사용합니다.
> 
> 

## <a name="availabilities-for-hdinsight-clusters"></a>HDInsight 클러스터에 대한 가용성

Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미합니다. 상대 경로를 확인하기 위해 사용할 수 있습니다. HDInsight 클러스터를 만드는 과정에서 Azure Storage에서 Blob 컨테이너를 기본 파일 시스템으로 지정하거나 HDInsight 3.5 이상을 통해, 몇 가지 예외를 제외하고 Azure Storage 또는 Azure Data Lake Store를 기본 파일 시스템으로 선택할 수 있습니다. 

HDInsight 클러스터는 Data Lake Store를 두 가지 방식으로 사용할 수 있습니다.

* 기본 저장소로
* 추가 저장소로, Azure Storage Blob을 기본 저장소로.

현재는 일부 HDInsight 클러스터 유형/버전에서만 Data Lake Store를 기본 저장소 및 추가 저장소 계정으로 사용하도록 지원합니다.

| HDInsight 클러스터 유형 | 기본 저장소로 Data Lake Store | 추가 저장소로 Data Lake Store| 메모 |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 버전 3.6 | 예 | 예 | |
| HDInsight 버전 3.5 | 예 | 예 | HBase 제외|
| HDInsight 버전 3.4 | 아니오 | 예 | |
| HDInsight 버전 3.3 | 아니오 | 아니오 | |
| HDInsight 버전 3.2 | 아니오 | 예 | |
| Storm | | |Data Lake Store를 사용하여 Storm 토폴로지에서 데이터를 쓸 수 있습니다. Storm 토폴로지에서 읽을 수 있는 참조 데이터에 Data Lake Store를 사용할 수도 있습니다.|

Data Lake Store를 추가 저장소 계정으로 사용하면 클러스터에서 Azure Storage로 읽거나 쓰는 성능 또는 기능에 영향을 주지 않습니다.


## <a name="use-data-lake-store-as-default-storage"></a>Data Lake Store를 기본 저장소로 사용

HDInsight가 Data Lake Store를 기본 저장소로 하여 배포되는 경우 클러스터 관련 파일은 Data Lake Store의 다음 위치에 저장됩니다.

    adl://mydatalakestore/<cluster_root_path>/

여기서 `<cluster_root_path>`는 Data Lake Store에 만드는 폴더의 이름입니다. 각 클러스터에 대한 루트 경로를 지정하면 동일한 Data Lake Store 계정을 둘 이상의 클러스터에 사용할 수 있습니다. 따라서 다음 위치에 설정할 수 있습니다.

* 클러스터1에 `adl://mydatalakestore/cluster1storage` 경로를 사용할 수 있습니다.
* 클러스터2에 `adl://mydatalakestore/cluster2storage` 경로를 사용할 수 있습니다.

두 클러스터 모두 동일한 Data Lake Store 계정인 **mydatalakestore**를 사용하는 것에 유의하세요. 각 클러스터는 Data Lake Store의 자체 루트 파일 시스템에 액세스 권한을 갖습니다. 특히 Azure Portal 배포 환경에서는 **/clusters/\<clustername>** 과 같은 폴더 이름을 루트 경로로 사용할지 묻는 메시지가 표시됩니다.

Data Lake Store를 기본 저장소로 사용할 수 있으려면 다음 경로에 대한 서비스 주체 액세스 권한을 부여해야 합니다.

- Data Lake Store 계정 루트.  예: adl://mydatalakestore/.
- 모든 클러스터 폴더에 대한 경로.  예: adl://mydatalakestore/clusters.
- 클러스터에 대한 폴더.  예: adl://mydatalakestore/clusters/cluster1storage.

서비스 주체 및 액세스 부여에 대한 자세한 내용은 [Data Lake Store 액세스 구성](#configure-data-lake-store-access)을 참조하세요.


## <a name="use-data-lake-store-as-additional-storage"></a>추가 저장소로 Data Lake Store 사용

Azure Data Lake Store를 클러스터에 대한 추가 저장소로 사용할 수도 있습니다. 이런 경우 클러스터 기본 저장소는 Azure Storage Blob 또는 Data Lake Store 계정입니다. 추가 저장소로 Data Lake Store에 저장된 데이터에 대해 HDInsight 작업을 실행하는 경우 파일에 대한 정규화된 경로를 사용해야 합니다. 예: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

이제 URL에 **cluster_root_path**가 없습니다. 이 경우 Data Lake Store가 기본 저장소가 아니기 때문입니다. 따라서 파일에 대한 경로만 제공하면 됩니다.

Data Lake Store를 기본 저장소로 사용할 수 있으려면 파일이 저장된 다음 경로에 대한 서비스 주체 액세스 권한을 부여해야 합니다.  예: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

서비스 주체 및 액세스 부여에 대한 자세한 내용은 [Data Lake Store 액세스 구성](#configure-data-lake-store-access)을 참조하세요.


## <a name="use-more-than-one-data-lake-store-accounts"></a>둘 이상의 Data Lake Store 계정 사용

추가로 Data Lake Store 계정을 추가하고 둘 이상의 Data Lake Store 계정을 추가하는 것은 하나 이상의 Data Lake Store 계정에 있는 데이터에 HDInsight 클러스터 권한을 부여하여 수행합니다. [Data Lake Store 액세스 구성](#configure-data-lake-store-access)을 참조하세요.

## <a name="configure-data-lake-store-access"></a>Data Lake Store 액세스 구성

HDInsight 클러스터에서 Data Lake Store 액세스를 구성하려면 Azure AD(Azure Active Directory) 서비스 주체가 있어야 합니다. Azure AD 관리자만 서비스 주체를 만들 수 있습니다. 서비스 주체는 인증서로 만들어야 합니다. 자세한 내용은 [Data Lake Store 액세스 구성](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#configure-data-lake-store-access) 및 [자체 서명된 인증서로 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate)를 참조하세요.

> [!NOTE]
> Azure Data Lake Store를 HDInsight 클러스터의 추가 저장소로 사용하려는 경우 이 문서에서 설명한 대로 클러스터를 만드는 동안 이 작업을 수행하는 것이 좋습니다. 기존의 HDInsight 클러스터에 Azure Data Lake Store를 추가 저장소로 추가하는 것은 복잡한 프로세스이며 오류가 발생하기 쉽습니다.
>

## <a name="access-files-from-the-cluster"></a>클러스터에서 파일 액세스

HDInsight 클러스터에서 Data Lake Store의 파일에 액세스할 수 있는 방법은 여러 가지입니다.

* **정규화된 이름 사용**. 이 방법의 경우 액세스할 파일에 대한 전체 경로를 제공합니다.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **줄인 경로 형식 사용**. 이 방식의 경우 adl:///을 사용하여 클러스터 루트에 대한 경로를 대체합니다. 따라서 위의 예제에서 `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/`를 `adl:///`로 대체할 수 있습니다.

        adl:///<file path>

* **상대 경로 사용**. 이 방법의 경우 액세스할 파일에 대한 상대 경로만 제공합니다. 예를 들어 파일에 대한 전체 경로는 다음과 같습니다.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    상대 경로를 대신 사용하여 sample.log 파일에 액세스할 수 있습니다.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Data Lake Store에 대한 액세스로 HDInsight 클러스터 만들기

Data Lake Store에 대한 액세스로 HDInsight 클러스터를 만드는 방법에 대한 자세한 지침은 다음 링크를 사용하세요.

* [포털 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [PowerShell 사용(Data Lake Store를 기본 저장소로)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell 사용(Data Lake Store를 추가 저장소로)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure 템플릿 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight로 HDFS 호환 Azure Data Lake Store를 사용하는 방법을 알아보았습니다. 이제 장기적이고 확장성 있는 보관 데이터 취득 솔루션을 구축할 수 있으며, 저장된 구조적 및 비구조적 데이터 내부의 정보를 활용하는 데 HDInsight를 사용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)
* [HDInsight 클러스터를 만들어 Azure Portal을 사용하는 Data Lake Store 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [HDInsight 클러스터를 만들어 Azure PowerShell을 사용하는 Data Lake Store 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
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
