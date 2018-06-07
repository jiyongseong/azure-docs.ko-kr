---
title: 클러스터 성능 모니터링 - Azure HDInsight | Microsoft Docs
description: HDInsight 클러스터의 용량 및 성능을 모니터링하는 방법을 설명합니다.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 9bf49631da58de86ffa1881bca976cab86677805
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-cluster-performance"></a>클러스터 성능 모니터링

성능과 리소스 사용률을 최대로 유지하려면 HDInsight 클러스터의 상태 및 성능을 모니터링해야 합니다. 또한 모니터링을 통해 코딩 또는 클러스터 구성 오류를 해결할 수 있습니다.

다음 섹션에서는 클러스터 로드, YARN 큐 효율성 및 저장소 접근성을 최적화하는 방법을 설명합니다.

## <a name="cluster-loading"></a>클러스터 로드

Hadoop 클러스터는 클러스터의 노드 간에 로드 균형을 유지해야 합니다. 이렇게 균형을 유지하면 프로세싱 작업이 RAM, CPU 또는 디스크 리소스의 제약을 받지 않습니다.

클러스터의 노드와 노드의 로드를 대략적인 수준에서 살펴보려면 [Ambari 웹 UI](hdinsight-hadoop-manage-ambari.md)에 로그인한 다음 **호스트** 탭을 선택합니다. 호스트는 정규화된 도메인 이름으로 나열됩니다. 각 호스트의 운영 상태는 색이 지정된 상태 표시기로 표시됩니다.

| 색 | 설명 |
| --- | --- |
| 빨강 | 호스트에서 적어도 하나 이상의 마스터 구성 요소가 중단되었습니다. 마우스를 가져다 대면 영향을 받는 구성 요소 목록을 나열하는 도구 설명이 표시됩니다. |
| 주황색 | 호스트에서 적어도 하나 이상의 종속 구성 요소가 중단되었습니다. 마우스를 가져다 대면 영향을 받는 구성 요소 목록을 나열하는 도구 설명이 표시됩니다. |
| 노랑 | Ambari 서버가 3분 이상 호스트에서 하트비트를 수신하지 못했습니다. |
| 녹색 | 정상적인 실행 상태입니다. |

또한 각 호스트의 코어 수와 RAM 양, 디스크 사용량 및 로드 평균을 보여주는 열이 표시됩니다.

