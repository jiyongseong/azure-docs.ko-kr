---
title: Distcp를 사용하여 WASB에(서) Data Lake Store 데이터 복사| Microsoft 문서
description: Distcp 도구를 사용하여 Azure Storage Blob에서 데이터 레이크 저장소로 데이터 복사
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: c682ff56f4850638d3cad6e327c1237a0492c4d3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Distcp를 사용하여 Azure Storage Blob과 데이터 레이크 저장소 간에 데이터 복사
> [!div class="op_single_selector"]
> * [DistCp 사용](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy 사용](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Store에 액세스할 수 있는 HDInsight 클러스터를 갖고 있다면 Distcp 같은 Hadoop 에코시스템 도구를 사용하여 HDInsight 클러스터 저장소(WASB)와 **주고 받는** 데이터를 Data Lake Store 계정에 복사할 수 있습니다. 이 문서에서는 Distcp 도구 사용 방법에 대한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure 데이터 레이크 저장소 계정**. 만드는 방법에 대한 지침은 [Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
* **Azure HDInsight 클러스터** 입니다. [Data Lake Store가 있는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.

## <a name="do-you-learn-fast-with-videos"></a>비디오로 빠르게 배우시겠습니까?
[비디오를 보세요](https://mix.office.com/watch/1liuojvdx6sie) .

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux 클러스터에서 Distcp 사용

HDInsight 클러스터는 서로 다른 원본에서 HDInsight 클러스터로 데이터를 복사하는 데 사용할 수 있는 Distcp 유틸리티와 함께 제공됩니다. Data Lake Store를 추가 저장소로 사용하도록 HDInsight 클러스터를 구성한 경우 Distcp 유틸리티는 기본적으로 Data Lake Store 계정으로/에서 데이터를 복사할 수 있습니다. 이 섹션에서는 Distcp 유틸리티를 사용하는 방법에 대해 살펴봅니다.

1. 데스크탑에서 SSH를 사용하여 클러스터에 연결합니다. [Linux 기반 HDInsight 클러스터에 연결](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. SSH 프롬프트에서 명령을 실행합니다.

2. Azure Storage Blob(WASB)에 액세스할 수 있는지 여부를 확인합니다. 다음 명령 실행:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    출력에 저장소 BLOB의 콘텐츠 목록이 제공되어야 합니다.

3. 마찬가지로 클러스터에서 데이터 레이크 저장소 계정에 액세스할 수 있는지 확인합니다. 다음 명령 실행:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    출력에 Data Lake Store 계정의 파일/폴더 목록이 제공되어야 합니다.

4. Distcp를 사용하여 WASB에서 데이터 레이크 저장소 계정에 데이터를 복사합니다.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    이 명령은 Asure Storage Blob에 있는 **/example/data/gutenberg/** 폴더의 콘텐츠를 Data Lake Store 계정의 **/myfolder** 폴더에 복사합니다.

5. 마찬가지로 Distcp를 사용하여 데이터 레이크 저장소 계정에서 WASB에 데이터를 복사합니다.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    이 명령은 Data Lake Store 계정에 있는 **/myfolder**의 콘텐츠를 Asure Storage Blob의 **/example/data/gutenberg/** 폴더에 복사합니다.

## <a name="performance-considerations-while-using-distcp"></a>DistCp 사용에 대한 성능 고려 사항

DistCp의 가장 낮은 세분성은 단일 파일이므로 최대 동시 복사본 수를 설정하는 것이 Data Lake Store에서 최적화할 가장 중요한 매개 변수입니다. 동시 복사의 수는 명령줄에서 매퍼 수(‘m’) 매개 변수를 설정하여 제어합니다. 이 매개 변수는 데이터를 복사하는 데 사용되는 최대 매퍼 수를 지정합니다. 기본값은 20입니다.

**예제**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>사용할 매퍼 수는 어떻게 확인하나요?

다음은 사용할 수 있는 몇 가지 지침입니다.

* **1단계: 전체 YARN 메모리 확인** - 첫 번째 단계는 DistCp 작업을 실행하는 클러스터에서 사용할 수 있는 YARN 메모리를 확인하는 것입니다. 이 정보는 클러스터와 연결된 Ambari 포털에서 사용할 수 있습니다. YARN으로 이동한 후 Configs 탭에서 YARN 메모리를 확인합니다. 전체 YARN 메모리를 알려면 노드당 YARN 메모리와 클러스터에 있는 노드 수를 곱합니다.

* **2단계: 매퍼 수 계산** - **m** 값은 전체 YARN 메모리를 YARN 컨테이너 크기로 나눈 몫과 같습니다. YARN 컨테이너 크기 정보도 Ambari 포털에서 사용할 수 있습니다. YARN으로 이동한 후 Configs 탭을 확인합니다. 이 창에 YARN 컨테이너 크기가 표시됩니다. 매퍼 수(**m**)를 구하는 수식은 다음과 같습니다.

        m = (number of nodes * YARN memory for each node) / YARN container size

**예제**

클러스터에 4개의 D14v2 노드가 있고 10개의 다른 폴더에서 10TB의 데이터를 전송하려고 한다고 가정해보겠습니다. 각 폴더에는 다양한 크기의 데이터가 포함되어 있고 각 폴더 내의 파일 크기가 서로 다릅니다.

* 전체 YARN 메모리 - Ambari 포털에서 D14 노드 1개의 YARN 메모리가 96GB임을 확인할 수 있습니다. 따라서 4노드 클러스터의 전체 YARN 메모리는 다음과 같습니다. 

        YARN memory = 4 * 96GB = 384GB

* 매퍼 수 - Ambari 포털에서 D14 클러스터 노드 1개에 대한 YARN 컨테이너 크기는 3072임을 확인할 수 있습니다. 따라서 매퍼 수는 다음과 같습니다.

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

다른 응용 프로그램에서 메모리를 사용하고 있으면 DistCp에 대한 클러스터 YARN 메모리 중에서 일부만 사용하도록 선택할 수 있습니다.

### <a name="copying-large-datasets"></a>큰 데이터 집합 복사

이동할 데이터 집합의 크기가 매우 크거나(예: 1TB 초과) 다른 폴더가 많이 있는 경우 여러 DistCp 작업을 사용하는 것을 고려해야 합니다. 성능이 좋아지지는 않을 수 있지만 작업이 분산되므로 한 작업이 실패해도 전체 작업이 아닌 해당 작업만 다시 시작하면 됩니다.

### <a name="limitations"></a>제한 사항

* DistCp는 성능을 최적화하기 위해 크기가 서로 비슷한 매퍼를 만들려고 합니다. 매퍼 수를 늘린다고 항상 성능이 증가하는 것은 아닐 수 있습니다.

* DistCp는 파일당 하나의 매퍼로 제한됩니다. 따라서 파일 개수보다 매퍼가 더 많지 않아야 합니다. DistCp는 파일에 하나의 매퍼를 할당하므로 큰 파일을 복사하는 데 사용할 수 있는 동시성 크기가 제한됩니다.

* 적은 수의 큰 파일이 있는 경우 이러한 파일을 256MB 파일 청크로 나누면 동시성이 높아질 수 있습니다. 
 
* Azure Blob Storage 계정에서 복사하는 경우 Blob Storage 쪽에서 복사 작업이 제한될 수도 있습니다. 따라서 복사 작업의 성능이 저하됩니다. Azure Blob Storage의 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한](../azure-subscription-service-limits.md)에서 Azure Storage 제한을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure Storage Blob에서 데이터 레이크 저장소로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 Data Lake Store 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
