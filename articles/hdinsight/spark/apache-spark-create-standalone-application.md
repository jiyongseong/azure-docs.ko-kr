---
title: '자습서: IntelliJ를 사용하여 HDInsight의 Spark에 대한 Scala Maven 응용 프로그램 만들기 | Microsoft Docs'
description: Apache Maven을 빌드 시스템으로 사용하여 Scala에서 작성된 Spark 응용 프로그램 및 IntelliJ IDEA에서 제공하는 Scala에 대한 기존 Maven 원형을 만듭니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: c72f513c7134c556afa5fa5d0b94c17b1142be54
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>자습서: IntelliJ를 사용하여 HDInsight의 Spark에 대한 Scala Maven 응용 프로그램 만들기

이 자습서에서는 IntelliJ IDEA에서 Maven을 사용하여 Scala로 작성한 Spark 응용 프로그램을 만드는 방법을 알아봅니다. 문서는 빌드 시스템으로 Apache Maven을 사용하고 IntelliJ IDEA에서 제공하는 Scala에 대한 기존 Maven 원형으로 시작합니다.  IntelliJ IDEA에서 Scala 응용 프로그램 만들기는 다음 단계를 포함합니다.

* 빌드 시스템으로 Maven을 사용합니다.
* POM(프로젝트 개체 모델) 파일을 업데이트하여 Spark 모듈 종속성을 해결합니다.
* Scala에서 응용 프로그램을 작성합니다.
* HDInsight Spark 클러스터에 제출할 수 있는 jar 파일을 생성합니다.
* Livy를 사용하여 Spark 클러스터에서 응용 프로그램을 실행합니다.

> [!NOTE]
> 또한 HDInsight는 Linux의 HDInsight Spark 클러스터에 대한 응용 프로그램을 만들고 제출하는 과정을 용이하게 하는 IntelliJ IDEA 플러그 인 도구를 제공합니다. 자세한 내용은 [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램 만들기 및 제출](apache-spark-intellij-tool-plugin.md)을 참조하세요.
> 

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * IntelliJ를 사용 하 여 Scala Maven 응용 프로그램 개발

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.


## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.
* Java IDE. 이 문서에서는 IntelliJ IDEA 18.1.1을 사용합니다. [여기](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다.

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA용 Scala 플러그 인 설치
Scala 플러그 인을 설치하려면 다음 단계를 사용합니다.

1. IntelliJ IDEA를 엽니다.
2. 시작 화면에서 **구성**을 선택하고 **플러그 인**을 선택합니다.
   
    ![scala 플러그 인 활성화](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
3. 왼쪽 아래 모서리에서 **JetBrains 플러그 인 설치**를 선택합니다. 
4. **JetBrains 플러그 인 찾아보기** 대화 상자에서 **Scala**를 검색한 다음, **설치**를 선택합니다.
   
    ![scala 플러그 인 설치](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
5. 플러그 인이 성공적으로 설치된 후에 IDE를 다시 시작해야 합니다.

## <a name="create-a-standalone-scala-project"></a>독립 실행형 Scala 프로젝트 만들기
1. IntelliJ IDEA를 엽니다.
2. **파일** 메뉴에서 **새로 만들기 > 프로젝트**를 선택하여 새 프로젝트를 만듭니다.
3. 새 프로젝트 대화 상자에서 다음과 같이 선택합니다.
   
    ![Maven 프로젝트 만들기](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * 프로젝트 유형으로 **Maven** 을 선택합니다.
   * **프로젝트 SDK**를 지정합니다. **새로 만들기**를 선택하고 Java 설치 디렉터리(일반적으로 `C:\Program Files\Java\jdk1.8.0_66`)로 이동합니다.
   * **원형으로부터 만들기** 옵션을 선택합니다.
   * 원형 목록에서 **org.scala-tools.archetypes:scala-archetype-simple**을 선택합니다. 이 원형은 올바른 디렉터리 구조를 만들고 Scala 프로그램 작성에 필요한 기본 종속성을 다운로드합니다.
4. **다음**을 선택합니다.
5. **GroupId**, **ArtifactId** 및 **버전**에 관련 값을 제공합니다. 이 자습서에서는 다음 값이 사용됩니다.

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
6. **다음**을 선택합니다.
7. 설정을 검토한 후 **다음**을 선택합니다.
8. 프로젝트 이름과 위치를 확인하고 **마침**을 선택합니다.
9. 왼쪽 창에서 **src > test > scala > com > microsoft > spark > example**를 선택하고 **MySpec**을 마우스 오른쪽 단추로 클릭한 후 **삭제**를 선택합니다. 응용 프로그램에 이 파일이 필요하지는 않습니다.
  
10. 이후 단계에서는 pom.xml을 업데이트하여 Spark Scala 응용 프로그램에 대한 종속성을 정의합니다. 다운로드되고 자동으로 해결되는 이러한 종속성의 경우 Maven을 적절하게 구성해야 합니다.
   
    ![자동 다운로드를 위해 Maven 구성](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. **파일** 메뉴에서 **설정**을 선택합니다.
   2. **설정** 대화 상자에서 **빌드, 실행, 배포** > **빌드 도구** > **Maven** > **가져오기**로 이동합니다.
   3. **Maven 프로젝트 자동으로 가져오기**에 옵션을 선택합니다.
   4. **적용**을 선택한 다음 **확인**을 선택합니다.
11. 왼쪽 창에서 **src > main > scala > com.microsoft.spark.example**을 선택하고 **앱**을 두 번 클릭하여 App.scala를 엽니다.

12. 기존 샘플 코드를 다음 코드로 바꾸고 변경 내용을 저장합니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, 여섯 번째 열에 한 자리만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 **/HVACOut** 에 씁니다.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
13. 왼쪽 창에서 **pom.xml**을 두 번 클릭합니다.
   
   1. `<project>\<properties>`에 다음 세그먼트를 추가합니다.
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. `<project>\<dependencies>`에 다음 세그먼트를 추가합니다.
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      pom.xml에 변경 내용을 저장합니다.
10. .jar 파일을 만듭니다. IntelliJ IDEA는 프로젝트의 아티팩트로 JAR을 작성할 수 있습니다. 다음 단계를 수행합니다.
    
    1. **파일** 메뉴에서 **프로젝트 구조**를 선택합니다.
    2. **프로젝트 구조** 대화 상자에서 **아티팩트**를 선택한 다음, +(더하기) 기호를 선택합니다. 팝업 대화 상자에서 **JAR**, **종속성이 있는 모듈에서**를 차례로 선택합니다.
       
        ![JAR 만들기](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. **모듈에서 JAR 만들기** 대화 상자에서 **주 클래스**에 대한 ![ellipsis](./media/apache-spark-create-standalone-application/ellipsis.png)(줄임표)를 선택합니다.
    4. **주 클래스 선택** 대화 상자에서 기본적으로 표시되는 클래스를 선택한 다음, **확인**을 선택합니다.
       
        ![JAR 만들기](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. **모듈에서 JAR 만들** 대화 상자에서 **대상 JAR에 추출** 옵션이 선택되었는지 확인한 다음, **확인**을 선택합니다.  이 설정을 사용하면 모든 종속성이 있는 단일 JAR이 만들어집니다.
       
        ![JAR 만들기](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. 출력 레이아웃 탭에는 Maven 프로젝트의 일부분으로 포함된 jar이 모두 나열됩니다. 직접 종속성이 없는 Scala 응용 프로그램을 선택하고 삭제할 수 있습니다. 여기에서 만드는 응용 프로그램의 경우 마지막 것(**SparkSimpleApp 컴파일 출력**)을 제외한 모두를 제거할 수 있습니다. jar을 선택하여 삭제한 다음 **삭제** 아이콘을 선택합니다.
       
        ![JAR 만들기](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        프로젝트를 작성하거나 업데이트할 때마다 jar이 생성되는지 확인하는 **프로젝트 빌드에 포함** 확인란이 선택되었는지 확인합니다. **적용**을 선택한 다음, **확인**을 선택합니다.
    7. **빌드** 메뉴에서 **빌드 아티팩트**를 선택하여 jar을 만듭니다. **\out\artifacts** 아래에 출력 jar이 만들어집니다.
       
        ![JAR 만들기](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Spark 클러스터에서 응용 프로그램 실행
클러스터에서 응용 프로그램을 실행하려면 다음 방법을 사용할 수 있습니다.

* **Azure 저장소 Blob에 응용 프로그램 jar을 복사** 합니다. [**AzCopy**](../../storage/common/storage-use-azcopy.md) 명령줄 유틸리티를 사용하면 이렇게 할 수 있습니다. 데이터를 업로드하는 데 사용할 수 있는 다른 클라이언트도 많이 있습니다. [HDInsight에서 Hadoop 작업용 데이터 업로드](../hdinsight-upload-data.md)에서 자세한 정보를 찾을 수 있습니다.
* **Livy를 사용하여 응용 프로그램 작업을 원격으로** Spark 클러스터에 제출합니다. HDInsight의 Spark 클러스터에는 Spark 작업을 원격으로 제출하는 REST 끝점을 노출하는 Livy가 포함됩니다. 자세한 내용은 [HDInsight의 Spark 클러스터와 함께 Livy를 사용하여 원격으로 Spark 작업 제출](apache-spark-livy-rest-interface.md)을 참조하세요.

## <a name="next-step"></a>다음 단계

이 문서에서는 Spark Scala 응용 프로그램을 만드는 방법을 배웠습니다. 다음 문서를 진행하여 Livy를 사용하여 HDInsight Spark 클러스터에서 이 응용 프로그램을 실행하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
>[Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](./apache-spark-livy-rest-interface.md)