![호스트 탭](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

해당 호스트에서 실행되는 구성 요소와 메트릭을 자세히 살펴보려면 호스트 이름 중 하나를 선택합니다. 메트릭은 CPU 사용량, 로드, 디스크 사용량, 메모리 사용, 네트워크 사용량 및 프로세스 수에 대한 선택 가능한 타임라인으로 표시됩니다.

![호스트 세부 정보](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

경고를 설정하고 메트릭을 보는 방법에 대한 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="yarn-queue-configuration"></a>YARN 큐 구성

Hadoop은 분산 플랫폼에서 실행되는 다양한 서비스를 갖고 있습니다. YARN(Yet Another Resource Negotiator)은 이러한 서비스를 조정하고 클러스터 리소스를 할당하고 공통 데이터 집합에 대한 액세스를 관리합니다.

YARN은 JobTracker가 하는 두 가지 작업, 즉 리소스 관리 및 일정 예약/모니터링 작업을 두 개의 디먼, 즉 전역 ResourceManager와 응용 프로그램별 ApplicationMaster(AM)에 나눠줍니다.

ResourceManager는 *순수 스케줄러*로써 모든 경합 응용 프로그램 간에 가용 리소스를 중재하는 일만 합니다. ResourceManager는 항상 모든 리소스를 사용하도록 보장하여 SLA, 용량 보장 등의 다양한 상수에 맞게 최적화합니다. ApplicationMaster는 ResourceManager의 리소스를 협상하고, NodeManager와 함께 작동하여 컨테이너와 컨테이너의 리소스 사용을 실행하고 모니터링합니다.

여러 테넌트가 대규모 클러스터를 공유하는 경우 클러스터의 리소스를 두고 경합이 발생합니다. CapacityScheduler는 요청을 큐에 추가하여 리소스 공유를 도와주는 플러그형 스케줄러입니다. CapacityScheduler 역시 다른 응용 프로그램의 큐가 무료 리소스를 사용하도록 허용하기 전에 리소스 조직의 하위 큐 간에 리소스를 공유하도록 *계층적 큐*를 지원합니다.

YARN은 이러한 큐에 리소스를 할당할 수 있도록 허용하며 모든 가용 리소스의 할당 여부를 보여줍니다. 큐에 대한 정보를 보려면 Ambari 웹 UI에 로그인한 다음 상단의 메뉴에서 **YARN 큐 관리자**를 선택합니다.

![YARN 큐 관리자](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

YARN 큐 관리자 페이지의 왼쪽에는 큐 목록과 각 큐에 할당된 용량 백분율이 표시됩니다.

![YARN 큐 관리자 세부 정보 페이지](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

큐에 대한 세부 정보를 보려면 Ambari 대시보드의 왼쪽에 있는 목록에서 **YARN** 서비스를 선택합니다. 그런 다음 **빠른 연결** 드롭다운 메뉴에서, 활성 노드 아래에서 **ResourceManager UI**를 선택합니다.

![ResourceManager UI 메뉴 연결](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

ResourceManager UI의 왼쪽 메뉴에서 **스케줄러**를 선택합니다. *응용 프로그램 큐* 아래에 큐 목록이 보입니다. 여기서 각 큐에 사용되는 용량, 작업이 큐 간에 얼마나 잘 분산되었는지 여부, 리소스가 제한된 작업이 있는지 여부를 볼 수 있습니다.

![ResourceManager UI 메뉴 연결](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>저장소 제한

저장소 수준에서 클러스터의 성능 병목 상태가 발생할 수 있습니다. 이러한 병목 상태의 주요 원인은 IO(입력/출력) 작업 *차단*이며, 작업 차단은 실행 중인 작업에서 저장소 서비스의 처리량을 초과하는 IO를 보낼 때 발생합니다. 작업이 차단되면 현재 IO를 처리한 후 처리될 때까지 대기하는 IO 요청 큐가 생성됩니다. 이 차단은 *저장소 제한* 때문에 발생하는데, 물리적 제한이 아닌 SLA(서비스 수준 계약)에 따른 저장소 서비스의 제한에 의해 발생합니다. 이 제한은 단일 클라이언트 또는 테넌트가 서비스를 독점하지 못하게 방지하는 역할을 합니다. SLA는 Azure Storage의 IOPS(초당 IO 수)를 제한하며, 자세한 내용은 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/storage-scalability-targets)를 참조하세요.

Azure Storage를 사용하는 경우 제한을 포함하여 저장소 관련 문제 모니터링에 대한 자세한 내용은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)을 참조하세요.

클러스터의 백업 저장소가 ADLS(Azure Data Lake Store)인 경우 제한이 발생하는 주요 원인은 대역폭 제한입니다. 이 경우 작업 로그에서 제한 오류를 확인하여 제한을 파악할 수 있습니다. ADLS에 대한 내용은 다음 문서에서 해당 서비스에 대한 제한 섹션을 참조하세요.

* [HDInsight의 Hive 및 Azure Data Lake Store에 대한 성능 조정 지침](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [HDInsight의 MapReduce 및 Azure Data Lake Store에 대한 성능 조정 지침](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [HDInsight의 Storm 및 Azure Data Lake Store에 대한 성능 조정 지침](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>다음 단계

다음 연결을 방문하여 클러스터 문제 해결 및 모니터링에 대해 자세히 알아봅니다.

* [HDInsight 로그 분석](hdinsight-debug-jobs.md)
* [YARN 로그를 사용하여 앱 디버그](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Linux 기반 HDInsight에서 Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
