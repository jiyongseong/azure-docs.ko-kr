---
title: Azure HDInsight를 사용한 Spark 문제 해결 | Microsoft Docs
description: Apache Spark 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
keywords: Azure HDInsight, Spark, FAQ, 문제 해결 가이드, 일반적인 문제, 응용 프로그램 구성, Ambari
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: ''
editor: ''
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 15fe5e6d2acdb8d782342b21f5db81443c44843d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Azure HDInsight를 사용한 Spark 문제 해결

Apache Ambari에서 Apache Spark 페이로드를 사용할 때의 주요 문제 및 해결 방법을 알아봅니다.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>클러스터에서 Ambari를 사용하여 Spark 응용 프로그램을 구성하는 방법

### <a name="resolution-steps"></a>해결 단계:

이 프로시저 구성 값은 이전에 HDInsight에서 설정한 값입니다. 설정해야 하는 Spark 구성 및 값을 결정하려면 [Spark 응용 프로그램 OutOfMemoryError 예외가 발생하는 원인](#what-causes-a-spark-application-outofmemoryerror-exception)을 참조하세요. 

1. 클러스터 목록에서 **Spark2**를 선택합니다.

    ![목록에서 클러스터 선택](./media/apache-troubleshoot-spark/update-config-1.png)

2. **Configs** 탭을 선택합니다.

    ![Configs 탭 선택](./media/apache-troubleshoot-spark/update-config-2.png)

3. 구성 목록에서 **Custom-spark2-defaults**를 선택합니다.

    ![custom-spark-defaults 선택](./media/apache-troubleshoot-spark/update-config-3.png)

4. **spark.executor.memory**와 같이 조정해야 하는 값 설정을 찾습니다. 이 경우 **4608m**의 값이 너무 높습니다.

    ![spark.executor.memory 필드 선택](./media/apache-troubleshoot-spark/update-config-4.png)

5. 값을 권장 설정으로 지정합니다. 이 설정에는 **2048m** 값이 권장됩니다.

    ![값을 2048m으로 변경](./media/apache-troubleshoot-spark/update-config-5.png)

6. 값을 저장하고 구성을 저장합니다. 도구 모음에서 **저장**을 선택합니다.

    ![설정 및 구성 저장](./media/apache-troubleshoot-spark/update-config-6a.png)

    주의할 필요가 있는 구성이면 알림이 표시됩니다. 항목을 확인한 후 **계속 진행**을 선택합니다. 

    ![계속 진행 선택](./media/apache-troubleshoot-spark/update-config-6b.png)

    구성 변경 내용에 대한 메모를 작성하고 **저장**을 선택합니다.

    ![변경 내용에 대한 메모 입력](./media/apache-troubleshoot-spark/update-config-6c.png)

7. 구성이 저장될 때마다 서비스를 다시 시작하라는 메시지가 표시됩니다. **다시 시작**을 선택합니다.

    ![다시 시작 선택](./media/apache-troubleshoot-spark/update-config-7a.png)

    다시 시작을 확인합니다.

    ![다시 시작 확인 선택](./media/apache-troubleshoot-spark/update-config-7b.png)

    실행 중인 프로세스를 검토할 수 있습니다.

    ![실행 중인 프로세스 검토](./media/apache-troubleshoot-spark/update-config-7c.png)

8. 구성을 추가할 수 있습니다. 구성 목록에서 **Custom-spark2-defaults**를 선택하고 **속성 추가**를 선택합니다.

    ![속성 추가 선택](./media/apache-troubleshoot-spark/update-config-8.png)

9. 새 속성을 정의합니다. 데이터 형식과 같은 특정 설정에 대한 대화 상자를 사용하여 단일 속성을 정의할 수 있습니다. 또는 줄당 하나의 정의를 사용하여 여러 속성을 정의할 수 있습니다. 

    이 예제에서 **spark.driver.memory** 속성의 값은 **4g**로 정의되었습니다.

    ![새 속성 정의](./media/apache-troubleshoot-spark/update-config-9.png)

10. 6단계와 7단계에서 설명한 대로 구성을 저장하고 서비스를 다시 시작합니다.

이러한 변경 내용은 클러스터 전체를 대상으로 하지만 Spark 작업을 제출할 때 재정의할 수 있습니다.

### <a name="additional-reading"></a>추가 참조 자료

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>클러스터에서 Jupyter Notebook을 사용하여 Spark 응용 프로그램을 구성하는 방법

### <a name="resolution-steps"></a>해결 단계:

1. 설정해야 하는 Spark 구성 및 값을 결정하려면 [Spark 응용 프로그램 OutOfMemoryError 예외가 발생하는 원인](#what-causes-a-spark-application-outofmemoryerror-exception)을 참조하세요.

2. Jupyter Notebook의 첫 번째 셀에서 **%%configure** 지시문 뒤에 유효한 JSON 형식의 Spark 구성을 지정합니다. 필요에 따라 실제 값을 변경합니다.

    ![구성 추가](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>추가 참조 자료

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>클러스터에서 Livy를 사용하여 Spark 응용 프로그램을 구성하는 방법

### <a name="resolution-steps"></a>해결 단계:

1. 설정해야 하는 Spark 구성 및 값을 결정하려면 [Spark 응용 프로그램 OutOfMemoryError 예외가 발생하는 원인](#what-causes-a-spark-application-outofmemoryerror-exception)을 참조하세요. 

2. cURL 같은 REST 클라이언트를 사용하여 Livy로 Spark 응용 프로그램을 제출합니다. 다음과 유사한 명령을 사용합니다. 필요에 따라 실제 값을 변경합니다.

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>추가 참조 자료

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>클러스터에서 spark-submit을 사용하여 Spark 응용 프로그램을 구성하는 방법

### <a name="resolution-steps"></a>해결 단계:

1. 설정해야 하는 Spark 구성 및 값을 결정하려면 [Spark 응용 프로그램 OutOfMemoryError 예외가 발생하는 원인](#what-causes-a-spark-application-outofmemoryerror-exception)을 참조하세요.

2. 다음과 비슷한 명령을 사용하여 spark-shell을 시작합니다. 필요에 따라 구성의 실제 값을 변경합니다. 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>추가 참조 자료

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Spark 응용 프로그램 OutofMemoryError 예외를 발생시키는 원인

### <a name="detailed-description"></a>자세한 설명

Spark 응용 프로그램이 다음과 같은 유형의 확인(catch)할 수 없는 예외로 인해 실패합니다.

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>가능한 원인:

이 예외의 가장 가능성 높은 원인은 JVM(Java Virtual Machine)에 할당된 힙 메모리가 충분하지 않다는 것입니다. 이러한 JVM은 Spark 응용 프로그램의 일부로 실행기 또는 드라이버로서 시작됩니다. 

### <a name="resolution-steps"></a>해결 단계:

1. Spark 응용 프로그램에서 처리하는 데이터의 최대 크기를 결정합니다. 입력 데이터의 최대 크기, 입력 데이터 변환을 통해 생성되는 중간 데이터, 응용 프로그램이 중간 데이터를 추가적으로 변환할 때 생성되는 출력 데이터에 따라 이러한 크기를 추측해 볼 수 있습니다. 초기에 공식적으로 추측할 수 없는 경우 이 프로세스를 반복할 수 있습니다. 

2. 사용할 HDInsight 클러스터가 Spark 응용 프로그램을 수용할 수 있는 메모리와 코어 등 충분한 리소스를 갖추고 있는지 확인하세요. YARN UI에서 Cluster Metrics 섹션에 있는 **Memory Used** 값과 Memory Total 값, VCores Used 값과 **Memory Total** 값, **VCores Used** 값과 **VCores Total** 값을 검토하여 이를 확인할 수 있습니다.

3. 다음 Spark 구성을 사용 가능한 메모리 및 코어의 90%를 초과하지 않는 적절한 값으로 설정합니다. 값은 Spark 응용 프로그램의 메모리 요구 사항을 벗어나지 않아야 합니다. 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    모든 실행기에서 사용하는 총 메모리를 계산하려면: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   드라이버에서 사용하는 총 메모리를 계산하려면:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>추가 참조 자료

- [Spark 메모리 관리 개요(영문)](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [HDInsight 클러스터에서 Spark 응용 프로그램 디버그](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>참고 항목
[Azure HDInsight를 사용하여 문제 해결](../../hdinsight/hdinsight-troubleshoot-guide.md)

