---
title: 게시된 응용 프로그램 설치 - Datameer - Azure HDInsight | Microsoft Docs
description: Datameer 타사 Hadoop 응용 프로그램을 설치하여 사용합니다.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 9eef1760b7cee3bbdf33122514669b38b0b4d9db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>게시된 응용 프로그램 설치 - CDAP(Cask Data Application Platform)

이 문서에서는 게시된 Hadoop 응용 프로그램 [CDAP](http://cask.co/products/cdap/)를 Azure HDInsight에 설치하고 실행하는 방법을 설명합니다. HDInsight 응용 프로그램 플랫폼 개요 및 사용 가능한 ISV(Independent Software Vendor) 게시된 응용 프로그램 목록은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요. 사용자 고유의 응용 프로그램을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

## <a name="about-cdap"></a>CDAP 정보

Hadoop에서 응용 프로그램을 개발하기가 어려울 수 있습니다.  여러 Hadoop 기술은 통합에 다소 시간이 걸릴 수 있으며 그 수가 점점 많아지고 있습니다. 데이터 흐름을 모니터링하고 메트릭을 수집하려면 별도의 솔루션을 구축해야 할 수 있습니다.

### <a name="how-does-cdap-help"></a>CDAP는 어떻게 도움을 주나요?

CDAP(Cask Data Application Platform)는 빅 데이터용 통합 플랫폼입니다. CDAP를 사용하면 기본 인프라 대신 응용 프로그램 구축에 집중할 수 있습니다.

CDAP는 개발자에게 익숙한 상위 수준 개념 및 추상화를 사용합니다. 이러한 추상화는 내부 시스템의 복잡성을 감추고 솔루션의 재사용을 유도합니다.

[Cask Hydrator](http://cask.co/products/hydrator/)라고 하는 CDAP 확장은 데이터 파이프라인을 개발하고 관리할 수 있는 사용자 인터페이스를 제공합니다. 데이터 파이프라인은 데이터 취득, 변환, 분석, 실행 후 작업 등의 작업을 수행하는 다양한 *플러그 인으로 구성됩니다.

응용 프로그램의 나머지 부분을 건드릴 필요 없이 한 플러그 인을 다른 플러그인으로 바꾸기만 하면 여러 기술을 평가할 수 있도록, CDAP 플러그 인마다 인터페이스가 잘 정의되어 있습니다.

CDAP *파이프라인*은 응용 프로그램 내 데이터의 상위 수준 흐름을 그림으로 보여줍니다. 이 시각화는 데이터 수집부터 데이터 변환 및 분석을 지나 마지막으로 외부 데이터 저장소로 이어지는 종단 간 데이터 흐름을 보여줍니다.

다음 데이터 파이프라인 예제는 twitter 데이터를 실시간으로 수집한 후 미리 정의된 기준에 따라 일부 트윗을 필터링합니다. 데이터 파이프라인은 원시 트윗 데이터 및 프로젝트 데이터를 보다 읽기 쉬운 형식으로 변환한 후 값 집합에 따라 트윗을 그룹화하고 그 결과를 HBase에 씁니다.

![Data 파이프라인](./media/hdinsight-apps-install-cask/pipeline.png)

이 종단 간 파이프라인은 **Cask Hydrator UI**를 사용하여 빌드되며, 플러그 인 인터페이스 및 끌어서 놓기 기능을 사용하여 각 단계 간 연결을 형성합니다. 각 플러그 인의 기능을 독립적으로 격리하고 수정할 수 있습니다. CDAP를 사용하면 몇 시간 안에 비슷한 파이프라인을 빌드하고 유효성을 검사할 수 있습니다. 일반적인 Hadoop 세계에서는 이러한 솔루션을 구축하는 데 며칠이 걸릴 수 있습니다.

CDAP는 응용 프로그램을 통해 이동하는 데이터를 시각적으로 추적할 수 있는 [Cask 추적기](http://cask.co/products/tracker/)라는 확장 기능을 제공합니다. Cask 추적기는 자산이 응용 프로그램 전체에서 공식적으로 관리되도록 시스템에 *데이터 거버넌스*를 추가합니다. 각 데이터 요소의 계보를 추적하고, 관련 메트릭을 수집하고, 프로세스 전체의 데이터 내역을 감사할 수 있습니다.

다음 그림은 위의 파이프라인에서 흐르는 데이터 흐름을 보여줍니다.

![CDAP 추적기](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>필수 조건

새 HDInsight 클러스터 또는 기존 클러스터에 이 앱을 설치하려면 다음 구성이 필요합니다.

* 클러스터 계층: 표준
* 클러스터 유형: HBase
* 클러스터 버전: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>CDAP 게시된 응용 프로그램 설치

이 응용 프로그램과 기타 사용 가능한 ISV 응용 프로그램을 설치하는 단계별 지침은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요.

## <a name="launch-cdap"></a>CDAP 실행

1. 설치가 끝나면 Azure Portal에서 **설정** 창으로 이동한 다음, **일반** 범주에서 **응용 프로그램**을 선택하여 클러스터에서 CDAP를 실행합니다. [설치된 앱] 창에는 설치된 모든 응용 프로그램이 표시됩니다.

    ![설치된 CDAP 앱](./media/hdinsight-apps-install-cask/cdap-app.png)

2. CDAP를 선택하면 웹 페이지 및 HTTP 엔드포인트 링크가 표시되며, SSH 엔드포인트 경로도 표시됩니다. WEBPAGE 링크를 선택합니다.

3. 메시지가 표시되면 클러스터 관리자 자격 증명을 입력합니다.

    ![인증](./media/hdinsight-apps-install-cask/auth.png)

4. 로그인하면 Cask CDAP GUI 홈페이지가 나타납니다.

    ![Cask GUI 홈페이지](./media/hdinsight-apps-install-cask/gui.png)

5. CDAP 인터페이스를 탐색하려면 페이지 위쪽에서 **Cask Market** 메뉴 링크를 클릭합니다.

    ![Cask Market 링크](./media/hdinsight-apps-install-cask/cask-market.png)

6. 목록에서 **액세스 로그 샘플**을 선택합니다.

    ![액세스 로그 샘플](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. **로드**를 클릭하여 확인합니다.

    ![로드 클릭](./media/hdinsight-apps-install-cask/market-load.png)

8. 포함된 샘플 데이터의 보기가 표시됩니다. **다음**을 선택합니다.

    ![액세스 로그 샘플 - 데이터 보기](./media/hdinsight-apps-install-cask/market-view-data.png)

9. 대상 유형으로 **스트림**을 선택하고, 대상 이름을 입력한 다음, **마침**을 선택합니다.

    ![액세스 로그 샘플-대상 선택](./media/hdinsight-apps-install-cask/market-destination.png)

10. 데이터팩을 로드한 후에는 **스트림 보기 세부 정보**를 선택합니다.

    ![데이터팩 업로드 완료](./media/hdinsight-apps-install-cask/market-view-details.png)

11. 네임스페이스에 메타데이터를 사용하려면 [액세스 로그] 세부 정보 페이지의 [사용] 탭에서 **사용**을 선택합니다.

    ![액세스 로그 샘플 - 로드됨 - 메타데이터 사용](./media/hdinsight-apps-install-cask/log-loaded.png)

12. 메타데이터를 사용하도록 설정하면 감사 메시지 정보를 표시하는 그래프가 나타납니다.

    ![액세스 로그 샘플 - 메타데이터 사용](./media/hdinsight-apps-install-cask/log-metadata.png)

13. 로그 데이터를 탐색하려면 페이지 위쪽에서 **탐색** 아이콘을 선택합니다.

    ![액세스 로그 샘플 - 살펴보기](./media/hdinsight-apps-install-cask/log-explore.png)

14. 샘플 SQL 쿼리가 표시됩니다. 원하는 대로 자유롭게 수정한 다음, **실행**을 선택합니다.

    ![액세스 로그 샘플 - 쿼리로 데이터 집합 탐색](./media/hdinsight-apps-install-cask/log-query.png)

15. 쿼리가 완료되면 [작업] 열 아래에서 **보기** 아이콘을 선택합니다.

    ![액세스 로그 샘플 - 완료된 쿼리 보기](./media/hdinsight-apps-install-cask/log-query-view.png)

16. 쿼리 결과를 봅니다.

    ![액세스 로그 샘플 - 쿼리 결과](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>다음 단계

* [Cask 설명서](http://cask.co/resources/documentation/).
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight에 게시되지 않은 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
* [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 동작을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
* [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md): 빈 에지 노드를 사용하여 HDInsight 클러스터에 액세스하고 HDInsight 응용 프로그램을 테스트 및 호스팅하는 방법을 알아봅니다.
