---
title: 'IntelliJ용 Azure 도구 키트: SSH를 통해 원격으로 Spark 응용 프로그램 디버그 | Microsoft Docs'
description: IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 SSH를 통해 HDInsight 클러스터에서 응용 프로그램을 원격으로 디버그하는 방법에 대한 단계별 지침
keywords: IntelliJ 원격으로 디버그, IntelliJ 원격 디버깅, SSH, IntelliJ, HDInsight, IntelliJ 디버그, 디버깅
services: hdinsight
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: ''
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: ad0b1bbfc74f992a646ac375583f3399243873f5
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Spark 응용 프로그램 디버그

이 문서에서는 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight 클러스터에서 응용 프로그램을 원격으로 디버그하는 방법에 대한 단계별 지침을 제공합니다. 프로젝트를 디버그하려는 경우 [IntelliJ용 Azure 도구 키트로 HDInsight Spark 응용 프로그램 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) 비디오를 시청할 수도 있습니다.

**필수 구성 요소**
* **IntelliJ용 Azure 도구 키트의 HDInsight 도구** 이 도구는 IntelliJ용 Azure 도구 키트의 일부입니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)를 참조하세요. **IntelliJ용 Azure 도구 키트**. 이 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램을 만듭니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)의 지침을 따르세요.

* **사용자 이름 및 암호 관리를 포함한 HDInsight SSH 서비스**. 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 및 [SSH 터널링을 사용하여 Ambari 웹 UI, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)를 참조하세요. 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>로컬 실행 및 디버깅을 수행하는 방법에 대해 알아봅니다
### <a name="scenario-1-create-a-spark-scala-application"></a>시나리오 1: Spark Scala 응용 프로그램 만들기 

