---
title: 'IntelliJ용 Azure 도구 키트: HDInsight 클러스터용 Spark 응용 프로그램 만들기 | Microsoft Docs'
description: IntelliJ용 Azure 도구 키트를 사용하여 Scala로 작성된 Spark 응용 프로그램을 개발한 후 HDInsight Spark 클러스터로 제출합니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/25/2017
ms.author: maxluk,jejiang
ms.openlocfilehash: cb78808b515bb3385f7cf56725441a2b228f0aba
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기

IntelliJ 플러그 인용 Azure 도구 키트를 사용하여 Scala로 작성된 Spark 응용 프로그램을 개발한 후 IntelliJ IDE(통합 개발 환경)에서 직접 HDInsight Spark 클러스터로 제출합니다. 다음과 같은 몇 가지 방식으로 플러그 인을 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 응용 프로그램을 개발 및 제출합니다.
* Azure HDInsight Spark 클러스터 리소스에 액세스합니다.
* Scala Spark 응용 프로그램을 로컬로 개발 및 실행합니다.

프로젝트를 만들려면 [IntelliJ용 Azure 도구 키트를 사용하여 Spark 응용 프로그램 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) 비디오를 시청하세요.

> [!IMPORTANT]
> 이 플러그 인을 사용하면 Linux의 HDInsight Spark 클러스터용 응용 프로그램만 만들고 제출할 수 있습니다.
> 

## <a name="prerequisites"></a>필수 조건

- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
- Oracle Java Development 키트. [Oracle 웹 사이트](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.
- IntelliJ IDEA. 이 문서에서는 버전 2017.1을 사용합니다. [JetBrains 웹 사이트](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다.

## <a name="install-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트 설치
설치 지침에 대해서는 [IntelliJ용 Azure 도구 키트 설치](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)를 참조하세요.

## <a name="get-started"></a>시작
[Azure 구독에 로그인](#sign-in-to-your-azure-subscription)하거나 Ambari 사용자 이름/암호 또는 도메인 가입 자격 증명을 사용하여 [HDInsight 클러스터를 연결하여](#link-a-cluster) 시작할 수 있습니다.


## <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

1. IntelliJ IDE를 시작하고 Azure 탐색기를 엽니다. **보기** 메뉴에서 **도구 창**을 선택한 후 **Azure 탐색기**를 선택합니다.
       
   ![Azure 탐색기 링크](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음 **로그인**을 선택합니다.

3. **Azure 로그인** 대화 상자에서 **로그인**을 선택하고 Azure 자격 증명을 입력합니다.

    ![Azure 로그인 대화 상자](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. 로그인하고 나면 **구독 선택** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. **선택** 단추를 선택합니다.

    ![구독 선택 대화 상자](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. **Azure 탐색기** 탭에서 **HDInsight**를 확장하여 구독에 포함된 HDInsight Spark 클러스터를 표시합니다.
   
    ![Azure 탐색기의 HDInsight Spark 클러스터](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. 클러스터와 연결된 리소스(예: 저장소 계정)를 표시하려면 클러스터 이름 노드를 더 확장할 수 있습니다.
   
    ![확장된 클러스터-이름 노드](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

<h2 id="linkcluster">클러스터 연결</h2>
Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결할 수 있고 도메인 사용자 이름(예: user1@contoso.com)을 사용하여 보안 Hadoop 클러스터를 연결할 수 있습니다. 
1. **Azure Explorer**에서 **Link a cluster**(클러스터 연결)를 클릭합니다.

   ![클러스터 연결 상황에 맞는 메뉴](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. **클러스터 이름**, **사용자 이름** 및 **암호**를 입력합니다. 인증에 실패하면 사용자 이름과 암호를 확인해야 합니다. 필요에 따라 저장소 계정, 저장소 키를 추가한 다음, 저장소 컨테이너에서 컨테이너를 선택합니다. 저장소 정보는 왼쪽 트리의 저장소 탐색기용입니다.
   
   ![클러스터 연결 대화 상자](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 저장소 키, 사용자 이름 및 암호를 사용합니다.
   > ![IntelliJ의 저장소 탐색기](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
3. 입력 정보가 올바르면 Linked 클러스터가 **HDInsight** 노드에 표시됩니다. 이제 응용 프로그램을 연결된 클러스터에 제출할 수 있습니다.

   ![연결된 클러스터](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. **Azure Explorer**에서 클러스터 연결을 해제할 수도 있습니다.
   
   ![연결되지 않은 클러스터](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에서 Spark Scala 응용 프로그램 실행

1. IntelliJ IDEA를 시작하고 프로젝트를 만듭니다. **새 프로젝트** 대화 상자에서 다음을 수행합니다. 

   a. **HDInsight** > **HDInsight의 Spark(Scala)** 를 선택합니다.

   나. **빌드 도구** 목록에서 요구 사항에 따라 다음 중 하나를 선택합니다.

      * Scala 프로젝트 만들기 마법사 지원에 대해 **Maven**
      * 종속성 관리 및 Scala 프로젝트에 대한 빌드에 대해 **SBT**

    ![새 프로젝트 대화 상자](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. **다음**을 선택합니다.

3. Scala 프로젝트 생성 마법사는 Scala 플러그 인이 설치되어 있는지 여부를 자동으로 검색합니다. **설치**를 선택합니다.

   ![Scala 플러그 인 검사](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Scala 플러그 인을 다운로드하려면 **확인**을 선택합니다. 지침에 따라 IntelliJ를 다시 시작합니다. 

   ![Scala 플러그 인 설치 대화 상자](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. **새 프로젝트** 창에서 다음을 수행합니다.  

    ![Spark SDK 선택](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. 프로젝트 이름 및 위치를 입력합니다.

   나. **프로젝트 SDK** 드롭다운 목록에서 Spark 2.x 클러스터에 대해 **Java 1.8**을 선택하거나 Spark 1.x 클러스터에 대해 **Java 1.7**을 선택합니다.

   다. **Spark 버전** 드롭다운 목록에서 Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 적절한 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**를 선택합니다. 그렇지 않은 경우 **Spark2.x**를 선택합니다. 이 예제에서는 **Spark 2.0.2(Scala 2.11.8)** 를 사용합니다.

6. **마침**을 선택합니다.

7. Spark 프로젝트가 자동으로 아티팩트를 만듭니다. 이 아티팩트를 보려면 다음을 수행합니다.

   a. **파일** 메뉴에서 **프로젝트 구조**를 선택합니다.

   나. **프로젝트 구조** 대화 상자에서 **아티팩트**를 선택하여 만들어지는 기본 아티팩트를 봅니다. 더하기 기호(**+**)를 선택하여 사용자 고유의 아티팩트를 만들 수도 있습니다.

      ![대화 상자의 아티팩트 정보](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. 다음을 수행하여 응용 프로그램 소스 코드를 추가합니다.

   a. 프로젝트 탐색기에서 **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala 클래스**를 선택합니다.
      
      ![프로젝트 탐색기에서 Scala 클래스 만들기에 대한 명령](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   나. **새 Scala 클래스 만들기** 대화 상자에서 이름을 제공하고 **종류** 상자에 **개체**를 선택한 다음 **확인**을 선택합니다.
      
      ![새 Scala 클래스 만들기 대화 상자](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   다. **MyClusterApp.scala** 파일에 다음 코드를 붙여넣습니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, CSV 파일의 일곱 번째 열에 한 자리 수만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 **/HVACOut**에 씁니다.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. 다음을 수행하여 HDInsight Spark 클러스터에서 응용 프로그램을 실행합니다.

   a. 프로젝트 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음 **HDInsight에 Spark 응용 프로그램 제출**을 선택합니다.
      
      ![HDInsight에 Spark 응용 프로그램 제출 명령](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   나. Azure 구독 자격 증명을 입력하라는 메시지가 표시됩니다. **Spark 제출** 대화 상자에 다음 값을 제공한 다음 **제출**을 선택합니다.
      
      * **Spark 클러스터(Linux만 해당)** 의 경우 응용 프로그램을 실행하려는 HDInsight Spark 클러스터를 선택합니다.

      * IntelliJ 프로젝트에서 아티팩트를 선택하거나 하드 드라이브에서 아티팩트를 선택합니다.

      * **주 클래스 이름** 상자에서 줄임표(**...**)를 선택하고 응용 프로그램 소스 코드에서 주 클래스를 선택한 다음 **확인**을 선택합니다.

        ![주 클래스 선택 대화 상자](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * 이 예제의 응용 프로그램 코드에는 명령줄 인수가 필요하지 않거나 JAR 또는 파일을 참조하지 않으므로 나머지 상자를 비워둘 수 있습니다. 모든 정보를 제공하면 대화 상자는 다음 이미지와 같아야 합니다.
        
        ![Spark 제출 대화 상자](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   다. 창 아래쪽의 **Spark 제출** 탭에 진행 상태가 표시되기 시작합니다. 또한 **Spark 제출** 창에서 빨간색 단추를 선택하여 응용 프로그램을 중지할 수 있습니다.
      
     ![Spark 제출 창](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      작업 출력에 액세스하는 방법을 알아보려면 이 문서의 뒷부분에 나오는 "IntelliJ용 Azure 도구 키트를 사용하여 HDInsight Spark 클러스터 액세스 및 관리" 섹션을 참조하세요.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 로컬 또는 원격으로 Spark 응용 프로그램 디버깅 
Spark 응용 프로그램을 클러스터에 제출하는 또 다른 권장되는 방법이 있습니다. **구성 실행/디버그** IDE에서 매개 변수를 설정하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Spark 응용 프로그램 디버그](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)를 참조하세요.



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트를 사용하여 HDInsight Spark 클러스터 액세스 및 관리
IntelliJ용 Azure 도구 키트를 사용하여 다양한 작업을 수행할 수 있습니다.

### <a name="access-the-job-view"></a>작업 보기 액세스
1. Azure 탐색기에서 **HDInsight**, Spark 클러스터 이름을 차례로 확장한 다음 **작업**을 선택합니다.  

    ![작업 보기 노드](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. 오른쪽 창의 **Spark 작업 보기** 탭에는 클러스터에서 실행된 모든 응용 프로그램이 표시됩니다. 자세한 내용을 보려면 원하는 응용 프로그램 이름을 선택합니다.

    ![응용 프로그램 세부 정보](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >참고
    >

3. 실행 중인 기본 작업 정보를 표시하려면 작업 그래프 위로 마우스를 가져갑니다. 모든 작업이 생성하는 단계 그래프 및 정보를 보려면 작업 그래프에서 노드를 선택합니다.

    ![작업 단계 세부 정보](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. *드라이버 Stderr*, *드라이버 Stdout* 및 *디렉터리 정보*와 같은 자주 사용되는 로그를 보려면 **로그** 탭을 선택합니다.

    ![로그 세부 정보](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. 창 맨 위에 있는 링크를 클릭하여 Spark 기록 UI 및 YARN UI(응용 프로그램 수준)를 볼 수도 있습니다.

### <a name="access-the-spark-history-server"></a>Spark 기록 서버 액세스
1. Azure 탐색기에서 **HDInsight**를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음 **Spark 기록 UI 열기**를 선택합니다. 

2. 메시지가 나타나면 클러스터를 설정할 때 지정한 클러스터의 관리자 자격 증명을 입력합니다.

3. Spark 기록 서버 대시보드에서 응용 프로그램 이름을 사용하여 방금 실행을 마친 응용 프로그램을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("MyClusterApp")`을 사용하여 응용 프로그램 이름을 설정합니다. 따라서 Spark 응용 프로그램 이름은 **MyClusterApp**입니다.

### <a name="start-the-ambari-portal"></a>Ambari 포털 시작
1. Azure 탐색기에서 **HDInsight**를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음 **클러스터 관리 포털(Ambari) 열기**를 선택합니다. 

2. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 클러스터 설치 프로세스 동안 이러한 자격 증명을 지정했을 것입니다.

### <a name="manage-azure-subscriptions"></a>Azure 구독 관리
기본적으로 IntelliJ용 Azure 도구 키트에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 액세스하려는 구독을 지정할 수 있습니다. 

1. Azure 탐색기에서 **Azure** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 선택합니다. 

2. 대화 상자에서 액세스하지 않으려는 구독 옆의 확인란을 선택 취소하고 **닫기**를 선택합니다. Azure 구독에서 로그아웃하려는 경우 **로그아웃**을 선택할 수도 있습니다.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트를 사용하도록 기존 IntelliJ IDEA 응용 프로그램 변환
IntelliJ IDEA에서 만든 기존 Spark Scala 응용 프로그램을 IntelliJ용 Azure 도구 키트와 호환되도록 변환할 수 있습니다. 이렇게 하면 플러그 인을 사용하여 HDInsight Spark 클러스터에 응용 프로그램을 제출할 수 있습니다.

1. IntelliJ IDEA를 통해 만들어진 기존 Spark Scala 응용 프로그램의 경우 관련된 .iml 파일을 엽니다.

2. 루트 수준에 다음과 같은 **module** 요소가 표시됩니다.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   **module** 요소가 다음과 같이 표시되도록 해당 요소를 편집하여 `UniqueKey="HDInsightTool"`을 추가합니다.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. 변경 내용을 저장합니다. 이제 응용 프로그램이 IntelliJ용 Azure 도구 키트와 호환됩니다. 프로젝트 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하여 이 테스트를 수행할 수 있습니다. 이제 팝업 메뉴에 **HDInsight에 Spark 응용 프로그램 제출** 옵션이 표시됩니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>로컬 실행에서 *더 큰 힙 크기를 사용하세요.* 오류 발생
Spark 1.6에서 로컬 실행 동안 32비트 Java SDK를 사용하는 경우 다음과 같은 오류가 발생할 수 있습니다.

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

이러한 오류는 힙 크기가 실행할 Spark에 대해 충분히 크지 않기 때문에 발생합니다. Spark에는 적어도 471MB가 필요합니다. 자세한 내용은 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)을 참조하세요. 한 가지 간단한 해결 방법은 64비트 Java SDK를 사용하는 것입니다. 또한 다음 옵션을 추가하여 IntelliJ의 JVM 설정을 변경할 수도 있습니다.

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![IntelliJ의 "VM 옵션" 상자에 옵션 추가](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>FAQ
클러스터를 연결하는 경우 저장소 자격 증명을 제공하는 것이 좋습니다.

![클러스터 링크, 저장소 자격 증명 제공](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

작업을 제출하는 두 가지 모드가 있습니다. 저장소 자격 증명을 제공하는 경우 작업을 제출하는 데 일괄 처리 모드가 사용됩니다. 그렇지 않으면 대화형 모드가 사용됩니다. 클러스터가 사용 중인 경우 아래 오류가 발생할 수 있습니다.

![클러스터가 사용 중인 경우 Intellij에 오류가 발생합니다.](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![클러스터가 사용 중인 경우 Intellij에 오류가 발생합니다.](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>사용자 의견 및 알려진 문제
현재 직접 Spark 출력 보기는 지원되지 않습니다.

제안 또는 피드백이 있거나 이 플러그 인을 사용할 때 문제가 발생하는 경우 hdivstool@microsoft.com으로 메일을 보내 주시기 바랍니다.

## <a name="seealso"></a>다음 단계
* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>데모
* Scala 프로젝트 만들기(비디오): [Spark Scala 응용 프로그램 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* 원격 디버그(비디오): [IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터에서 원격으로 Spark 응용 프로그램 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ용 Azure 도구 키트를 사용하여 VPN을 통해 원격으로 Spark 응용 프로그램 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 원격으로 Spark 응용 프로그램 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
