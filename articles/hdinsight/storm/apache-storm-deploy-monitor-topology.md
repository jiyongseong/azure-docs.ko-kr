---
title: HDInsight | Microsoft Docs에서 Apache Storm 토폴로지 배포 및 관리
description: HDInsight에서 Storm 대시보드를 사용하여 Apache Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 배웁니다. Visual Studio용 Hadoop 도구를 사용합니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 55c92e6408522b8a96a37dbedd99d929af1e49fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Windows 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리

Storm 대시보드를 사용하면 사용자의 웹 브라우저를 사용하는 HDInsight 클러스터에 Apache Storm 토폴로지를 쉽게 배포 및 실행할 수 있습니다. 실행 중인 토폴로지를 모니터링 및 관리하기 위해 대시보드를 사용할 수도 있습니다. Visual Studio를 사용하는 경우 Visual Studio용 HDInsight 도구는 Visual Studio에서 유사한 기능을 제공합니다.

Storm 대시보드와 HDInsight 도구의 Storm 기능은 사용자 고유의 모니터링 및 관리 솔루션을 만들기 위해 사용할 수 있는 Storm REST API를 필요로 합니다.

> [!IMPORTANT]
> 이 문서의 단계에는 운영 체제로 Windows를 사용하는 HDInsight 클러스터에 Storm이 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.
>
> Linux를 사용하는 HDInsight 클러스터에서 Storm 토폴로지 배포 및 관리에 대한 자세한 내용은 [Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](apache-storm-deploy-monitor-topology-linux.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **HDInsight의 Apache Storm** - 클러스터를 만드는 단계는 [HDInsight에서 Apache Storm 시작](apache-storm-tutorial-get-started-linux.md)을 참조하세요.

* **Storm 대시보드** - HTML5를 지원하는 최신 웹 브라우저

* **Visual Studio** - Azure SDK 2.5.1 이상 및 Visual Studio용 HDInsight 도구 Visual Studio용 HDInsight 도구를 설치하고 구성하려면 [Visual Studio용 HDInsight 도구 사용 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

    다음과 같은 Visual Studio 버전 중 하나:

  * Visual Studio 2012 업데이트 4

  * Visual Studio 2013 업데이트 4 또는 Visual Studio 2013 Community

  * Visual Studio 2015(모든 버전)

  * Visual Studio 2017(모든 버전)

## <a name="storm-dashboard"></a>Storm 대시보드

Storm 대시보드는 Storm 클러스터에서 사용할 수 있는 웹 페이지입니다. URL은 **https://&lt;clustername>.azurehdinsight.net/** 이며, **clustername**은 HDInsight 클러스터에서 Storm의 이름입니다.

Storm 대시보드의 위쪽에서 **토폴로지 제출**을 선택합니다. 샘플 토폴로지를 실행하거나 사용자가 만든 토폴로지를 업로드 및 실행하려면 페이지의 지침을 따릅니다.

![토폴로지 제출 페이지][storm-dashboard-submit]

### <a name="storm-ui"></a>Storm UI

Storm 대시보드에서 **Storm UI** 링크를 선택합니다. 실행 중인 토폴로지 외에도 클러스터에 대한 정보가 표시됩니다.

![Storm UI][storm-dashboard-ui]

> [!NOTE]
> 일부 버전의 Internet Explorer에서는 처음으로 방문한 후 rm UI가 새로 고쳐지지 않습니다. 예를 들어 제출한 새 토폴로지가 표시되지 않거나 이전에 비활성화한 토폴로지가 활성 상태로 표시될 수 있습니다. Microsoft는 이 문제를 알고 있으며 해결 방법을 찾기 위해 노력하고 있습니다.

#### <a name="main-page"></a>기본 페이지

Storm UI의 기본 페이지에서는 다음 정보를 제공합니다.

* **클러스터 요약**: Storm 클러스터에 대한 기본 정보입니다.

* **토폴로지 요약**: 실행 중인 토폴로지 목록입니다. 특정 토폴로지에 대한 자세한 정보를 보려면 이 섹션의 링크를 사용하세요.

* **감독자 요약**: Storm 감독자에 대한 정보입니다.

* **Nimbus 구성**: 클러스터에 대한 Nimbus 구성입니다.

#### <a name="topology-summary"></a>토폴로지 요약

**토폴로지 요약** 섹션의 링크를 선택하면 토폴로지에 대한 다음 정보가 표시됩니다.

* **토폴로지 요약**: 토폴로지에 대한 기본 정보입니다.

* **토폴로지 동작**: 토폴로지에 대해 수행할 수 있는 관리 작업입니다.

  * **활성화**- 비활성화된 토폴로지 처리를 다시 시작합니다.

  * **비활성화**- 실행 중인 토폴로지를 일시 중지합니다.

  * **균형 다시 맞추기**- 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가하거나 감소한 노드 수를 보충할 수 있습니다.

      자세한 내용은 [Storm 토폴로지의 병렬 처리 이해(http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

  * **중단**- 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

* **토폴로지 통계**: 토폴로지에 대한 통계입니다. **창** 열에 있는 링크를 사용하여 페이지에서 나머지 항목에 대한 시간 프레임을 설정할 수 있습니다.

* **Spout**: 토폴로지에서 사용하는 Spout입니다. 이 섹션의 링크를 사용하면 특정 Spout에 대한 자세한 내용을 볼 수 있습니다.

* **Bolt**: 토폴로지에서 사용하는 Bolt입니다. 이 섹션의 링크를 사용하면 특정 Bolt에 대한 자세한 내용을 볼 수 있습니다.

* **토폴로지 구성**: 선택한 토폴로지의 구성입니다.

#### <a name="spout-and-bolt-summary"></a>Spout 및 Bolt 요약

**Spouts** 또는 **Bolts** 섹션에서 Spout를 선택하면 선택한 항목에 대해 다음 정보가 표시됩니다.

* **구성 요소 요약**: Spout 또는 Bolt에 대한 기본 정보입니다.

* **Spout/Bolt 통계**: Spout 또는 Bolt에 대한 통계입니다. **창** 열에 있는 링크를 사용하여 페이지에서 나머지 항목에 대한 시간 프레임을 설정할 수 있습니다.

* **입력 통계** (Bolt에만 해당): Bolt에서 사용하는 입력 스트림에 대한 정보입니다.

* **출력 통계**: 이 Spout 또는 Bolt가 내보낸 스트림에 대한 정보입니다.

* **실행자**: Spout 또는 Bolt의 인스턴스에 대한 정보입니다. 특정 실행자에 대한 **Port** 항목을 선택하면 이 인스턴스에 대해 처리된 진단 정보의 로그를 볼 수 있습니다.

* **오류**: 이 Spout 또는 Bolt에 대한 오류 정보입니다.

## <a name="hdinsight-tools-for-visual-studio"></a>Visual Studio용 HDInsight 도구

HDInsight 도구는 Storm 클러스터에 C# 또는 하이브리드 토폴로지를 제출하는 데 사용할 수 있습니다. 다음 단계는 샘플 응용 프로그램을 사용합니다. HDInsight 도구를 사용하여 자신만의 토폴로지 만들기에 대한 자세한 내용은 [Visual Studio용 HDInsight 도구를 사용하여 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

다음 단계를 통해 HDInsight 클러스터에서 Storm에 샘플을 배포한 다음 토폴로지를 보고 관리할 수 있습니다.

1. 최신 버전의 Visual Studio용 HDInsight 도구를 아직 설치하지 않은 경우 [Visual Studio용 HDInsight 도구 사용 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

2. Visual Studio를 열고 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

3. **새 프로젝트** 대화 상자에서 **설치됨** > **템플릿**을 확장하고 **HDInsight**를 선택합니다. 템플릿 목록에서 **Storm 샘플**을 선택합니다. 대화 상자 아래쪽에서 응용 프로그램 이름을 입력합니다.

    ![이미지](./media/apache-storm-deploy-monitor-topology/sample.png)

4. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.

   > [!NOTE]
   > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

5. **Storm 클러스터** 드롭다운 목록에서 HDInsight의 Storm 클러스터를 선택한 다음 **제출**을 선택합니다. **출력** 창을 사용하여 제출 성공 여부를 모니터링할 수 있습니다.

6. 토폴로지 제출에 성공하면 클러스터에 대한 **Storm 토폴로지** 가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 토폴로지를 선택합니다.

    ![VISUAL STUDIO 모니터](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > **Azure** > **HDInsight**를 확장한 다음 HDInsight의 Storm 클러스터를 마우스 오른쪽 단추로 클릭하고 **Storm 토폴로지 보기**를 선택하여 **서버 탐색기**에서 **Storm 토폴로지**를 볼 수도 있습니다.

    이러한 구성 요소에 대한 정보를 보려면 Spout 또는 Bolt에 대한 셰이프를 선택합니다. 선택한 각 항목에 대해 새 창이 열립니다.

   > [!NOTE]
   > 토폴로지 이름은 추가된 타임 스탬프가 있는 토폴로지의 클래스 이름입니다(이 경우 `HelloWord`).

7. 토폴로지를 중단하려면 **토폴로지 요약** 보기에서 **중단**을 선택합니다.

   > [!NOTE]
   > Storm 토폴로지는 중지되거나 클러스터가 삭제될 때까지 계속 실행됩니다.


## <a name="rest-api"></a>REST API

Storm UI는 REST API의 맨 위에 기본 제공되므로 REST API를 사용하여 유사한 관리 및 모니터링 기능을 수행할 수 있습니다. REST API를 사용하여 Storm 토폴로지를 관리 및 모니터링하는 사용자 지정 도구를 만들 수 있습니다.

자세한 내용은 [Storm UI REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md)를 참조하세요. 다음 정보는 HDInsight에서 Apache Storm과 REST API 사용하기에 관한 것입니다.

### <a name="base-uri"></a>기본 URI

HDInsight 클러스터에서 REST API의 기본 URI는 **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/** 이며**clustername**은 HDInsight 클러스터에서 Storm의 이름입니다.

### <a name="authentication"></a>인증

REST API 요청에서는 **기본 인증**을 사용해야 하므로 HDInsight 클러스터 관리자 이름 및 암호를 사용합니다.

> [!NOTE]
> 기본 인증은 일반 텍스트로 전송되기 때문에 클러스터와의 안전한 통신을 위해서는 **항상** HTTPS를 사용해야 합니다.

### <a name="return-values"></a>반환 값

REST API에서 반환되는 정보는 클러스터와 동일한 Azure Virtual Network에 있는 클러스터 또는 가상 머신 내에서만 사용할 수 있습니다. 예를 들어, Zookeeper 서버에 대해 반환된 FQDN(정규화된 도메인 이름)은 인터넷에서 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Storm 대시보드를 사용하여 토폴로지를 배포 및 모니터링하는 방법을 배웠으므로 이제 다음 방법을 알아봅니다.

* [Visual Studio용 HDInsight 도구를 사용하여 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)

* [Maven을 사용하여 Java 기반 토폴로지 개발](apache-storm-develop-java-topology.md)

추가 예제 토폴로지 목록은 [HDInsight의 Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)를 참조하세요.

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
