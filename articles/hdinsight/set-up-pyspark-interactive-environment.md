---
title: Azure HDInsight Tools - Visual Studio Code용 PySpark 대화형 환경 설정 | Microsoft Docs
description: Azure HDInsight Tools for Visual Studio Code를 사용하여 쿼리와 스크립트를 만들고 제출하는 방법에 대해 알아봅니다.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4ab7b95861fcd1ff75f8ac84e4f00aedb6e526f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code용 PySpark 대화형 환경 설정

다음 단계에서는 **HDInsight: PySpark 대화형**을 실행하여 Python 패키지를 설치하는 방법을 보여줍니다.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>macOS 및 Linux에서 PySpark 대화형 환경 설정
**python 3.x**를 사용하는 경우 다음 단계에 **pip3** 명령을 사용해야 합니다.

1. **Python** 및 **pip**가 설치되어 있는지 확인합니다.
 
    ![Python pip 버전](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Jupyter를 설치합니다.
    ```
    sudo pip install jupyter
    ```
   Linux 및 macOS에서 다음과 같은 오류 메시지가 표시될 수 있습니다.

   ![오류 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. **libkrb5-dev**를 설치합니다(Linux에만 해당). 다음 오류 메시지가 표시될 수 있습니다.

   ![오류 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. **sparkmagic**을 설치합니다.
   ```
   sudo pip install sparkmagic
   ```

4. 다음을 실행하여 **ipywidgets**가 제대로 설치되었는지 확인합니다.
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![래퍼 커널 설치](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. 래퍼 커널을 설치합니다. **pip show sparkmagic**을 실행합니다. 출력은 **sparkmagic** 설치에 대한 경로를 보여줍니다. 

    ![sparkmagic 위치](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. 위치로 이동한 후 다음을 실행합니다.

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. 설치 상태를 확인합니다.

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    사용할 수 있는 커널: 
    - **python2** 및 **pysparkkernel**은 **python 2.x**에 해당합니다. 
    - **python3** 및 **pyspark3kernel**은 **python 3.x**에 해당합니다. 

8. VS Code를 다시 시작하고 **HDInsight: PySpark 대화형**을 실행하는 스크립트 편집기로 돌아옵니다.

## <a name="next-steps"></a>다음 단계

### <a name="demo"></a>데모
* HDInsight for VS Code: [비디오](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>도구 및 확장
* [Azure HDInsight Tool for Visual Studio Code 사용](hdinsight-for-vscode.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](spark/apache-spark-intellij-tool-plugin.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 SSH를 통해 원격으로 Spark 응용 프로그램 디버그](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 VPN을 통해 원격으로 Spark 응용 프로그램 디버그](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](spark/apache-spark-eclipse-tool-plugin.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Hive 데이터 시각화](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리 실행](hdinsight-connect-hive-zeppelin.md)
