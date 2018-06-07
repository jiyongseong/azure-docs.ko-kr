---
title: Linux 기반 HDInsight 클러스터의 OS 패치 일정 구성 - Azure | Microsoft Docs
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: 42771b9ff0f177b6b31f626d1dd2d07046a53965
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="os-patching-for-hdinsight"></a>HDInsight의 OS 패치 
관리되는 Hadoop 서비스인 HDInsight는 HDInsight 클러스터에서 사용하는 기본 VM의 OS를 패치하는 작업을 담당합니다. 2016년 8월 1일을 기준으로 Linux 기반 HDInsight 클러스터에 대한 게스트 OS 패치 정책을 변경했습니다(버전 3.4 이상). 새 정책의 목표는 패치로 인해 부팅 횟수를 크게 줄이는 것입니다. 새 정책은 월요일 또는 목요일 오전 12시(UTC)마다 시차를 두고 모든 지정된 클러스터의 노드에 있는 Linux 클러스터에서 계속 VM(가상 머신)을 패치합니다. 그러나 지정된 VM은 게스트 OS 패치로 인해 최대 30일마다 다시 부팅됩니다. 또한 새로 만든 클러스터는 생성된 날짜로부터 30일보다 이전에 첫 번째로 다시 부팅되지 않습니다. VM이 다시 부팅되면 패치가 적용됩니다.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법
중요한 보안 패치를 설치할 수 있도록 간혹 HDInsight 클러스터에서 가상 머신을 다시 부팅해야 합니다. 새 Linux 기반 HDInsight 클러스터(버전 3.4 이상)는 2016년 8월 1일을 기준으로 다음 일정을 사용하여 다시 부팅됩니다.

1. 패치를 위해 30일 기간 내에서 최대 한 번 클러스터에 있는 가상 머신을 다시 부팅할 수 있습니다.
2. 다시 부팅은 오전 12시(UTC)에 시작합니다.
3. 다시 부팅 프로세스 중 클러스터를 계속 사용할 수 있도록 다시 부팅 프로세스는 가상 머신 클러스터에 걸쳐 지연됩니다.
4. 새로 만든 클러스터의 첫 번째 다시 부팅은 클러스터 생성 날짜 이후 30일이 지나기 전에 일어나지 않습니다.

이 문서에서 설명한 스크립트 작업을 사용하여 다음과 같이 OS 패치 일정을 수정할 수 있습니다.
1. 자동 다시 부팅 사용 또는 사용 안 함
2. 다시 부팅 빈도 설정(다시 부팅 간의 날짜)
3. 다시 부팅을 수행할 요일 설정

> [!NOTE]
> 2016년 8월 1일 이후에 만들어진 Linux 기반 HDInsight 클러스터에서만 이 스크립트 작업이 작동합니다. VM이 다시 부팅되는 경우에만 패치가 적용됩니다. 
>

## <a name="how-to-use-the-script"></a>스크립트를 사용하는 방법 

이 스크립트를 사용하는 경우 다음 정보가 필요합니다.
1. 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh  HDInsight는 이 URI를 사용하여 클러스터의 모든 가상 머신에서 스크립트를 찾아 실행합니다.
  
2. 스크립트를 적용하는 클러스터 노드 유형: 헤드 노드, workernode, zookeeper 이 스크립트는 클러스터의 모든 노드 유형에 적용되어야 합니다. 노드 유형에 적용되지 않으면 해당 노드 유형인 가상 머신은 계속 이전 패치 일정을 사용합니다.


3.  매개 변수: 이 스크립트는 세 가지 숫자 매개 변수를 수락합니다.

    | 매개 변수를 포함해야 합니다. | 정의 |
    | --- | --- |
    | 자동 다시 부팅 사용/사용 안 함 |0 또는 1입니다. 0 값은 자동 다시 부팅을 사용하지 않는 반면 1은 자동 다시 부팅을 해제합니다. |
    | Frequency(빈도) |7~90입니다(포괄). 다시 부팅해야 하는 패치를 위해 가상 머신을 다시 부팅하기 전에 대기한 날짜입니다. |
    | 요일 |1~7입니다(포괄). 1 값은 월요일에 재부팅해야 함을 나타내고 7은 일요일을 나타냅니다. 예를 들어 매개 변수 1 60 2를 사용하면 최대 60일마다 화요일에 자동 다시 부팅이 발생하게 됩니다. |
    | 지속성 |기존 클러스터에 스크립트 작업을 적용할 경우 스크립트를 지속형으로 표시할 수 있습니다. 지속형 스크립트는 크기 조정 작업을 통해 클러스터에 새 작업자 노드를 추가할 경우에 적용됩니다. |

> [!NOTE]
> 기존 클러스터에 적용하는 경우 이 스크립트를 지속형으로 표시해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다.
클러스터를 만드는 프로세스의 일부로 스크립트를 적용하는 경우 자동으로 유지됩니다.
>

## <a name="next-steps"></a>다음 단계

스크립트 작업을 사용하는 방법에 대한 특정 단계는 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)에서 다음 섹션을 참조하세요.

* [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
