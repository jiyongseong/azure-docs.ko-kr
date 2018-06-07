---
title: Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 | Microsoft Docs
description: Data Lake Tools for Visual Studio를 설치하고 사용하여 Azure HDInsight에서 Hadoop 클러스터에 연결하고 Hive 쿼리를 실행하는 방법을 알아봅니다.
keywords: Hadoop 도구, Hive 쿼리, Visual Studio, Visual Studio Hadoop Hive
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 1707a681315fbb53f21df95664188463968bb855
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202478"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Hive 쿼리 실행

Data Lake Tools for Visual Studio(Azure Data Lake and Stream Analytics Tools for Visual Studio라고도 함)를 사용하여 [Azure HDInsight](../hdinsight-hadoop-introduction.md)에서 Hadoop 클러스터에 연결하고 Hive 쿼리를 제출하는 방법에 대해 알아봅니다. 

HDInsight 사용에 대한 자세한 내용은 [HDInsight 소개](../hdinsight-hadoop-introduction.md) 및 [HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요. 

Storm 클러스터에 연결하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 HDInsight의 Apache Storm에 대한 C# 토폴로지 개발](../storm/apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

Data Lake Tools for Visual Studio를 사용하여 Azure Data Lake Analytics 및 HDInsight에 액세스할 수 있습니다. Data Lake Tools에 대한 자세한 내용은 [Data Lake Tools for Visual Studio를 사용하여 U-SQL 스크립트 개발](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하고 Data Lake Tools for Visual Studio를 사용하려면 다음 항목이 필요합니다.

* Azure HDInsight 클러스터를 만듭니다. HDInsight 클러스터를 만들려면 [Azure HDInsight에서 Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요. 대화형 Hive 쿼리를 실행하려면 [HDInsight 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터가 필요합니다.
* Visual Studio 2017, 2015 또는 2013이 설치된 컴퓨터
    
    > [!NOTE]
    > 현재 Data Lake Tools for Visual Studio 영어 버전만 사용할 수 있습니다.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Azure Data Lake Tools for Visual Studio 설치 또는 업데이트

### <a name="install-data-lake-tools"></a>Data Lake Tools 설치

Data Lake Tools은 Visual Studio 2017에 기본적으로 설치됩니다. 이전 버전의 Visual Studio에서는 [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 Data Lake Tools를 설치할 수 있습니다. Visual Studio 버전과 일치하는 Data Lake Tools 버전을 선택하세요. 

### <a name="install-visual-studio"></a>Visual Studio 설치

Visual Studio가 설치되어 있지 않으면 [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 최신 버전의 Visual Studio Community 및 Azure SDK을 설치합니다.

![Data Lake Tools for Visual Studio - 웹 플랫폼 설치 관리자의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "플랫폼 설치 관리자를 사용하여 Data Lake Tools for Visual Studio 설치")

### <a name="update-the-tools"></a>도구 업데이트

1. Visual Studio를 엽니다.
2. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다.
3. **업데이트**를 펼친 다음, **Azure Data Lake and Stream Analytics Tools**를 선택합니다(설치되어 있는 경우).

> [!NOTE]
>
> Data Lake Tools 버전 2.3.0.0 이상만 사용하여 대화형 쿼리 클러스터에 연결하고 대화형 Hive 쿼리를 실행할 수 있습니다.

## <a name="connect-to-azure-subscriptions"></a>Azure 구독에 연결
Data Lake Tools for Visual Studio를 사용하여 HDInsight 클러스터에 연결하고, 몇 가지 기본 관리 작업을 수행하고, Hive 쿼리를 실행할 수 있습니다.

> [!NOTE]
> 일반 Hadoop 클러스터에 연결하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 Hive 쿼리 작성 및 제출](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)을 참조하세요.
> 
> 

Azure 구독에 연결하려면,

1. Visual Studio를 엽니다.
2. **보기** 메뉴에서 **서버 탐색기**를 선택합니다.
3. [서버 탐색기]에서 **Azure**, **HDInsight**를 차례로 펼칩니다.
   
   > [!NOTE]
   > **HDInsight 작업 목록** 창이 열립니다. 창이 표시되지 않으면 **보기** 메뉴에서 **다른 창**을 선택한 다음, **HDInsight 작업 목록 창**을 선택합니다.  
   > 
   > 
4. Azure 구독 자격 증명을 입력한 다음, **로그인**을 선택합니다. 이 컴퓨터의 Visual Studio에서 Azure 구독에 연결한 적이 없는 경우에만 인증이 필요합니다.
5. 서버 탐색기에 기존 HDInsight 클러스터 목록이 표시됩니다. 클러스터가 없는 경우 Azure Portal, Azure PowerShell 또는 HDInsight SDK를 사용하여 클러스터를 만들 수 있습니다. 자세한 내용은 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
   
   ![Data Lake Tools for Visual Studio 서버 탐색기 - 클러스터 목록의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Data Lake Tools for Visual Studio 서버 탐색기")
6. HDInsight 클러스터를 확장합니다. **Hive 데이터베이스**, 기본 저장소 계정, 연결된 저장소 계정 및 **Hadoop 서비스 로그**가 표시됩니다. 엔터티를 더 확장할 수 있습니다.

Azure 구독에 연결되면 다음 작업을 수행할 수 있습니다.

Visual Studio에서 Azure Portal에 연결하려면,

1. [서버 탐색기]에서 **Azure** > **HDInsight**를 차례로 선택합니다.
2. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭한 다음, **Azure Portal에서 클러스터 관리**를 선택합니다.

Visual Studio에서 질문하고 사용자 의견을 제공하려면,

1. **도구** 메뉴에서 **HDInsight**를 선택합니다.
2. 질문을 하려면 **MSDN 포럼**을 선택합니다. 사용자 의견을 제공하려면 **피드백 제공**을 선택합니다.

## <a name="explore-linked-resources"></a>연결된 리소스 탐색
서버 탐색기에서 기본 저장소 계정 및 연결된 저장소 계정을 확인할 수 있습니다. 기본 저장소 계정을 확장한 경우 저장소 계정의 컨테이너를 확인할 수 있습니다. 기본 저장소 계정과 기본 컨테이너가 표시되어 있습니다. 컨테이너를 마우스 오른쪽 단추로 클릭하여 컨테이너 콘텐츠를 확인합니다.

![Data Lake Tools for Visual Studio 서버 탐색기 - 연결된 리소스 목록의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "연결된 리소스 목록")

컨테이너를 연 후에 다음 단추를 사용하여 Blob을 업로드, 삭제 및 다운로드할 수 있습니다.

![Data Lake Tools for Visual Studio 서버 탐색기 - Blob 작업의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "서버 탐색기에서 Blob 업로드, 삭제 및 다운로드")

## <a name="run-interactive-hive-queries"></a>대화형 Hive 쿼리 실행
[Apache Hive](http://hive.apache.org)는 Hadoop을 기반으로 하는 데이터 웨어하우스 인프라입니다. Hive는 데이터 요약, 쿼리 및 분석에 사용됩니다. Data Lake Tools for Visual Studio를 사용하여 Visual Studio에서 Hive 쿼리를 실행할 수 있습니다. Hive에 대한 자세한 내용은 [HDInsight에서 Hive 사용](hdinsight-use-hive.md)을 참조하세요.

[대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md)는 Apache Hive 2.1에서 [LLAP의 Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP)를 사용합니다. 대화형 쿼리는 저장된 대규모 데이터 집합에 대해 복잡한 데이터 웨어하우스 스타일의 쿼리에 대화형 기능을 제공합니다. 대화형 쿼리에서 Hive 쿼리를 실행하면 기존의 Hive 일괄 처리 작업에 비해 훨씬 빠릅니다. 자세한 내용은 [Hive 일괄 작업 실행](#run-hive-batch-jobs)을 참조하세요.

> [!NOTE]
>
> [HDInsight 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터에 연결하는 경우에만 대화형 Hive 쿼리를 실행할 수 있습니다.

또한 Data Lake Tools for Visual Studio를 사용하여 Hive 작업에 대해 자세히 확인할 수도 있습니다. Data Lake Tools for Visual Studio는 특정 Hive 작업에 대한 Yarn 로그를 수집하고 표시합니다.

### <a name="view-hivesampletable"></a>**hivesampletable** 보기
모든 HDInsight 클러스터에는 hivesampletable이라는 기본 샘플 Hive 테이블이 있습니다. Hive 테이블은 Hive 테이블을 나열하고, 테이블 스키마를 보고, Hive 테이블의 행을 나열하는 방법을 정의합니다.

Hive 테이블을 나열하고 Hive 테이블 스키마를 보려면,

1. 테이블 스키마를 보려면 **서버 탐색기**에서 **Azure** > **HDInsight**를 차례로 선택합니다. 클러스터를 선택한 다음, **Hive 데이터베이스** > **기본값** > **hivesampletable**을 차례로 선택합니다.
2. **hivesampletable**을 마우스 오른쪽 단추로 클릭한 후 **상위 100개 행 보기**를 클릭하여 행을 나열합니다. 이는 Hive ODBC 드라이버를 사용하여 다음 Hive 쿼리를 실행하는 것과 동일합니다.
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   행 개수를 사용자 지정할 수 있습니다.
   
   ![HDInsight Hive Visual Studio 스키마 쿼리의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive 쿼리 결과")

### <a name="create-hive-tables"></a>Hive 테이블 만들기
Hive 테이블을 만들려면 GUI를 사용하거나 Hive 쿼리를 사용할 수 있습니다. Hive 쿼리 사용에 대한 자세한 내용은 [Hive 쿼리 실행](#run.queries)을 참조하세요.

Hive 테이블을 만들려면,

1. **서버 탐색기**에서 **Azure** > **HDInsight 클러스터**를 차례로 선택합니다. HDInsight 클러스터를 선택한 다음, **Hive 데이터베이스**를 선택합니다.
2. **기본값**을 마우스 오른쪽 단추로 클릭한 다음, **테이블 만들기**를 선택합니다.
3. 테이블을 구성합니다.  
4. **테이블 만들기**를 선택하여 새 Hive 테이블을 만들기 위한 작업을 제출합니다.
   
    ![HDInsight Visual Studio Tools - 테이블 만들기 창의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Hive 테이블 만들기")

### <a name="run.queries"></a>Hive 쿼리 유효성 검사 및 실행
Hive 쿼리를 만들고 실행하기 위한 두 가지 옵션이 있습니다.

* 임시 쿼리 만들기
* Hive 응용 프로그램 만들기

임시 쿼리를 만들고 유효성을 검사하고 실행하려면,

1. **서버 탐색기**에서 **Azure** > **HDInsight 클러스터**를 차례로 선택합니다.
2. 쿼리를 실행하려는 클러스터를 마우스 오른쪽 단추로 클릭한 다음, **Hive 쿼리 작성**을 선택합니다.  
3. Hive 쿼리를 입력합니다. 

    Hive 편집기는 IntelliSense를 지원합니다. Data Lake Tools for Visual Studio는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어 **SELECT * FROM**을 입력하면 IntelliSense에서 제안된 테이블 이름을 모두 나열합니다. 테이블 이름이 지정되면 IntelliSense에서 열 이름을 나열합니다. 이 도구는 대부분의 Hive DML 문, 하위 쿼리 및 기본 제공 UDF를 지원합니다.
   
    ![HDInsight Visual Studio Tools- IntelliSense 예 1의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools - IntelliSense 예 2의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > IntelliSense는 HDInsight 도구 모음에서 선택한 클러스터의 메타데이터만 제안합니다.
   > 
   
4. (선택 사항) 스크립트 구문 오류를 확인하려면 **스크립트 유효성 검사**를 선택합니다.
   
    ![Data Lake Tools for Visual Studio - 로컬 유효성 검사의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "스크립트 유효성 검사")
5. **제출** 또는 **제출(고급)** 을 선택합니다. 고급 제출 옵션을 선택하는 경우 스크립트에 대한 **작업 이름**, **인수**, **추가 구성** 및 **상태 디렉터리**를 구성합니다.
   
    ![HDInsight Hadoop Hive 쿼리의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "쿼리 제출")
   
    작업이 제출되면 **Hive 작업 요약** 창이 표시됩니다.
   
    ![HDInsight Hadoop Hive 쿼리 요약의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Hive 작업 요약")
6. 작업 상태가 **완료**로 변경될 때까지 **새로 고침** 단추를 사용하여 상태를 새로 고칩니다.
7. **작업 쿼리**, **작업 출력**, **작업 로그** 또는 **Yarn 로그**를 보려면 아래쪽의 링크를 선택합니다.

Hive 솔루션을 만들고 실행하려면,

1. **파일** 메뉴에서 **새로 만들기**, **프로젝트**를 차례로 선택합니다.
2. 왼쪽 창에서 **HDInsight**를 선택합니다. 가운데 창에서 **Hive 응용 프로그램**을 선택합니다. 속성을 입력한 다음, **확인**을 선택합니다.
   
    ![HDInsight Visual Studio Tools - 새 Hive 프로젝트의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Visual Studio에서 Hive 응용 프로그램 만들기")
3. **솔루션 탐색기**에서 **Script.hql**을 두 번 클릭하여 이 스크립트를 엽니다.
4. Hive 스크립트의 유효성을 검사하려면 **스크립트 유효성 검사** 단추를 선택합니다. 또는 Hive 편집기에서 스크립트를 마우스 오른쪽 단추로 클릭한 다음, 바로 가기 메뉴에서 **스크립트 유효성 검사**를 선택할 수 있습니다.

### <a name="view-hive-jobs"></a>Hive 작업 보기
Hive 작업에 대한 작업 쿼리, 작업 출력, 작업 로그 및 Yarn 로그를 볼 수 있습니다. 자세한 내용은 앞의 스크린샷을 참조하세요.

최신 릴리스의 도구에서는 Yarn 로그를 수집하고 표시하여 Hive 작업에 대한 자세한 정보를 확인할 수 있습니다. Yarn 로그는 성능 문제를 조사하는 데 도움이 됩니다. HDInsight에서 Yarn 로그를 수집하는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 HDInsight 응용 프로그램 로그에 액세스](../hdinsight-hadoop-access-yarn-app-logs.md)를 참조하세요.

Hive 작업을 보려면,

1. **서버 탐색기**에서 **Azure**, **HDInsight**를 차례로 펼칩니다.
2. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭한 다음, **작업 보기**를 선택합니다. 클러스터에서 실행한 Hive 작업 목록이 표시됩니다.  
3. 작업을 선택합니다. **Hive 작업 요약** 창에서 다음 중 하나를 선택합니다.
    - **작업 쿼리**
    - **작업 출력**
    - **작업 로그**  
    - **Yarn 로그**
   
    ![HDInsight Visual Studio Tools - Hive 작업 보기 창의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Hive 작업 보기")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>HiveServer2를 통해 빠른 경로 Hive 실행
> [!NOTE]
> 이 기능은 HDInsight 버전 3.2 이상의 클러스터에서만 작동합니다.
 
Data Lake Tools for Visual Studio는 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)(Templeton이라고도 함)을 통해 Hive 작업을 제출하는 데 사용됩니다. Hive 작업을 제출하는 이 방법은 작업 세부 정보 및 오류 정보를 반환 하는 데 오랜 시간이 걸렸습니다.

이 성능 문제를 해결하기 위해 Data Lake Tools for Visual Studio는 RDP/SSH를 무시하고 HiveServer2를 통해 클러스터에서 직접 Hive 작업을 실행할 수 있습니다.

이 방법을 사용하면 더 나은 성능 외에도 Apache Tez 그래프 및 작업 세부 정보에서 Hive를 볼 수도 있습니다.

HDInsight 클러스터 버전 3.2 이상의 클러스터에서는 **HiveServer2를 통해 실행** 단추가 표시됩니다.

![Data Lake Tools for Visual Studio - HiveServer2를 통해 실행 옵션의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "HiveServer2를 사용하여 Hive 쿼리 실행")

또한 실시간으로 스트리밍 로그를 다시 볼 수도 있습니다. Tez에서 Hive 쿼리를 실행하면 작업 그래프도 볼 수 있습니다.

![Data Lakes Tools for Visual Studio - HiveServer2를 사용한 빠른 경로 Hive 실행의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "작업 그래프 보기")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>HiveServer2를 통한 쿼리 실행 및 WebHCat을 통한 쿼리 제출

HiveServer2를 통해 쿼리를 실행할 때 많은 성능상의 이점을 얻을 수 있지만 이 방법에는 몇 가지 제한 사항이 있습니다. 일부 제한 사항으로 인해 이 방법은 프로덕션 사용에 적합하지 않습니다. 

다음 표에서는 HiveServer2를 통해 쿼리를 실행하는 것과 WebHCat을 통해 쿼리를 제출하는 것과의 차이점을 보여 줍니다.

|  | HiveServer2를 통해 실행 | WebHCat을 통해 제출 |
| --- | --- | --- |
| 쿼리 실행 |WebHCat(TempletonControllerJob이라는 MapReduce 작업을 시작함)의 오버헤드를 제거합니다. |쿼리가 WebHCat을 통해 실행되면 WebHCat은 MapReduce 작업을 시작하여 대기 시간이 추가됩니다. |
| 로그 다시 스트리밍 |거의 실시간으로 수행됩니다. |작업 실행 로그는 작업이 완료될 때 사용할 수 있습니다. |
| 작업 기록 보기 |HiveServer2를 통해 쿼리를 실행하는 경우 해당 작업 기록(작업 로그, 작업 출력)이 유지되지 않습니다. 제한된 정보로 Yarn UI에서 응용 프로그램을 볼 수 있습니다. |WebHCat을 통해 쿼리를 실행하는 경우 작업 기록(작업 로그, 작업 출력)이 유지됩니다. Visual Studio, HDInsight SDK 또는 PowerShell을 사용하여 작업 기록을 볼 수 있습니다. |
| 창 닫기 |HiveServer2를 통한 실행은 *동기*입니다. 창이 닫히면 쿼리 실행이 취소됩니다. |WebHCat을 통한 제출은 *비동기*입니다. WebHCat을 통해 쿼리를 제출한 다음, Visual Studio를 닫을 수 있습니다. 돌아와서 언제든지 결과를 참조할 수 있습니다. |

### <a name="tez-hive-job-performance-graph"></a>Tez Hive 작업 성능 그래프
Data Lake Tools for Visual Studio에서 Tez 실행 엔진이 실행하는 Hive 작업에 대한 성능 그래프를 볼 수 있습니다. Tez를 사용하도록 설정하는 방법에 대한 자세한 내용은 [HDInsight에서 Hive 사용](hdinsight-use-hive.md)을 참조하세요. 

Visual Studio에 Hive 작업이 제출되면 Visual Studio에서 작업 완료 시 그래프를 보여 줍니다. 최신 작업 상태를 보기 위해 **새로 고침** 단추를 선택해야 할 수도 있습니다.

> [!NOTE]
> 이 기능은 HDInsight 버전 3.2.4.593 이상의 클러스터에서만 사용할 수 있습니다. 이 기능은 완료된 작업에 대해서만 작동합니다. 또한 이 기능을 사용하려면 WebHCat을 통해 작업을 제출해야 합니다. HiveServer2를 통해 쿼리를 실행하면 다음 이미지가 표시됩니다. 
> 
> ![Hadoop Hive Tez 성능 그래프의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "작업 상태")

Hive 쿼리를 더 잘 이해하는 데 도움이 되도록 이 릴리스에서 Hive 연산자 보기가 추가되었습니다. 꼭짓점 내의 모든 연산자를 보려면 작업 그래프의 꼭짓점을 두 번 클릭합니다. 또한 특정 연산자를 가리켜서 연산자에 대한 자세한 정보를 볼 수도 있습니다.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Tez 작업에 대한 Hive 작업 실행 보기
Tez 작업에 대한 Hive 작업 실행 보기를 사용하여 Hive 작업에 대해 구조화되고 시각화된 정보를 얻을 수 있습니다. 또한 더 많은 작업 세부 정보도 얻을 수 있습니다. 성능 문제가 발생하면 보기를 사용하여 문제에 대한 자세한 정보를 얻을 수 있습니다. 예를 들어 각 작업의 작동 방식 및 각 작업에 대한 자세한 정보(데이터 읽기/쓰기, 일정/시작/종료 시간 등)를 얻을 수 있습니다. 이 정보를 사용하여 시각화된 정보에 따라 작업 구성 또는 시스템 아키텍처를 튜닝합니다.

![Data Lake Visual Studio Tools - 작업 실행 보기 창의 스크린샷](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "작업 실행 보기")

## <a name="run-hive-batch-jobs"></a>Hive 일괄 작업 실행
HDInsight 클러스터(대화형 쿼리 클러스터 제외)에 대해 Hive 스크립트를 테스트하는 경우 시간이 많이 걸릴 수 있습니다. 이 프로세스는 몇 분 이상 걸릴 수 있습니다. Data Lake Tools for Visual Studio는 라이브 클러스터에 연결하지 않고도 Hive 스크립트의 유효성을 로컬로 검사할 수 있습니다. 대화형 쿼리 실행에 대한 자세한 내용은 [대화형 Hive 쿼리 실행](#run-interactive-hive-queries)을 참조하세요.

Data Lake Tools for Visual Studio를 사용하여 특정 Hive 작업의 Yarn 로그를 수집하고 표시하여 Hive 작업에 대한 자세한 정보를 볼 수 있습니다.

Hive 일괄 처리 작업을 실행하는 방법에 대한 자세한 내용은 [대화형 Hive 쿼리 실행](#run-interactive-hive-queries)을 참조하세요. 이 섹션의 정보는 실행 시간이 더 긴 Hive 일괄 처리 작업 실행에 적용됩니다.

## <a name="run-pig-scripts"></a>Pig 스크립트 실행
Data Lake Tools for Visual Studio를 사용하여 Pig 스크립트를 만들고 HDInsight 클러스터에 제출할 수 있습니다. 먼저 템플릿에서 Pig 프로젝트를 만듭니다. 그런 다음, 해당 스크립트를 HDInsight 클러스터에 제출합니다.

## <a name="feedback-and-known-issues"></a>사용자 의견 및 알려진 문제
* 현재 HiveServer2 결과는 일반 텍스트 형식으로 표시되며, 이는 바람직하지 않습니다. Microsoft는 이 문제를 해결하기 위해 노력하고 있습니다.
* null 값으로 시작되는 결과가 표시되지 않는 문제는 해결되었습니다. 이 문제로 인해 차단되는 경우 지원 팀에 문의하세요.
* Visual Studio에서 만드는 HQL 스크립트는 사용자의 로컬 지역 설정에 따라 인코딩됩니다. 클러스터에 스크립트를 이진 파일로 업로드하면 스크립트가 제대로 실행되지 않습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Data Lake Tools for Visual Studio 패키지를 사용하여 Visual Studio에서 HDInsight 클러스터에 연결하는 방법을 알아보았습니다. 또한 Hive 쿼리를 실행하는 방법도 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Hadoop Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에서 Hadoop 작업 제출](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight에서 Hadoop으로 Twitter 데이터 분석](../hdinsight-analyze-twitter-data.md)

