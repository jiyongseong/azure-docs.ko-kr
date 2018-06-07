---
title: Windows 기반 HDInsight에서 Tez UI 사용 - Azure | Microsoft Docs
description: Windows 기반 HDInsight 클러스터에서 Tez UI를 사용하여 Tez 작업 디버깅하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 4201fb76ef9b0e711fd48972db86c356d72e6671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Windows 기반 HDInsight 클러스터에서 Tez UI를 사용하여 Tez 작업 디버깅
Tez UI는 실행 엔진으로 Tez를 사용하는 Hive 작업을 디버그하는 데 사용할 수 있습니다. Tez UI를 사용하면 연결된 항목의 그래프로 작업을 시각화하고 각 항목을 자세히 알아보며 통계 및 로깅 정보를 검색할 수 있습니다.

> [!IMPORTANT]
> 이 문서의 단계에는 Windows를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Windows 기반 HDInsight 클러스터입니다. 새 클러스터를 만드는 단계는 [Windows 기반 HDInsight 사용 시작](hdinsight-hadoop-tutorial-get-started-windows.md)을 참조하세요.

  > [!IMPORTANT]
  > Tez UI는 2016년 2월 8일 이후에 작성된 Windows 기반 HDInsight 클러스터에서만 사용할 수 있습니다.
  >
  >
* Windows 기반 원격 데스크톱 클라이언트입니다.

## <a name="understanding-tez"></a>Tez 이해
Tez는 Hadoop의 데이터 처리에 대해 확장 가능한 프레임워크로, 기존 MapReduce 처리보다 빠른 속도를 제공합니다. 다음 텍스트를 Hive 쿼리의 일부로 포함하여 Tez를 사용하도록 설정할 수 있습니다.

    set hive.execution.engine=tez;

Tez는 작업에 필요한 동작의 실행 순서를 설명하는 DAG(방향성 비순환 그래프)를 만듭니다. 개별 동작은 꼭짓점을 호출하고 전체 작업의 일부를 실행합니다. 꼭짓점에서 설명하는 작업의 실제 실행을 태스크라고 하며 클러스터의 여러 노드에 분산될 수 있습니다.

### <a name="understanding-the-tez-ui"></a>Tez UI 이해
Tez UI는 Tez를 사용하는 프로세스에 대한 정보를 제공하는 웹 페이지입니다. 다음과 같은 시나리오에서 유용한 정보를 제공할 수 있습니다.

* 장기 실행 처리를 모니터링하고 맵의 진행 상황을 보며 태스크를 줄입니다.
* 처리를 향상시킬 방법 또는 실패한 이유를 알아보기 위해 성공 또는 실패한 처리에 대한 기록 데이터를 분석합니다.

## <a name="generate-a-dag"></a>DAG 생성
Tez UI는 Tez 엔진을 사용하는 작업이 현재 실행되거나 이전에 실행된 경우 데이터를 포함합니다. 간단한 Hive 쿼리는 일반적으로 Tez를 사용하지 않고도 확인할 수 있습니다. 필터링, 그룹화, 정렬, 조인 등을 수행하는 복잡한 쿼리에는 Tez가 필요합니다.

Tez를 사용하는 Hive 쿼리를 실행하려면 다음 단계를 사용합니다.

