---
title: Azure CLI를 사용하여 Hadoop 클러스터 관리 - Azure HDInsight| Microsoft Docs
description: Azure 명령줄 인터페이스를 사용하여 Azure HDInsight의 Hadoop 클러스터를 관리하는 방법을 알아봅니다. Azure CLI는 Windows, Mac 및 Linux에서 작동합니다.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 18901c3e99b1c67d01c091918a6abdd2f298defa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Azure CLI를 사용하여 HDInsight의 Hadoop 클러스터 관리
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure 명령줄 인터페이스](../cli-install-nodejs.md)를 사용하여 Azure HDInsight의 Hadoop 클러스터를 관리하는 방법을 알아봅니다. Azure CLI는 Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다. 현재 HDInsight는 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure)을 지원하지 않습니다.

이 문서에서는 HDInsight을 통한 Azure CLI 사용에 대해서만 설명합니다. Azure CLI를 사용하는 방법에 대한 일반 가이드는 [Azure CLI 설치 및 구성][azure-command-line-tools]을 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **Azure CLI** - 설치 및 구성 정보는 [Azure CLI 설치 및 구성](../cli-install-nodejs.md) 을 참조하세요.
* **Azure에 연결**. 다음 명령을 사용합니다.

    ```cli
    azure login
    ```
  
    회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](/cli/azure/authenticate-azure-cli)을 참조하세요.
* **Azure Resource Manager 모드로 전환**은 다음 명령을 사용합니다.
  
    ```cli
    azure config mode arm
    ```

도움말을 보려면 **-h** 스위치를 사용합니다.  예: 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>CLI를 사용하여 클러스터 만들기
[Azure CLI를 사용하여 HDInsight의 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md)를 참조하세요.

## <a name="list-and-show-cluster-details"></a>클러스터 세부 정보 나열 및 표시
클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![클러스터 목록의 명령줄 보기][image-cli-clusterlisting]

## <a name="delete-clusters"></a>클러스터 삭제
클러스터를 삭제하려면 다음 명령을 사용합니다.

```cli
azure hdinsight cluster delete <Cluster Name>
```

또한 클러스터를 포함하는 리소스 그룹을 삭제하여 클러스터를 삭제할 수도 있습니다. 이렇게 하면 기본 저장소 계정을 포함한 그룹 내 모든 리소스가 삭제됩니다.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>클러스터 크기 조정
Hadoop 클러스터 크기를 변경하려면

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>클러스터에 대한 HTTP 액세스 사용/사용 안 함

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>클러스터에 대한 RDP 액세스 사용/사용 안 함

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터 관리 작업을 수행하는 여러 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 HDInsight 관리][hdinsight-admin-portal]
* [Azure PowerShell을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-powershell]
* [Azure HDInsight 시작][hdinsight-get-started]
* [Azure CLI를 사용하는 방법][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "클러스터 나열 및 표시"
