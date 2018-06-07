---
title: 게시된 응용 프로그램 설치 - Dataiku DDS - Azure HDInsight | Microsoft Docs
description: Dataiku DDS 타사 Hadoop 응용 프로그램을 설치하여 사용합니다.
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
ms.openlocfilehash: facee9187528fa0c6a74f71ee73636dc23ca35ba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---dataiku-dds"></a>게시된 응용 프로그램 설치 - Dataiku DDS

이 문서에서는 게시된 Hadoop 응용 프로그램 [Dataiku DDS](https://www.dataiku.com/)를 Azure HDInsight에 설치하고 실행하는 방법을 설명합니다. HDInsight 응용 프로그램 플랫폼 개요 및 사용 가능한 ISV(Independent Software Vendor) 게시된 응용 프로그램 목록은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요. 사용자 고유의 응용 프로그램을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

## <a name="about-dataiku-dss"></a>Dataiku DSS 정보

Dataiku [DSS(Data Science Studio)](https://www.dataiku.com/dss/features/connectivity/)는 데이터 과학자가 분석 솔루션을 빌드하여 제공할 수 있게 도와주는 공동 작업 데이터 과학 플랫폼입니다. DSS를 HDInsight 응용 프로그램으로 제공하면 데이터 과학을 사용하여 빅 데이터 솔루션을 빌드하고 엔터프라이즈급 및 규모에서 실행할 수 있습니다.

DSS를 사용하여 데이터를 수집, 준비, 처리하는 완전한 분석 솔루션을 구현할 수 있습니다. 또한 DSS 솔루션은 기계 학습 모델, 시각화 및 조작에 대한 교육과 적용을 포함할 수 있습니다.

Hadoop 또는 Spark 클러스터를 사용하여 HDInsight에 DSS를 설치할 수 있습니다. 실행 중인 기존 클러스터에 또는 새 클러스터를 만들 때 DSS를 설치할 수 있습니다. 또한 DSS는 Azure Blob 저장소를 커넥터로 사용하여 데이터를 읽을 수 있습니다.

DSS를 사용하여 프로젝트를 빌드한 다음 MapReduce 또는 Spark 작업을 생성할 수 있습니다. 이러한 작업은 HDInsight에서 기본 MapReduce 또는 Spark 작업으로 실행되므로, 필요 시 클러스터 크기를 조정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

새 HDInsight 클러스터 또는 기존 클러스터에 이 앱을 설치하려면 다음 구성이 필요합니다.

* 클러스터 계층: 표준, 프리미엄
* 클러스터 유형: Hadoop, Spark
* 클러스터 버전: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Dataiku DDS 게시된 응용 프로그램 설치

이 응용 프로그램과 기타 사용 가능한 ISV 응용 프로그램을 설치하는 단계별 지침은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요.

## <a name="launch-dataiku-dss"></a>Dataiku DSS 실행

1. 설치가 끝나면 Azure Portal에서 **설정** 창으로 이동한 다음, **일반** 범주에서 **응용 프로그램**을 클릭하여 클러스터에서 DSS를 실행할 수 있습니다. [설치된 앱] 창에는 설치된 응용 프로그램이 표시됩니다.

    ![설치된 Dataiku DSS 앱](./media/hdinsight-apps-install-dataiku/app.png)

2. HDInsight에서 DSS를 선택하면 웹 페이지 링크와 SSH 엔드포인트 경로가 표시됩니다. WEBPAGE 링크를 선택합니다.

3. 처음으로 실행하면 새 무료 Dataiku 계정을 만들거나 기존 계정으로 로그인하는 양식이 제공됩니다. [Enterprise Edition](https://www.dataiku.com/dss/editions/) 2주 평가판을 시작하는 옵션도 제공됩니다. 현재는 계속해서 Enterprise Edition 라이선스 키를 입력하거나 Community Edition을 사용하는 옵션 중에 선택할 수 있습니다.

4. 선택한 라이선스 옵션을 완료하면 로그인 양식이 표시됩니다. 표시된 기본 자격 증명을 입력하고 로그인 양식을 작성합니다.

다음 단계에서는 간단한 데모를 제공합니다.

1. [샘플 주문 CSV를 다운로드합니다](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. DSS 대시보드의 왼쪽에 있는 메뉴에서 **+** (새 프로젝트) 링크를 선택하여 새 프로젝트를 만듭니다.

    ![새 프로젝트 링크](./media/hdinsight-apps-install-dataiku/new-project.png)

3. 새 프로젝트 양식에 **이름**을 입력합니다. **프로젝트 키**가 권장 값으로 자동으로 채워집니다. 여기서는 "주문"을 입력합니다. **만들기**를 클릭합니다.

    ![새 프로젝트 양식](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. 새 프로젝트 페이지에서 **+ 첫 번째 데이터 집합 가져오기**를 선택합니다.

    ![파일 업로드](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. **파일** 데이터 집합 목록 아래에서 **파일 업로드**를 선택합니다. 업로드 대화 상자가 표시됩니다. [파일 추가]를 클릭하고, 다운로드한 `haiku_shirt_sales.csv` 파일을 선택하고, 유효성을 검사합니다.

6. 이 파일은 DSS에 업로드됩니다. [미리 보기] 단추를 클릭하여 DSS가 CSV 형식을 올바르게 검색하는지 확인합니다.

    ![파일 업로드](./media/hdinsight-apps-install-dataiku/preview.png)

7. 가져오기는 거의 완벽합니다. CSV 파일은 탭 구분 기호를 사용합니다. 데이터는 테이블 형식이고, 기능이라는 열과 관찰을 나타내는 선이 있습니다. 한 가지 명확한 오류는 헤더와 데이터 사이의 빈 줄에 포함된 파일입니다. 이 오류를 해결하려면 **다음 줄 건너뛰기** 필드에 `1`을 입력합니다.

    ![저장](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. 새 데이터 집합의 이름을 입력합니다. 화면 위쪽의 필드에 **haiku_shirt_sales**를 입력한 다음, **만들기** 단추를 선택합니다.

9. 데이터의 테이블 형식 보기가 표시되며, 여기서 탐색을 시작할 수 있습니다. 각 열에 대해 Dataiku Science Studio가 _파란색_ 데이터 형식을 검색했습니다(이 예에서는 구문 분석되지 않은 텍스트, 숫자 또는 날짜). 계기는 값이 형식과 일치하지 않거나(빨간색) 값이 누락된(비어 있는) 열의 비율을 나타냅니다. 이 예제 데이터 집합에서는 부서에 빈 값과 잘못된 데이터가 전부 있습니다.

    ![테이블 형식 보기](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>데이터 조작

실제 데이터는 거의 항상 난잡하며 깔끔하게 패키지되어 있는 경우는 거의 없습니다. 일반적으로 데이터를 정리하려면 스크립트 및 관련 비즈니스 논리 체인이 필요합니다. Dataiku DSS는 이 작업을 보다 친숙하게 할 수 있게 도와주는 전용 **랩** 도구를 제공합니다.

1. 오른쪽 위 모서리에서 **랩**을 클릭합니다.

    ![랩 단추](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. 랩 창이 열립니다. 랩에서 데이터 집합을 반복적으로 작업하여 익숙해질 수 있습니다. 이 자습서에서는 시각적 분석 측면을 보여줍니다. 시각적 분석 아래에서 **새로 만들기** 단추를 선택합니다. 분석의 이름을 지정하라는 메시지가 표시됩니다. 지금은 기본 이름을 그대로 사용하기로 하고, **만들기**를 클릭합니다.

    ![랩 만들기](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. 페이지의 오른쪽 위 모서리에서 **빠른 열 통계** 단추를 선택합니다.

    ![빠른 열 통계](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. **열 빠른 보기** 창 아래의 타임라인 기준 그래프에 데이터 형식 및 값에 대한 통계가 표시됩니다.

    ![열 빠른 보기](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

이제 샘플 데이터를 사용하여 DSS를 탐색할 수 있습니다. 데이터를 정리 및 작업하고, 새로운 시각화를 만들 수 있습니다.

자세한 자습서는 [Dataiku DSS 알아보기](https://www.dataiku.com/learn/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Dataiku DSS 설명서](https://doc.dataiku.com/dss/latest/).
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight에 게시되지 않은 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
* [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 동작을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
* [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md): 빈 에지 노드를 사용하여 HDInsight 클러스터에 액세스하고 HDInsight 응용 프로그램을 테스트 및 호스팅하는 방법을 알아봅니다.
