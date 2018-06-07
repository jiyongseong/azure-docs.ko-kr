---
title: Azure Portal을 사용하여 HDInsight의 Hadoop 클러스터 관리 | Microsoft Docs
description: Azure Portal을 사용하여 HDInsight 클러스터를 만들고 관리하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jgao
ms.openlocfilehash: 90261e090f87a5ca0d92b86c33addce2449cfd24
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361974"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure Portal][azure-portal]을 사용하여 Azure HDInsight에서 Hadoop 클러스터를 관리할 수 있습니다. 다른 도구를 사용하여 HDInsight에서 Hadoop 클러스터를 관리하는 방법에 대한 정보를 보려면 위쪽에 있는 탭 선택기를 사용하세요.

**필수 구성 요소**

이 문서의 단계를 수행하려면 **Azure 구독**이 필요합니다. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## <a name="open-the-azure-portal"></a>Azure 포털 열기
1. [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.
2. 포털을 연 후 다음을 수행할 수 있습니다.

   * 왼쪽 메뉴에서 **리소스 만들기**를 클릭하여 새 클러스터를 만듭니다.

       ![새 HDInsight 클러스터 단추](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       **마켓플레이스 검색**에서 **HDInsight**를 입력하고, **HDInsight**를 클릭한 다음, **만들기**를 클릭합니다.

   * 왼쪽 메뉴에서 **HDInsight 클러스터**를 클릭하여 기존 클러스터를 나열합니다.

       ![Azure 포털 HDInsight 클러스터 단추](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       **HDInsight 클러스터** 단추가 표시되지 않으면 **인텔리전스 + 분석** 섹션 아래쪽에 있는 **HDInsight 클러스터**를 클릭합니다.


## <a name="create-clusters"></a>클러스터 만들기
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight는 다양한 Hadoop 구성 요소에서 작동합니다. 검증되어 지원되는 구성 요소 목록은 [Azure HDInsight에 포함된 Hadoop 버전은 무엇인가요?](hdinsight-component-versioning.md)를 참조하세요. 클러스터를 만드는 방법에 대한 일반적인 정보는 [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

### <a name="access-control-requirements"></a>Access Control 요구 사항

HDInsight 클러스터를 만들 때 Azure 구독을 지정해야 합니다. 클러스터는 새 Azure 리소스 그룹 또는 기존 리소스 그룹에서 만들 수 있습니다. 다음 단계를 사용하여 HDInsight 클러스터를 만들기 위한 권한을 확인할 수 있습니다.

- 새 리소스 그룹을 만들려면 다음을 수행합니다.

    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    2. 왼쪽 메뉴에서 **구독**을 클릭합니다. 노란색 키 아이콘이 있습니다. 구독의 목록이 표시됩니다.
    3. 클러스터를 만드는 데 사용할 구독을 클릭합니다. 
    4. **내 사용 권한**을 클릭합니다.  구독에 [역할](../role-based-access-control/overview.md#built-in-roles)이 표시됩니다. HDInsight 클러스터를 만들기 위해서는 참여자 액세스 권한 이상이 필요합니다.

- 기존 리소스 그룹을 사용하려면 다음을 수행합니다.

    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    2. 왼쪽 메뉴에서 **리소스 그룹**을 클릭하여 리소스 그룹을 나열합니다.
    3. HDInsight 클러스터를 만드는 데 사용할 리소스 그룹을 클릭합니다.
    4. **액세스 제어(IAM)** 를 클릭하고 사용자(또는 사용자가 속한 그룹)에게 리소스 그룹에 대한 참가자 액세스 권한 이상이 있는지 확인합니다.

NoRegisteredProviderFound 오류 또는 MissingSubscriptionRegistration 오류가 발생하면 [Azure 리소스 관리자를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="list-and-show-clusters"></a>클러스터 나열 및 표시
1. [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터** 를 클릭하여 기존 클러스터를 나열합니다. **HDInsight 클러스터**가 표시되지 않으면 먼저 **모든 서비스**를 클릭합니다.
3. 클러스터 이름을 클릭합니다. 클러스터 목록이 긴 경우 페이지 상단의 필터를 사용할 수 있습니다.
4. 개요 페이지를 보려면 목록에서 클러스터를 클릭합니다.

    ![Azure Portal HDInsight 클러스터 기본 정보](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **개요 메뉴:**
    * **대시보드**: 클러스터에 대한 Ambari Web UI를 엽니다.
    * **보안 셸**: SSH(보안 셸) 연결을 사용하여 클러스터에 연결하는 지침을 보여 줍니다.
    * **클러스터 크기 조정**: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.
    * **이동**: 클러스터를 다른 리소스 그룹 또는 다른 구독으로 이동시킵니다.
    * **삭제**: 클러스터를 삭제합니다.

    **왼쪽 메뉴:**
    * **활동 로그**: 활동 로그를 표시하고 쿼리합니다.
    * **Access Control(IAM)**: 역할 할당을 사용합니다.  [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
    * **태그**: 태그를 사용하면 Cloud Services의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 **project**라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.
    * **문제 진단 및 해결**: 문제 해결 정보를 표시합니다.
    * **잠금**: 클러스터가 수정되거나 삭제되지 않도록 잠금을 추가합니다.
    * **Automation 스크립트**: 클러스터에 대한 Azure Resource Manager 템플릿을 표시하고 내보냅니다. 현재는 Azure Storage 계정만 내보낼 수 있습니다. [Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.
    * **빠른 시작**: HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.
    * **HDInsight용 도구**: HDInsight 관련 도구에 대한 도움말 정보입니다.
    * **구독 코어 사용량**: 구독에 사용된 코어 및 사용 가능한 코어를 표시합니다.
    * **클러스터 크기 조정**: 클러스터 작업자 노드의 수를 늘리거나 줄입니다. [클러스터 크기 조정](hdinsight-administer-use-management-portal.md#scale-clusters)을 참조하세요.
    * **SSH + 클러스터 로그인**: SSH(Secure Shell) 연결을 사용하여 클러스터에 연결하기 위한 지침을 표시합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
    * **HDInsight 파트너**: 현재 HDInsight 파트너를 추가/제거합니다.
    * **외부 Metastore**: Hive 및 Oozie Metastore를 표시합니다. Metastore는 클러스터 생성 과정 중에만 구성될 수 있습니다. [Hive/Oozie metastore 사용](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore)을 참조하세요.
    * **스크립트 작업**: 클러스터에서 Bash 스크립트를 실행합니다. [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.
    * **응용 프로그램**: HDInsight 응용 프로그램을 추가/제거합니다.  [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.
    * **모니터링**: Azure Log Analytics에서 클러스터를 모니터링합니다.
    * **속성**: 클러스터 속성을 표시합니다.
    * **Storage 계정**: Storage 계정 및 키를 봅니다. 저장소 계정은 클러스터를 만드는 과정에서 구성됩니다.
    * **Data Lake Store 액세스**: Data Lake Stores에 대한 액세스를 구성합니다.  [Azure Portal을 사용하여 Data Lake Store가 있는 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.
    * **리소스 상태**: [Azure Resource Health 개요](../service-health/resource-health-overview.md)를 참조하세요.
    * **새 지원 요청**: Microsoft 지원에 지원 티켓을 만들 수 있습니다.
    
6. **속성**을 클릭합니다.

    속성은 다음과 같습니다.

   * **호스트 이름**: 클러스터 이름입니다.
   * **클러스터 URL**: Ambari 웹 인터페이스에 대한 URL입니다.
   * **보안 셸(SSH)**: SSH를 통해 클러스터에 액세스할 때 사용할 사용자 이름과 호스트 이름입니다.
   * **상태**: 중단됨, 수락됨, 클러스터 저장소가 프로비전됨, Azure VM 구성, HDInsight 구성, 작동 가능, 실행 중, 오류, 삭제 중, 삭제됨, 시간이 초과됨, 삭제가 큐에 대기됨, 삭제 시간이 초과됨, 삭제 오류, 패치가 큐에 대기됨, 인증서 롤오버가 큐에 대기됨, 크기가 조정되어 큐에 대기됨 또는 클러스터 사용자 지정 중 하나입니다.
   * **지역**: Azure 위치입니다. 지원되는 Azure 위치를 보려면, **HDInsight 가격** 의 [지역](https://azure.microsoft.com/pricing/details/hdinsight/)드롭다운 목록 상자를 참고하세요.
   * **만든 날짜**: 클러스터가 배포된 날짜입니다.
   * **운영 체제**: **Windows** 또는 **Linux**입니다.
   * **형식**: Hadoop, HBase, Storm, Spark.
   * **버전**. [HDInsight 버전](hdinsight-component-versioning.md) 참조
   * **구독**: 구독 이름입니다.
   * **기본 데이터 원본**: 기본 클러스터 파일 시스템입니다.
   * **작업자 노드 크기**: 선택한 작업자 노드의 VM 크기입니다.
   * **헤드 노드 크기**: 선택한 헤드 노드의 VM 크기입니다.
   * **가상 네트워크**: 배포 시 하나를 선택한 경우 해당 클러스터가 배포되는 Virtual Network의 이름입니다.

## <a name="delete-clusters"></a>클러스터 삭제
클러스터를 삭제하더라도 기본 저장소 계정이나 연결된 저장소 계정은 삭제하지 않습니다. 동일한 저장소 계정과 동일한 Metastore를 사용하여 클러스터를 다시 만들 수 있습니다. 클러스터를 다시 만들 때 새 기본 Blob 컨테이너를 사용하는 것이 좋습니다.

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터** 를 클릭합니다. **HDInsight 클러스터**가 표시되지 않으면 먼저 **모든 서비스**를 클릭합니다.
3. 삭제할 클러스터를 클릭합니다.
4. 상단 메뉴에서 **삭제** 를 클릭하고 지침을 따릅니다.

참고 항목: [클러스터 일시 중지/종료](#pauseshut-down-clusters)

## <a name="add-additional-storage-accounts"></a>추가 저장소 계정 추가

클러스터를 만든 후 Azure Storage 계정 및 Azure Data Lake Store 계정을 더 추가할 수 있습니다. 자세한 내용은 [HDInsight에 추가 저장소 계정 추가](./hdinsight-hadoop-add-storage.md)를 참조하세요.

## <a name="scale-clusters"></a>클러스터 크기 조정
클러스터 크기 조정 기능을 사용하면 클러스터를 다시 만들지 않고 Azure HDInsight 클러스터에서 사용되는 작업자 노드 수를 변경할 수 있습니다.

> [!NOTE]
> HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다.  [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.
>
>

데이터 노드 수 변경에 따른 영향은 다음과 같이 HDInsight에서 지원하는 각 클러스터 유형에 따라 다릅니다.

* Hadoop은

    모든 보류 중인 또는 실행 중인 작업에 영향을 주지 않고 실행되는 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 오류는 정상적으로 처리되므로 클러스터는 항상 기능 상태로 남아 있습니다.

    데이터 노드 수를 줄여 Hadoop 클러스터를 축소하면 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.
* HBase

    HBase 클러스터가 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 클러스터의 헤드 노드에 로그인한 다음 명령 프롬프트 창에서 다음 명령을 실행하여 자동으로 지역 서버의 균형을 맞출 수도 있습니다.

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    HBase 셸을 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache HBase 예제 시작](hbase/apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

* Storm

    실행 중인 동안 Storm 클러스터에 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 토폴로지 균형을 다시 조정해야 합니다.

    다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.

  * Storm 웹 UI
  * 명령줄 인터페이스(CLI) 도구

    자세한 내용은 [Apache Storm 설명서](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

    Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.

    ![HDInsight Storm 규모 균형 재조정](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    다음은 Storm 토폴로지 균형을 다시 조정하는 CLI 명령의 예제입니다.

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**클러스터 크기를 조정하려면**

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터** 를 클릭합니다.
3. 크기 조정하려는 클러스터를 클릭합니다.
3. **클러스터 크기 조정**을 클릭합니다.
4. **작업자 노드 수**를 입력합니다. 클러스터 노드 수에 대한 제한은 Azure 구독 간에 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의할 수 있습니다.  비용 정보는 노드 수에 대한 변경 내용을 반영합니다.

    ![HDinsight Hadoop Hbase Storm Spark 크기 조정](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>클러스터 일시 중지/종료

대부분의 Hadoop 작업은 이따금 실행되는 일괄 처리 작업입니다. 대부분의 Hadoop 클러스터는 프로세스에 사용되지 않는 기간이 깁니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다.
HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

프로세스를 프로그래밍할 수 있는 방법은 다양합니다.

* 사용자 Azure 데이터 팩터리. 주문형 HDInsight 연결된 서비스 만들기는 [Azure Data Factory를 사용하여 HDInsight에서 주문형 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-adf.md) 를 참조하세요.
* Azure PowerShell 사용  [비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data.md)을 참조하세요.
* Azure CLI 사용 [Azure CLI를 사용하여 HDInsight 클러스터 관리](hdinsight-administer-use-command-line.md)를 참조하세요.
* HDInsight .NET SDK 사용 [Hadoop 작업 제출](hadoop/submit-apache-hadoop-jobs-programmatically.md)을 참조하세요.

가격 정보는 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요. 포털에서 클러스터를 삭제하려면 [클러스터 삭제](#delete-clusters)

## <a name="move-cluster"></a>클러스터 이동

다른 Azure 리소스 그룹 또는 다른 구독에 HDInsight 클러스터를 이동할 수 있습니다.  [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.

## <a name="upgrade-clusters"></a>클러스터 업그레이드

[HDInsight 클러스터를 최신 버전으로 업그레이드](./hdinsight-upgrade-cluster.md)를 참조하세요.

## <a name="open-the-ambari-web-ui"></a>Ambari 웹 UI 열기

Ambari는 RESTful API에서 지원하는 직관적이고 사용하기 쉬운 Hadoop 관리 웹 UI를 제공합니다. Ambari를 사용하면 시스템 관리자가 Hadoop 클러스터를 관리하고 모니터링할 수 있습니다.

1. Azure Portal에서 HDInsight 클러스터를 엽니다.  [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.
2. **클러스터 대시보드**를 클릭합니다.

    ![HDInsight Hadoop 클러스터 메뉴](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. 클러스터 사용자 이름 및 암호를 입력합니다.  기본 클러스터 사용자 이름은 _admin_입니다. Ambari 웹 UI는 다음과 같습니다.

    ![HDInsight Hadoop Ambari 웹 UI](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="change-passwords"></a>암호 변경
HDInsight 클러스터마다 두 개의 사용자 계정이 포함될 수 있습니다. HDInsight 클러스터 사용자 이름(HTTP 사용자 계정이라고도 함) 및 SSH 사용자 계정은 만들기 프로세스 중에 만들어집니다. Ambari 웹 UI를 사용하여 클러스터 사용자 계정의 사용자 이름 및 암호를 변경할 수 있으며 스크립트 작업을 사용하여 SSH 사용자 계정을 변경할 수 있습니다.

### <a name="change-the-cluster-user-password"></a>클러스터 사용자 암호 변경
Ambari 웹 UI를 사용하여 클러스터 사용자 암호를 변경할 수 있습니다. Ambari에 로그인하려면 기존 클러스터 사용자 이름 및 암호를 사용해야 합니다.

> [!NOTE]
> 클러스터 사용자(관리자) 암호를 변경하면 이 클러스터에 대해 실행하는 스크립트 동작이 실패할 수 있습니다. 작업자 노드를 대상으로 하는 지속적인 스크립트 작업이 있는 경우 이러한 스크립트는 작업의 크기 조정을 통해 클러스터에 노드를 추가할 때 실패할 수 있습니다. 스크립트 작업에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.
>
>

1. HDInsight 클러스터 사용자 자격 증명을 사용하여 Ambari 웹 UI에 로그인합니다. 기본 사용자 이름은 **admin**입니다. URL은 **https://&lt;HDInsight Cluster Name>azurehdinsight.net**입니다.
2. 위쪽 메뉴에서 **관리자** 를 클릭한 다음 "Ambari 관리"를 클릭합니다.
3. 왼쪽 메뉴에서 **사용자**를 클릭합니다.
4. **Admin**을 클릭합니다.
5. **암호 변경**을 클릭합니다.

Ambari는 클러스터의 모든 노드에 대해 암호를 변경합니다.

### <a name="change-the-ssh-user-password"></a>SSH 사용자 암호 변경
1. 텍스트 편집기를 사용하여 다음 텍스트를 **changepassword.sh**라는 파일로 저장합니다.

    > [!IMPORTANT]
    > 줄 끝으로 LF를 사용하는 편집기를 사용해야 합니다. 편집기에서 CRLF를 사용하는 경우 스크립트가 작동하지 않습니다.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. HTTP 또는 HTTPS 주소를 사용하여 HDInsight에서 액세스할 수 있는 저장소 위치에 파일을 업로드합니다. 예를 들어 OneDrive 또는 Azure Blob 저장소와 같은 공용 파일 저장소입니다. 다음 단계에서 이 URI가 필요하므로 URI(HTTP 또는 HTTPS 주소)를 파일에 저장합니다.
3. Azure Portal에서 **HDInsight 클러스터**를 클릭합니다.
4. HDInsight 클러스터를 클릭합니다.
4. **스크립트 작업**을 클릭합니다.
4. **스크립트 동작** 블레이드에서 **새로운 항목 제출**을 선택합니다. **스크립트 동작 제출** 블레이드가 나타나면 다음 정보를 입력합니다.

   | 필드 | 값 |
   | --- | --- |
   | Name |SSH 암호 변경 |
   | Bash 스크립트 URI |Changepassword.sh 파일에 대한 URI |
   | 노드(헤드, 작업자, Nimbus, 감독자, Zookeeper 등) |나열된 모든 노드 형식에 대한 ✓ |
   | 매개 변수 |SSH 사용자 이름 및 새 암호를 입력합니다. 사용자 이름과 암호 사이에 공백이 하나 있어야 합니다. |
   | 이 스크립트 작업을 유지... |이 필드는 선택 취소로 둡니다. |
5. **만들기**를 선택하여 스크립트를 적용합니다. 스크립트가 완료되면 새 암호와 함께 SSH를 사용하여 클러스터에 연결할 수 있습니다.

## <a name="grantrevoke-access"></a>액세스 권한 부여/해지
HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 끝점이 있음).

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. [Azure CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) 및 [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access)을 사용하여 액세스 권한을 해지/부여할 수 있습니다.

## <a name="find-the-subscription-id"></a>구독 ID 찾기

**Azure 구독 ID를 찾으려면**

1. [포털][azure-portal]에 로그인합니다.
2. **구독**을 클릭합니다. 각 구독에는 이름 및 ID가 있습니다.

각 클러스터가 Azure 구독에 연결됩니다. ID 구독은 클러스터 **필수** 타일에 표시됩니다. [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.

## <a name="find-the-resource-group"></a>리소스 그룹 찾기
Azure Resource Manager 모드에서는 각각의 HDInsight 클러스터가 Azure Resource Manager 그룹과 함께 만들어집니다. 클러스터가 속하는 Resource Manager 그룹이 아래 표시됩니다.

* 클러스터 목록에 **리소스 그룹** 열이 있습니다.
* 클러스터 **Essential** 타일.  

[클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.

## <a name="find-the-storage-accounts"></a>저장소 계정 찾기

HDInsight 클러스터는 Azure Storage 계정 또는 Azure Data Lake Store를 사용하여 데이터를 저장합니다. 각 HDInsight 클러스터에는 하나의 기본 저장소 계정 및 여러 연결된 저장소 계정이 있을 수 있습니다. Storage 계정을 나열하려면 먼저 포털에서 클러스터를 열고 **Storage 계정**을 클릭합니다.

![HDInsight 클러스터 저장소 계정](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

이전 스크린샷에는 계정이 기본 저장소 계정인지를 나타내는 __기본__ 열이 있습니다.

Data Lake Store 계정을 나열하려면 이전 스크린샷에서 **Data Lake Store 액세스**를 클릭합니다.

## <a name="run-hive-queries"></a>Hive 쿼리 실행
Azure 포털에서 직접 Hive 작업을 실행할 수는 없지만 Ambari 웹 UI에서 Hive View를 사용할 수 있습니다.

**Ambari Hive View를 사용하여 Hive 쿼리를 실행하려면**

1. HDInsight 클러스터 사용자 자격 증명을 사용하여 Ambari 웹 UI에 로그인합니다. 기본 사용자 이름은 **admin**입니다. URL은 **https://&lt;HDInsight Cluster Name>azurehdinsight.net**입니다.
2. 다음 스크린샷에 표시된 것처럼 Hive View를 엽니다.  

    ![HDInsight Hive 보기](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. 위쪽 메뉴에서 **쿼리** 를 클릭합니다.
4. **쿼리 편집기**에서 Hive 쿼리를 입력한 다음 **실행**을 클릭합니다.

## <a name="monitor-jobs"></a>작업 모니터링
[Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md#monitoring)를 참조하세요.

## <a name="browse-files"></a>파일 찾아보기
Azure 포털을 사용하여 기본 컨테이너의 콘텐츠를 찾아볼 수 있습니다.

1. [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터** 를 클릭하여 기존 클러스터를 나열합니다.
3. 클러스터 이름을 클릭합니다. 클러스터 목록이 긴 경우 페이지 상단의 필터를 사용할 수 있습니다.
4. 클러스터 왼쪽 메뉴에서 **Storage 계정**을 클릭합니다.
5. Storage 계정을 클릭합니다.
7. **Blob** 타일을 클릭합니다.
8. 기본 컨테이너 이름을 클릭합니다.

## <a name="monitor-cluster-usage"></a>클러스터 사용 현황 모니터링
HDInsight 클러스터 블레이드의 **사용량** 섹션에는 HDInsight에서 사용하기 위해 구독에서 사용할 수 있는 코어 수뿐만 아니라 이 클러스터에 할당된 코어 수 및 이 클러스터 내에서 노드에 할당된 방법에 대한 정보도 표시됩니다. [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.

> [!IMPORTANT]
> HDInsight 클러스터에 의해 제공되는 서비스를 모니터링하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>클러스터에 연결

* [HDInsight에서 하이브 사용](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>다음 단계

이 문서에서는 몇 가지 기본 관리 함수에 대해 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)
* [Azure CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md)
* [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)
* [Ambari 웹 UI 사용에 대한 자세한 내용](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API 사용에 자세한 내용](hdinsight-hadoop-manage-ambari-rest-api.md)
* [HDInsight에서 Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight에서 Sqoop 사용](hadoop/hdinsight-use-sqoop.md)
* [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight에 포함된 Hadoop 버전](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop 명령줄"