1. 웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. 여기서 **CLUSTERNAME**은 HDInsight 클러스터의 이름입니다.
2. 페이지 위쪽의 메뉴에서 **Hive 편집기**를 선택합니다. 다음 예제 쿼리를 포함하는 페이지가 표시됩니다.

        Select * from hivesampletable

    예제 쿼리를 지우고 다음으로 바꿉니다.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. **제출** 단추를 선택합니다. 페이지 아래쪽의 **작업 세션** 섹션에는 쿼리 상태가 표시됩니다. 상태가 **완료됨**으로 변경되면 **세부 정보 보기** 링크를 선택하여 결과를 봅니다. **작업 출력**은 다음과 비슷해야 합니다.

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Tez UI 사용
> [!NOTE]
> Tez UI는 클러스터 헤드 노드의 바탕 화면에서만 사용할 수 있으므로 원격 데스크톱을 사용하여 헤드 노드에 연결해야 합니다.
>
>

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터를 선택합니다. HDInsight 블레이드 위쪽에서 **원격 데스크톱** 아이콘을 선택합니다. 이 링크는 원격 데스크톱 블레이드를 표시합니다.

    ![원격 데스크톱 아이콘](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. [원격 데스크]톱 블레이드에서 **연결** 을 선택하여 클러스터 헤드 노드에 연결합니다. 대화 상자가 나타나면 클러스터 원격 데스크톱 사용자 이름 및 암호를 사용하여 연결을 인증합니다.

    ![원격 데스크톱 연결 아이콘](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > 원격 데스크톱 연결을 설정하지 않은 경우 사용자 이름, 암호 및 만료 날짜를 제공한 다음 **사용**을 선택하여 원격 데스크톱을 사용하도록 설정합니다. 활성화되면 이전 단계를 사용하여 연결합니다.
   >
   >
3. 연결되면 원격 데스크톱에서 Internet Explorer를 열고 브라우저의 오른쪽 위에 있는 기어 아이콘을 선택한 다음 **호환성 보기 설정**을 선택합니다.
4. **호환성 보기 설정**의 아래쪽에서**호환성 보기에 인트라넷 사이트 표시** 및 **Microsoft 호환성 목록** 확인란의 선택을 취소한 다음 **닫기**를 선택합니다.
5. Internet Explorer에서 http://headnodehost:8188/tezui/#/으로 이동합니다. 그러면 Tez UI가 표시됩니다.

    ![Tez UI](./media/hdinsight-debug-tez-ui/tezui.png)

    Tez UI가 로드되면 클러스터에서 현재 실행 중이거나 실행된 DAG의 목록이 표시됩니다. 기본 보기는 DAG 이름, ID, 제출자, 상태, 시작 시간, 종료 시간, 기간, 응용 프로그램 ID 및 큐를 포함합니다. 페이지의 오른쪽에 있는 기어 아이콘을 사용하여 많은 열을 추가할 수 있습니다.

    항목이 하나만 있는 경우 이전 섹션에서 실행한 쿼리에 대한 것입니다. 여러 항목이 있는 경우 DAG 위에 있는 필드에 검색 조건을 입력하여 검색한 다음 **Enter** 키를 누릅니다.
6. 가장 최근의 DAG 항목에 대한 **DAG 이름**을 선택합니다. 이 링크는 DAG에 대한 정보 뿐만 아니라 DAG에 대한 정보가 포함된 JSON 파일의 zip을 다운로드할 수 있는 옵션을 표시합니다.

    ![DAG 세부 정보](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. 위 **DAG 세부 정보**에는 DAG 정보를 표시하는 데 사용될 수 있는 링크가 여러 개 있습니다.

   * **DAG 카운터**는 해당 DAG에 대한 카운터 정보를 표시합니다.
   * **그래픽 보기**는 해당 DAG의 그래픽 표시를 나타냅니다.
   * **모든 꼭짓점**은 해당 DAG의 꼭짓점에 대한 목록을 표시합니다.
   * **모든 태스크**는 해당 DAG의 꼭짓점에 대한 태스크 목록을 표시합니다.
   * **모든 태스크 시도**는 해당 DAG의 태스크를 실행하려는 시도에 대한 정보를 표시합니다.

     > [!NOTE]
     > 꼭짓점, 태스크 및 TaskAttempts에 대한 열 표시를 스크롤하는 경우 각 행에 대한 **카운터** 및 **로그 보기 또는 다운로드** 링크를 볼 수 있습니다.
     >
     >

     작업에 오류가 발생한 경우 DAG 세부 정보는 실패한 태스크에 대한 정보의 링크와 함께 실패 상태가 표시됩니다. 진단 정보는 DAG 세부 정보 아래에 표시됩니다.
8. **그래픽 보기**를 선택합니다. 이 DAG의 그래픽 표시를 나타냅니다. 보기에서 각 꼭짓점 위로 마우스를 두어 그에 대한 정보를 표시할 수 있습니다.

    ![그래픽 보기](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. 꼭짓점을 클릭하면 해당 항목에 대한 **꼭짓점 세부 정보** 를 로드합니다. **맵 1** 꼭짓점을 클릭하면 이 항목에 대한 세부 정보를 표시합니다. **확인**을 선택하여 탐색을 확인합니다.

    ![꼭짓점 세부 정보](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. 이제 꼭짓점 및 태스크에 관련된 페이지의 위쪽에 링크가 있습니다.

    > [!NOTE]
    > 또한 **DAG 세부 정보**로 다시 이동하고 **꼭짓점 세부 정보**를 선택한 다음 **맵 1** 꼭짓점을 선택하면 이 페이지로 이동할 수 있습니다.
    >
    >

    * **꼭짓점 카운터**는 이 꼭짓점에 대한 카운터 정보를 표시합니다.
    * **태스크**는 이 꼭짓점에 대한 태스크를 표시합니다.
    * **태스크 시도**는 이 꼭짓점의 태스크를 실행하려는 시도에 대한 정보를 표시합니다.
    * **원본 및 싱크**는 이 꼭짓점에 대한 데이터 원본 및 싱크를 표시합니다.

      > [!NOTE]
      > 이전 메뉴 작업으로 태스크, 태스크 시도, 원본 및 싱크__에 대한 열 표시를 스크롤하여 각 항목에 대한 자세한 정보의 링크를 표시할 수 있습니다.
      >
      >
11. **태스크**를 선택한 다음 **00_000000**이라는 항목을 선택합니다. 이 링크는 이 태스크에 대한 **태스크 세부 정보**를 표시합니다. 이 화면에서 **태스크 카운터** 및 **태스크 시도**를 볼 수 있습니다.

    ![태스크 세부 정보](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>다음 단계
이제 Tez 뷰를 사용하는 방법을 배웠으므로 [HDInsight에서 Hive 사용](hadoop/hdinsight-use-hive.md)에 대해 자세히 알아봅니다.

Tez에서 자세한 기술 정보는 [Hortonworks의 Tez 페이지](http://hortonworks.com/hadoop/tez/)를 참조하세요.