1. IntelliJ IDEA를 시작하고 프로젝트를 만듭니다. **새 프로젝트** 대화 상자에서 다음을 수행합니다.

   a. **HDInsight**를 선택합니다. 

   나. 기본 설정에 따라 Java 또는 Scala 템플릿을 선택합니다. 다음 옵션 중에서 선택합니다.

      - **HDInsight의 Spark(Scala)**

      - **HDInsight의 Spark(Java)**

      - **HDInsight 샘플의 Spark(Scala)**

      이 예제에서는 **HDInsight 샘플의 Spark(Scala)** 템플릿을 사용합니다.

   다. **빌드 도구** 목록에서 요구 사항에 따라 다음 중 하나를 선택합니다.

      - Scala 프로젝트 만들기 마법사 지원에 대해 **Maven**

      -  종속성 관리 및 Scala 프로젝트에 대한 빌드에 대해 **SBT** 

      ![디버그 프로젝트 만들기](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. **다음**을 선택합니다.     
 
2. 다음 **새 프로젝트** 창에서 다음을 수행합니다.

   ![Spark SDK 선택](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. 프로젝트 이름과 프로젝트 위치를 입력합니다.

   나. **프로젝트 SDK** 드롭다운 목록에서 **Spark 2.x** 클러스터에 대해 **Java 1.8**을 선택하거나 **Spark 1.x** 클러스터에 대해 **Java 1.7**을 선택합니다.

   다. **Spark 버전** 드롭다운 목록에서 Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 올바른 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**를 선택합니다. 그렇지 않은 경우 **Spark 2.x**을 선택합니다. 이 예제에서는 **Spark 2.0.2(Scala 2.11.8)** 를 사용합니다.

   d. **마침**을 선택합니다.

3. **src** > **main** > **scala**를 선택하여 프로젝트에서 코드를 엽니다. 이 예제에서는 **SparkCore_wasbloTest** 스크립트를 사용합니다.

### <a name="prerequisite-for-windows"></a>Windows에 대한 필수 구성 요소
Windows 컴퓨터에서 로컬 Spark Scala 응용 프로그램을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에서 설명한 예외가 발생할 수 있습니다. 이 예외는 Windows에 WinUtils.exe가 없기 때문에 발생합니다. 

이 오류를 해결하려면 [실행 파일을 다운로드](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)하여 **C:\WinUtils\bin** 등의 위치에 저장합니다. 그런 다음 **HADOOP_HOME** 환경 변수를 추가하고 이 변수 값을 **C:\WinUtils**로 설정합니다.

### <a name="scenario-2-perform-local-run"></a>시나리오 2: 로컬 실행 수행
1. **SparkCore_wasbloTest** 스크립트를 열고 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 **'[Spark 작업] XXX' 실행** 옵션을 선택하여 로컬 실행을 수행합니다.
2. 로컬 실행이 완료되면 현재 프로젝트 탐색기 **data** > **__default__** 에 저장된 출력 파일을 볼 수 있습니다.

    ![로컬 실행 결과](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. 해당 도구는 로컬 실행 및 로컬 디버그를 수행하면 기본 로컬 실행 구성을 자동으로 설정합니다. 맨 위 모서리에서 **[Spark 작업] XXX** 구성을 열고 **Azure HDInsight Spark 작업** 아래에 이미 만들어진 **[Spark 작업]XXX**를 볼 수 있습니다. **로컬로 실행** 탭으로 전환합니다.

    ![로컬 실행 구성](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [환경 변수](#prerequisite-for-windows): 시스템 환경 변수 **HADOOP_HOME**을 **C:\WinUtils**로 이미 설정한 경우 자동으로 감지되므로 수동으로 추가할 필요가 없습니다.
    - [WinUtils.exe 위치](#prerequisite-for-windows): 시스템 환경 변수를 설정하지 않은 경우 해당 단추를 클릭하여 위치를 찾을 수 있습니다.
    - 두 옵션 중 하나를 선택하면 됩니다. 단, MacOS 및 Linux에서는 필요하지 않습니다.
4. 또한 로컬 실행 및 로컬 디버그를 수행하기 전에 수동으로 구성을 설정할 수 있습니다. 이전 스크린샷에서 더하기 기호(**+**)를 선택합니다. 그 다음, **Azure HDInsight Spark 작업** 옵션을 선택합니다. 저장할 **이름**, **주 클래스 이름**에 대한 정보를 입력하고 로컬 실행 단추를 클릭합니다.

### <a name="scenario-3-perform-local-debugging"></a>시나리오 3: 로컬 디버그 수행
1. **SparkCore_wasbloTest** 스크립트를 열고 중단점을 설정합니다.
2. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 **'[Spark 작업] XXX' 디버그** 옵션을 선택합니다.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>원격 실행 및 디버깅 수행 방법 알아보기
### <a name="scenario-1-perform-remote-run"></a>시나리오 1: 원격 실행 수행

1. **구성 편집** 메뉴에 액세스하려면 오른쪽 위 구석에 있는 아이콘을 선택합니다. 이 메뉴에서 원격 디버깅에 대한 구성을 만들거나 편집할 수 있습니다.

   ![구성 편집](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. **실행/디버깅 구성** 대화 상자에서 더하기 기호(**+**)를 선택합니다. 그 다음, **Azure HDInsight Spark 작업** 옵션을 선택합니다.

   ![새 구성 추가](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. **클러스터에서 원격으로 실행** 탭으로 전환합니다. **이름**, **Spark 클러스터** 및 **주 클래스 이름**에 정보를 입력합니다. 그런 다음 **고급 구성**을 선택합니다. 이 도구는 **실행기**를 사용하여 디버그를 지원합니다. **numExectors**의 기본값은 5입니다. 3보다 큰 값을 설정하지 않는 것이 좋습니다.

   ![디버그 구성 실행](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. **Spark 제출 고급 구성** 대화 상자에서 **Spark 원격 디버그 설정**을 선택합니다. SSH 사용자 이름을 입력하고 암호를 입력하거나 개인 키 파일을 사용합니다. 구성을 저장하려면 **확인**을 선택합니다. 원격 디버그를 수행하려는 경우 설정해야 합니다. 원격 실행만 사용하려는 경우는 설정할 필요가 없습니다.

   ![Spark 원격 디버그 설정](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. 이제 사용자가 입력한 이름으로 구성이 저장됩니다. 구성 세부 정보를 보려면 구성 이름을 선택합니다. 변경하려면 **구성 편집**을 선택합니다. 

6. 구성 설정을 완료한 후에 원격 클러스터에 대해 프로젝트를 실행하거나 원격 디버깅을 수행할 수 있습니다.
   
   ![원격 실행 단추](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. **연결 해제** 단추를 클릭하면 제출 로그가 왼쪽 패널에 나타나지 않습니다. 그러나 백 엔드에서는 계속 실행되고 있습니다.

   ![원격 실행 단추](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>시나리오 2: 원격 디버깅 수행
1. 중단점을 설정한 다음 **원격 디버그** 아이콘을 클릭합니다. 원격 제출과의 차이점은 SSH 사용자 이름/암호를 구성해야 한다는 것입니다.

   ![디버그 아이콘 선택](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. 프로그램 실행이 중단점에 도달하면 **디버거** 창에 1개의 **드라이버** 탭과 2개의 **실행기** 탭이 표시됩니다. **프로그램 다시 시작** 아이콘을 선택하여 코드를 계속 실행합니다. 그러면 다음 중단점에 도달합니다. 디버깅할 대상 실행기를 찾으려면 올바른 **실행기** 탭으로 전환해야 합니다. 해당 **콘솔** 탭에서 실행 로그를 검토할 수 있습니다.

   ![디버깅 탭](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>시나리오 3: 원격 디버깅 및 버그 수정 수행
1. 두 개의 중단점을 설정하고 **디버그** 아이콘을 선택하여 원격 디버깅 프로세스를 시작합니다.

2. 첫 번째 중단점에서 코드가 중지되고 **변수** 창에 매개 변수 및 변수 정보가 표시됩니다. 

3. **프로그램 다시 시작** 아이콘을 선택하여 계속합니다. 두 번째 지점에서 코드가 중지됩니다. 예상대로 예외가 catch됩니다.

   ![오류 throw](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. **프로그램 다시 시작** 아이콘을 다시 선택합니다. **HDInsight Spark 제출** 창에 “작업 실행 실패” 오류가 표시됩니다.

   ![오류 전송](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. IntelliJ 디버깅 기능을 사용하여 변수 값을 동적으로 업데이트하려면 **디버그**를 다시 선택합니다. **변수** 창이 다시 나타납니다. 

6. **디버그** 탭에서 대상을 마우스 오른쪽 단추로 클릭한 다음 **값 설정**을 선택합니다. 다음으로 변수의 새 값을 입력합니다. 그런 다음 **Enter** 키를 선택하여 값을 저장합니다. 

   ![값 설정](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. **프로그램 다시 시작** 아이콘을 선택하여 프로그램을 계속 실행합니다. 이번에는 예외가 catch되지 않습니다. 예외 없이 프로젝트가 성공적으로 실행되는지 확인할 수 있습니다.

   ![예외 없이 디버그](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>다음 단계
* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>데모
* Scala 프로젝트 만들기(비디오): [Spark Scala 응용 프로그램 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* 원격 디버그(비디오): [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터에서 원격으로 Spark 응용 프로그램 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](../hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기](apache-spark-intellij-tool-plugin.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 VPN을 통해 원격으로 Spark 응용 프로그램 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
