---
title: Azure HDInsight Linux 클러스터에 Presto 설치 | Microsoft Docs
description: 스크립트 작업을 사용하여 Linux 기반 HDInsight Hadoop 클러스터에 Presto 및 Airpal을 설치하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 32b7925b7414f00dfdd7d5c8a45b3601bf58942e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop 클러스터에 Presto 설치 및 사용

이 문서에서는 스크립트 작업을 사용하여 HDInsight Hadoop 클러스터에 Presto를 설치하는 방법에 대해 알아봅니다. 또한 기존 Presto HDInsight 클러스터에 Airpal을 설치하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> 이 문서의 단계에는 Linux를 사용하는 **HDInsight 3.5 Hadoop 클러스터**가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전](hdinsight-component-versioning.md)을 참조하세요.

## <a name="what-is-presto"></a>Presto란?
[Presto](https://prestodb.io/overview.html)는 빅 데이터에 대한 빠른 분산된 SQL 쿼리 엔진입니다. Presto는 페타바이트의 데이터를 대화형으로 쿼리하는 데 적합합니다. Presto의 구성 요소 및 작동 방법에 대한 자세한 내용은 [Presto 개념](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)을 참조하세요.

> [!WARNING]
> HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
> 
> Presto와 같은 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/)).
> 
> 


## <a name="install-presto-using-script-action"></a>스크립트 작업을 사용하여 Presto 설치

이 섹션에서는 Azure 포털을 사용하여 새 클러스터를 만들면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다. 

1. [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-create-linux-clusters-portal.md)의 단계를 사용하여 클러스터 프로비전을 시작합니다. **사용자 지정** 클러스터 만들기 흐름을 사용하여 클러스터를 만듭니다. 클러스터는 다음 요구 사항을 충족해야 합니다.

    * HDInsight 버전 3.5를 사용하는 Hadoop 클러스터여야 합니다.

    * 데이터 저장소로 Azure Storage를 사용해야 합니다. 저장소 옵션으로 Azure Data Lake Store를 사용하는 클러스터에서 Presto 사용은 아직 지원되지 않습니다. 

    ![사용자 지정 옵션을 사용하여 HDInsight 클러스터 만들기](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. **고급 설정** 영역에서 **스크립트 작업**을 선택하고 아래 정보를 제공합니다.
   
   * **이름**: 스크립트 동작의 이름을 입력합니다.
   * **Bash 스크립트 URI**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: 이 옵션 선택
   * **WORKER**: 이 옵션을 선택합니다.
   * **ZOOKEEPER**: 이 확인란의 선택 취소
   * **PARAMETERS**: 이 필드는 공백으로 둡니다.


3. **스크립트 작업** 영역의 아래쪽에서 **선택** 단추를 클릭하여 구성을 저장합니다. 마지막으로 **고급 설정** 영역 아래쪽의 **선택** 단추를 클릭하여 구성 정보를 저장합니다.

4. [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터를 계속 프로비전합니다.

    > [!NOTE]
    > Azure PowerShell, Azure CLI, HDInsight .NET SDK 또는 Azure Resource Manager 템플릿을 스크립트 동작을 적용하는 데 사용할 수도 있습니다. 이미 실행 중인 클러스터에도 스크립트 동작을 적용할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>HDInsight와 함께 Presto 사용

HDInsight 클러스터에서 Presto를 사용하려면 다음 단계를 사용합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
     

2. 다음 명령을 사용하여 Presto 셸을 시작합니다.
   
        presto --schema default

3. 모든 HDInsight 클러스터에서 기본으로 사용할 수 있는 **hivesampletable** 샘플 테이블에서 쿼리를 실행합니다.
   
        select count (*) from hivesampletable;
   
    기본적으로 Presto에 대한 [Hive](https://prestodb.io/docs/current/connector/hive.html) 및 [TPCH](https://prestodb.io/docs/current/connector/tpch.html) 커넥터는 이미 구성되어 있습니다. Hive 커넥터는 기본 설치된 Hive 설치를 사용하도록 구성되므로 Hive의 모든 테이블은 Presto에 자동으로 표시됩니다.

    자세한 내용은 [Presto 설명서](https://prestodb.io/docs/current/index.html)를 참조하세요.

## <a name="use-airpal-with-presto"></a>Presto와 함께 Airpal 사용

[Airpal](https://github.com/airbnb/airpal#airpal)은 Presto에 대한 오픈 소스 웹 기반 쿼리 인터페이스입니다. Airpal에 대한 자세한 내용은 [Airpal 설명서](https://github.com/airbnb/airpal#airpal)를 참조하세요.

에지 노드에서 Airpal을 설치하려면 다음 단계를 사용합니다.

1. SSH를 사용하여 Presto를 설치한 HDInsight 클러스터의 헤드 노드에 연결합니다.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 연결한 후 다음 명령을 실행합니다.

        sudo slider registry  --name presto1 --getexp presto 
   
    다음 JSON과 비슷한 출력이 표시됩니다.

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 출력에서 **값** 속성에 대한 값을 적어 둡니다. 클러스터 에지 노드에 Airpal을 설치하는 동안 이 값이 필요합니다. 위의 출력에서 필요한 값은 **10.0.0.12:9090**입니다.

4. **[여기](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** 의 템플릿을 사용하여 HDInsight 클러스터 에지 노드를 만들고 다음 스크린샷에 표시된 대로 값을 제공합니다.

    ![HDInsight에서 Presto 클러스터에 Airpal 설치](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. **구매**를 클릭합니다.

6. 변경 내용이 클러스터 구성에 적용되면 다음 단계를 사용하여 Airpal 웹 인터페이스에 액세스할 수 있습니다.

    1. 클러스터 대화 상자에서 **응용 프로그램**을 클릭합니다.

        ![HDInsight에서 Presto 클러스터에 Airpal 시작](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. **설치된 앱** 영역에서 airpal에 대한 **포털**을 클릭합니다.

        ![HDInsight에서 Presto 클러스터에 Airpal 시작](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. 대화 상자가 나타나면 HDInsight Hadoop 클러스터를 만들 때 지정한 관리자 자격 증명을 입력합니다.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>HDInsight 클러스터에서 Presto 설치 사용자 지정

설치를 사용자 지정하려면 다음 단계를 사용합니다.

1. SSH를 사용하여 Presto를 설치한 HDInsight 클러스터의 헤드 노드에 연결합니다.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` 파일에서 구성을 변경합니다. Presto 구성에 대한 자세한 내용은 [YARN 기반 클러스터에 대한 Presto 구성](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html)을 참조하세요.

3. 현재 실행 중인 Presto 인스턴스를 중지하고 종료합니다.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. 사용자 지정으로 Presto의 새 인스턴스를 시작합니다.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. 새 인스턴스가 준비될 때까지 기다리고 presto 코디네이터 주소를 적어 둡니다.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Presto를 실행하는 HDInsight 클러스터에 대한 벤치마크 데이터를 생성합니다.

TPC-DS는 빅 데이터 시스템을 비롯한 여러 의사 결정 지원 시스템의 성능을 측정하기 위한 업계 표준입니다. Presto를 사용하여 데이터를 생성하고 사용자 고유의 HDInsight 벤치마크 데이터와 비교하는 방법을 평가합니다. 자세한 내용은 [여기](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)를 참조하세요.



## <a name="see-also"></a>참고 항목
* [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)입니다. Hue는 Pig 및 Hive 작업을 쉽게 만들고 실행하고 저장할 수 있도록 하는 웹 UI입니다.

* [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.

* [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
