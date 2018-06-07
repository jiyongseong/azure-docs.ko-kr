---
title: 프로그래밍 방식으로 Hadoop YARN 응용 프로그램 로그에 액세스 - Azure | Microsoft Docs
description: HDInsight의 Hadoop 클러스터에서 프로그래밍 방식으로 응용 프로그램 로그에 액세스합니다.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: aab7865548c034cb550874c31977b05936dc45b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Windows 기반 HDInsight에서 YARN 응용 프로그램 로그에 액세스
이 문서에서는 Azure HDInsight의 Windows 기반 Hadoop 클러스터에서 완료된 YARN 응용 프로그램에 대한 로그에 액세스하는 방법에 대해 설명합니다.

> [!IMPORTANT]
> 이 문서의 정보는 Windows 기반 HDInsight 클러스터에만 적용됩니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요. Linux 기반 HDInsight 클러스터에서 YARN 로그 액세스에 대한 정보는 [HDInsight의 Linux 기반 Hadoop에 대한 YARN 응용 프로그램 로그 액세스](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>필수 조건
* Windows 기반 HDInsight 클러스터입니다.  [HDInsight에서 Windows 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

## <a name="yarn-timeline-server"></a>YARN Timeline Server
<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> (영문)는 두 가지 다른 인터페이스를 통해 완료된 응용 프로그램에 대한 제네릭 정보 및 프레임워크별 응용 프로그램 정보를 제공합니다. 구체적으로 살펴보면 다음과 같습니다.

* 3.1.1.374 이상 버전에서는 HDInsight 클러스터에서 제네릭 응용 프로그램 정보를 저장하고 검색할 수 있습니다.
* Timeline Server의 프레임워크별 응용 프로그램 정보 구성 요소는 HDInsight 클러스터에서 현재 사용할 수 없습니다.

응용 프로그램에 대한 제네릭 정보에는 다음과 같은 데이터가 포함됩니다.

* 응용 프로그램의 고유한 식별자인 응용 프로그램 ID
* 응용 프로그램을 시작한 사용자
* 응용 프로그램을 완료하려고 시도한 횟수
* 지정된 응용 프로그램 시도에 사용된 컨테이너

HDInsight 클러스터에서 이 정보는 Azure Resource Manager에서 저장합니다. 이 정보는 클러스터의 기본 저장소에 있는 기록 저장소에 저장됩니다. 완료된 응용 프로그램에 대한 이 제네릭 데이터는 REST API를 통해 검색할 수 있습니다.

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN 응용 프로그램 및 로그
YARN은 여러 프로그래밍 모델을 지원하여 리소스 관리를 응용 프로그램 예약/모니터링과 분리합니다. YARN은 *리소스 관리자*(RM), 작업자 노드별 *노드 관리자*(NM) 및 응용 프로그램별 *응용 프로그램 마스터*(AM)를 사용합니다. 응용 프로그램별 AM은 응용 프로그램을 실행하기 위한 리소스(CPU, 메모리, 디스크, 네트워크)를 RM과 협상합니다. RM은 NM과 협력하여 이러한 리소스를 부여하며, 이 리소스는 *컨테이너는*로 부여됩니다. AM은 RM에 의해 부여받은 컨테이너의 진행률 추적합니다. 응용 프로그램의 특성에 따라 응용 프로그램에 여러 컨테이너가 필요할 수 있습니다.

* 각 응용 프로그램은 여러 *응용 프로그램 시도*로 구성될 수 있습니다. 
* 컨테이너는 응용 프로그램의 특정 시도에 부여됩니다. 
* 컨테이너는 기본 작업 단위에 대한 컨텍스트를 제공합니다. 
* 컨테이너의 컨텍스트 내에서 수행되는 작업은 컨테이너가 할당되는 단일 작업자 노드에서 수행됩니다. 

자세한 내용은 [YARN 개념][YARN-concepts]을 참조하세요.

응용 프로그램 로그(및 연관된 컨테이너 로그)는 문제가 있는 Hadoop 응용 프로그램을 디버깅하는 데 매우 중요합니다. YARN은 [로그 집계][log-aggregation] 기능을 사용하여 응용 프로그램 로그를 수집, 집계 및 저장하기 위한 유용한 프레임워크를 제공합니다. 로그 집계 기능은 응용 프로그램이 완료된 후 작업자 노드의 모든 컨테이너에서 로그를 집계하고 이를 작업자 노드별 하나의 집계 파일로 기본 파일 시스템에 저장하므로 응용 프로그램 로그에 더 명확하게 액세스할 수 있도록 지원합니다. 응용 프로그램은 수백 수천 개의 컨테이너를 사용할 수 있지만 단일 작업자 노드에서 실행되는 모든 컨테이너에 대한 로그는 단일 파일로 집계되므로, 응용 프로그램에서 작업자 노드당 하나의 파일만 사용합니다. 로그 집계는 HDInsight 클러스터(버전 3.0 이상)에서 기본적으로 사용하도록 설정되며 집계된 로그는 클러스터의 기본 컨테이너인 다음 위치에 있습니다.

    wasb:///app-logs/<user>/logs/<applicationId>

이 위치에서 *user*는 응용 프로그램을 시작한 사용자의 이름이고, *applicationId*는 YARN RM이 할당한 응용 프로그램의 고유 식별자입니다.

집계된 로그는 컨테이너별로 인덱싱된 [이진 형식][binary-format]인 [TFile][T-file]로 작성되므로 직접 읽을 수 없습니다. YARN에서는 관심 있는 응용 프로그램 또는 컨테이너에 대한 이러한 로그를 일반 텍스트로 덤프하는 CLI 도구를 제공합니다. RDP를 통해 연결한 후 클러스터 노드에서 직접 다음 YARN 명령 중 하나를 실행하여 이러한 로그를 일반 텍스트로 볼 수 있습니다.

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI 클러스터 헤드 노드에서 실행되며 Azure 포털 대시보드를 통해 액세스할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **찾아보기**, **HDInsight 클러스터**, YARN 응용 프로그램 로그에 액세스할 Windows 기반 클러스터를 차례로 클릭합니다.
3. 위쪽 메뉴에서 **대시보드**를 클릭합니다. **HDInsight 쿼리 콘솔**이라는 새 브라우저 탭에 페이지가 열립니다.
4. **HDInsight 쿼리 콘솔**에서 **Yarn UI**를 클릭합니다.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
