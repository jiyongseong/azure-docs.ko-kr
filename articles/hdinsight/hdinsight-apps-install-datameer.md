---
title: 게시된 응용 프로그램 설치 - Datameer - Azure HDInsight | Microsoft Docs
description: Datameer 타사 Hadoop 응용 프로그램을 설치하고 사용합니다.
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
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---datameer"></a>게시된 응용 프로그램 설치 - Datameer

이 문서에서는 Azure HDInsight에 게시된[Datameer](https://www.datameer.com/) Hadoop 응용 프로그램을 설치하고 실행하는 방법에 대해 설명합니다. HDInsight 응용 프로그램 플랫폼 개요 및 사용 가능한 ISV(Independent Software Vendor) 게시된 응용 프로그램 목록은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요. 사용자 고유의 응용 프로그램을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

## <a name="about-datameer"></a>Datameer 정보

Datameer는 Hadoop 플랫폼을 위한 네이티브 응용 프로그램으로, 기존 Azure HDInsight 기능을 확장하고 구조화 및 비구조화 데이터의 빠른 통합, 준비 및 분석을 제공합니다. Datameer는 70개 이상의 원본 및 형식(구조화, 반구조화 및 비구조화)에 액세스할 수 있습니다. 데이터를 직접 업로드하거나 고유한 데이터 링크를 사용하여 필요에 따라 데이터를 가져올 수 있습니다. Datameer의 셀프 서비스 기능과 친숙한 스프레드시트 인터페이스는 빅 데이터 기술의 복잡성을 줄이고 통찰력에 대한 시간을 가속화합니다. 스프레드시트 인터페이스는 선언된 수식을 입력하고 이를 최적화된 Hadoop 작업으로 변환하는 간단한 메커니즘을 제공합니다. Datameer와 BI(비즈니스 인텔리전스) 및 Excel 기술을 사용하면 클라우드에서 Hadoop을 빠르게 사용할 수 있습니다. 자세한 내용은 [Datameer 설명서](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

새 HDInsight 클러스터 또는 기존 클러스터에 이 앱을 설치하려면 다음 구성이 필요합니다.

* 클러스터 계층: 표준
* 클러스터 유형: Hadoop
* 클러스터 버전: 3.4

## <a name="install-the-datameer-published-application"></a>게시된 Datameer 응용 프로그램 설치

이 버전 및 사용 가능한 다른 ISV 응용 프로그램을 설치하는 단계별 지침은 [타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요.

## <a name="launch-datameer"></a>Datameer 시작

1. 설치가 완료되면 **설정** 창으로 이동한 다음, **일반** 범주 아래에서 **응용 프로그램**을 클릭하여 Azure Portal의 클러스터에서 Datameer를 시작할 수 있습니다. [설치된 앱] 창에 설치된 응용 프로그램이 나열됩니다.

    ![설치된 Datameer 앱](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Datameer가 선택되면 웹 페이지 및 SSH 엔드포인트 경로에 대한 링크가 표시됩니다. WEBPAGE 링크를 선택합니다.

3. 첫 번째 시작에는 체험 14일 평가판 또는 기존 라이선스 활성화의 두 가지 라이선스 옵션이 있습니다.

    ![라이선스 옵션](./media/hdinsight-apps-install-datameer/license.png)

4. 선택한 라이선스 옵션이 완료되면 로그인 양식이 표시됩니다. 로그인 양식 이전에 표시된 기본 자격 증명을 입력합니다. 로그인한 후 계속하려면 소프트웨어 계약에 동의합니다.

    ![로그인](./media/hdinsight-apps-install-datameer/login.png)

다음 단계에서는 "Hello World" 데모를 보여 줍니다.

1. [샘플 CSV를 다운로드](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf)합니다.

2. Datameer 대시보드 위쪽에서 **+** 기호를 클릭하고, **파일 업로드**를 클릭합니다.

    ![파일 업로드](./media/hdinsight-apps-install-datameer/upload.png)

3. 업로드 대화 상자에서 다운로드한 **Hello World.csv** 파일을 찾아 선택합니다. **파일 형식**이 CSV/TSV로 설정되어 있는지 확인합니다. **다음**을 선택합니다. 마법사가 끝날 때까지 **다음**을 계속 클릭합니다.

    ![파일 업로드](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. [새 폴더] 아래에 **Hello World** 파일의 이름을 지정합니다. 새 폴더의 이름을 "데모"로 변경합니다. **저장**을 선택합니다.

    ![저장](./media/hdinsight-apps-install-datameer/save.png)

5. **+** 기호를 한 번 더 클릭하고, **통합 문서**를 선택하여 데이터에 대한 새 통합 문서를 만듭니다.

    ![통합 문서 추가](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. **데이터** 폴더, **FileUploads**, "Hello World" 파일을 저장할 때 만든 **데모** 폴더를 차례로 펼칩니다. 파일 목록에서 **Hello World**를 선택한 다음, **데이터 추가**를 클릭합니다.

    ![저장](./media/hdinsight-apps-install-datameer/select-file.png)

7. 스프레드시트 인터페이스에 로드된 데이터가 표시됩니다. 데이터의 하위 집합을 선택하려면 도구 모음에서 **필터** 단추를 선택합니다.

    ![필터 단추](./media/hdinsight-apps-install-datameer/filter-button.png)

8. [필터 적용] 대화 상자에서 **도시** 열 및 **같음** 연산자를 선택하고, 필터 텍스트 상자에 **Chicago**를 입력합니다. **Create filter in new sheet**(새 시트에 필터 만들기) 확인란을 선택한 다음, **필터 만들기**를 선택합니다.

    ![필터 적용](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. **파일**, **저장**을 차례로 클릭하여 통합 문서를 저장합니다. "Hello World 통합 문서"와 같은 이름을 제공합니다.

10. 통합 문서를 실행하는 방법과 시기에 대한 옵션이 제공됩니다. 지금은 모든 옵션의 기본값으로 그대로 두고, **Start calculation process immediately after save**(저장하는 즉시 계산 프로세스 시작)를 확인하고, **저장**을 선택합니다.

    ![통합 문서 저장](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer는 강력한 시각화 도구를 제공합니다. 데이터를 표시하려면 인포그래픽을 만듭니다. 대시보드 위쪽에서 **+** 기호를 선택한 다음, **인포그래픽**을 선택합니다.

    ![인포그래픽 추가](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. 다음 다이어그램의 1단계와 같이 왼쪽의 위젯 목록에서 막대형 차트 위젯을 끌어옵니다. 다음으로, 오른쪽의 데이터 브라우저 아래에 있는 [데이터] 폴더를 탐색하고, 통합 문서를 펼친 다음, 필터가 추가된 워크시트(2단계)를 펼칩니다. 막대형 차트의 위쪽에서 **이름** 열을 끌어 **레이블** 대상에 놓고, 통합 문서의 [이름] 열을 차트의 레이블 필드로 설정합니다(3단계).

    ![인포그래픽](./media/hdinsight-apps-install-datameer/infographic.png)

13. [사용 기간]을 차트의 Y축으로 설정하려면 **사용 기간** 열을 차트의 **데이터** 필드로 끕니다.

    ![인포그래픽](./media/hdinsight-apps-install-datameer/infographic-age.png)

축하합니다! 코드를 작성하지 않고도 데이터 시각화를 만들었습니다. 이제 변형 및 추가 시각화를 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Datameer 설명서](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight에 게시되지 않은 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
* [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 동작을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
* [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md): 빈 에지 노드를 사용하여 HDInsight 클러스터에 액세스하고 HDInsight 응용 프로그램을 테스트 및 호스팅하는 방법을 알아봅니다.
