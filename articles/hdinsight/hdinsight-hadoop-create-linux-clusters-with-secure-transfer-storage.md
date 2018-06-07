---
title: Azure HDInsight에서 보안 전송 저장소 계정으로 Hadoop 클러스터 만들기 | Microsoft Docs
description: 보안 전송이 활성화된 Azure 저장소 계정을 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
keywords: hadoop 시작,hadoop linux,hadoop 빠른 시작,보안 전송,azure 저장소 계정
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 7a2022fd5cd67120b8dc435a950533e07017218c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200523"
---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight에서 보안 전송 저장소 계정으로 Hadoop 클러스터 만들기

[보안 전송 필요](../storage/common/storage-require-secure-transfer.md) 기능은 보안 연결을 통해 모든 요청을 계정에 적용하여 Azure Storage 계정의 보안을 강화합니다. 이 기능과 wasbs 체계는 HDInsight 클러스터 버전 3.6 이상에서만 지원됩니다. 

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**: 1개월 평가판 계정을 만들려면 [azure.microsoft.com/free](https://azure.microsoft.com/free)로 이동합니다.
* **보안 전송이 활성화된 Azure Storage 계정**. 자세한 지침은 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 및 [보안 전송 필요](../storage/common/storage-require-secure-transfer.md)를 참조하세요.
* **저장소 계정의 Blob 컨테이너**. 
## <a name="create-cluster"></a>클러스터 만들기

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


이 섹션에서는 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-template-deploy.md)을 사용하여 HDInsight에서 Hadoop 클러스터를 만듭니다. 이 템플릿은 [Gibhub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/)에 있습니다. 이 자습서를 따라 하는 데 Resource Manager 템플릿 환경이 필요하지는 않습니다. 기타 클러스터 생성 방법 및 이 자습서에 사용된 속성에 대한 이해는 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

1. Azure에 로그인하여 Azure Portal에서 Azure Resource Manager 템플릿을 열려면 다음 이미지를 클릭합니다. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 지침에 따라 다음과 같은 사양의 클러스터를 만듭니다. 

    - HDInsight 버전 3.6을 지정합니다.  3.6 이상 버전이 필요합니다.
    - 보안 전송이 활성화된 저장소 계정을 지정합니다.
    - 저장소 계정에 짧은 이름을 사용합니다.
    - 저장소 계정과 blob 컨테이너를 미리 만들어 두어야 합니다. 

    자세한 지침은 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요. 

스크립트 동작을 사용하여 고유의 구성 파일을 제공하는 경우 다음과 같은 설정에서 wasbs를 사용해야 합니다.

- fs.defaultFS(코어 사이트)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>추가 저장소 계정 추가

보안 전송이 활성화된 저장소 계정을 추가할 수 있는 여러 옵션이 있습니다.

- 마지막 섹션에서 Azure Resource Manager 템플릿을 수정합니다.
- [Azure Portal](https://portal.azure.com)을 사용하여 클러스터를 만들고 연결된 저장소 계정을 지정합니다.
- 스크립트 동작을 사용하여 보안 전송이 활성화된 저장소 계정을 기존 HDInsight 클러스터에 추가합니다.  자세한 내용은 [HDInsight에 추가 저장소 계정 추가](hdinsight-hadoop-add-storage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 HDInsight 클러스터를 만들고 저장소 계정에 보안 전송을 사용하도록 설정하는 방법을 배웠습니다.

HDInsight를 사용하여 데이터를 분석하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* Visual Studio에서 Hive 쿼리를 수행 하는 방법을 비롯하여 HDInsight로 Hive를 사용하는 데 대한 자세한 내용은 [HDInsight로 Hive 사용][hdinsight-use-hive]을 참조하세요.
* 데이터를 변환하는 데 사용되는 언어인 Pig에 대해 알아보려면 [HDInsight로 Pig 사용][hdinsight-use-pig]을 참조하세요.
* Hadoop에서 데이터를 처리하는 프로그램을 작성하는 방법인 MapReduce에 대해 알아보려면 [HDInsight로 MapReduce 사용][hdinsight-use-mapreduce]을 참조하세요.
* Visual Studio에 HDInsight Tools를 사용하여 HDInsight의 데이터를 분석하는 방법에 대해 알아보려면 [HDInsight용 Visual Studio Hadoop 도구를 사용하여 시작](hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

HDInsight가 데이터를 저장하는 방법 또는 HDInsight로 데이터를 가져오는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* HDInsight에서 Azure Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.
* 데이터를 HDInsight로 업로드하는 방법에 대한 정보는 [HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하십시오.

HDInsight 클러스터를 만드는 방법 또는 관리하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* Linux 기반 HDInsight 클러스터 관리에 대해 알아보려면 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.
* HDInsight 클러스터를 만들 때 선택하는 옵션에 대해 자세히 알아보려면 [사용자 지정 옵션을 사용하여 Linux에 HDInsight 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* Linux 및 Hadoop를 사용하는 것이 익숙하지만 HDInsight에서 Hadoop에 대한 구체적인 정보를 알아보려면 [Linux에서 HDInsight로 작업](hdinsight-hadoop-linux-information.md)을 참조하세요. 이 문서에서 제공하는 정보는 다음과 같습니다.
  
  * 클러스터에서 호스트되는 서비스(예: Ambari 및 WebHCat)의 URL
  * 로컬 파일 시스템의 Hadoop 파일 및 예제의 위치
  * 기본 데이터 저장소로 HDFS 대신 Azure Storage(WASB) 사용

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md


