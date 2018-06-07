---
title: 데이터 과학 Virtual Machine에 대한 데이터 플랫폼 - Azure | Microsoft Docs
description: 데이터 과학 Virtual Machine에 대한 데이터 플랫폼.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: b3f340006801287383c2afb2924706affbd77a51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="data-platforms"></a>데이터 플랫폼

데이터 과학 Virtual Machine(DSVM)을 사용하여 광범위한 데이터 플랫폼에 대한 분석을 빌드할 수 있습니다. 원격 데이터 플랫폼에 대 한 인터페이스를 외에도 DSVM 신속 하 게 개발 및 프로토타입 생성에 대 한 로컬 인스턴스를 제공합니다. 

다음은 DSVM에서 지원되는 데이터 플랫폼 도구입니다. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 관계형 데이터베이스 인스턴스      |
| 지원되는 DSVM 버전      | Windows      |
| 일반적인 사용 용도      | 더 작은 데이터 집합을 사용하여 로컬에서 빠른 개발 <br/> In-database R 실행   |
| 샘플에 대한 링크      |    뉴욕 시 데이터 집합의 작은 샘플은 SQL 데이터베이스 `nyctaxi`로 로드됩니다. <br/> Microsoft R 및 데이터베이스 내 분석을 보여주는 Jupyter 샘플은 다음에서 찾을 수 있습니다.<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM의 관련 도구       | SQL Server Management Studio <br/> ODBC/JDBC 드라이버<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 Developer Edition은 개발 및 테스트 목적으로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스나 SQL Server VM 중 하나가 필요합니다. 


### <a name="setup"></a>설정

데이터베이스 서버는 이미 미리 구성되었고 Microsoft SQL Server와 관련된 Windows 서비스(`SQL Server (MSSQLSERVER)`과 같음)는 자동으로 실행되도록 설정됩니다. 실행할 유일한 수동 단계는 Microsoft R을 사용하여 데이터베이스 내 분석을 사용하도록 설정할 수 있습니다. 컴퓨터 관리자로 로그인한 후 SQL Server Management Studio(SSMS)에서 일회성 작업으로 다음 명령을 실행함으로써 이렇게 할 수 있습니다. SSMS에서 "새 쿼리"를 열고 선택한 데이터베이스가 `master`인지 확인한 후 실행합니다. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
SQL Server Management Studio를 실행하려면 프로그램 목록에서 "SQL Server Management Studio"를 검색하거나 Windows 검색을 사용하여 그것을 찾아 실행할 수 있습니다. 자격 증명을 묻는 메시지가 나타나면 "Windows 인증"을 선택하고 컴퓨터 이름을 사용하거나 SQL Server Name에서 ```localhost```을 사용합니다. 

### <a name="how-to-use--run-it"></a>사용/실행 방법  

기본 데이터베이스 인스턴스가 장착된 데이터베이스 서버는 기본적으로 자동 실행됩니다. Microsoft SQL Server 데이터베이스에 로컬로 액세스하려면 VM에서 SQL Server Management Studio와 같은 도구를 사용할 수 있습니다. 로컬 관리자 계정에는 데이터베이스에 관리자 액세스할 권한이 있습니다. 

또한 DSVM은 Python, R을 포함하여 여러 언어로 작성된 응용 프로그램에서 Microsoft SQL Server 및 Azure SQL databases, Microsoft Azure SQL Data Warehouse와 통신하는 ODBC 드라이버와 JDBC 드라이버가 함께 제공됩니다. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>DSVM에 구성/설치 방법 

Microsoft SQL Server는 표준 방식으로 설치됩니다. 그것은 `C:\Program Files\Microsoft SQL Server`에서 찾을 수 있습니다. In‑database R은 `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`에 있습니다. DSVM에는 또한 `C:\Program Files\Microsoft\R Server\R_SERVER`에 별도 설치된 독립 실행형 Microsoft R Server 인스턴스가 있습니다. 이들 두 R 인스턴스는 라이브러리를 공유하지 않습니다.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (독립 실행형)

| | |
| ------------- | ------------- |
| 무엇인가요?   | 빠른 대규모 데이터 처리 및 기계 학습을 위한 시스템인, 많이 사용되는 Apache Spark 플랫폼의 독립 실행형(단일 노드 In Process) 인스턴스     |
| 지원되는 DSVM 버전      | Linux <br /> Windows (실험적)      |
| 일반적인 사용 용도      | * 더 작은 데이터 집합을 사용하여 로컬에서 빠르게 Spark/PySpark 응용 프로그램을 개발하고 나중에 Azure HDInsight와 같은 큰 Spark 클러스터에 배포합니다<br/> * Microsoft R Server Spark 컨텍스트를 테스트합니다. <br />* SparkML 또는 Microsoft의 오픈 소스인 [MMLSpark](https://github.com/Azure/mmlspark) 라이브러리를 사용하여 ML 응용 프로그램을 빌드합니다  |
| 샘플에 대한 링크      |    Jupyter 샘플: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server(Spark 컨텍스트): /dsvm/samples/MRS/MRSSparkContextSample.R |
| DSVM의 관련 도구       | PySpark, Scala<br/>Jupyter (Spark/PySpark 커널)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>사용 방법
명령줄에서 `spark-submit` 또는 `pyspark` 명령으로 Spark 작업을 제출하여 Spark를 실행할 수 있습니다. 또한 Spark 커널로 새 노트북을 만듦으로써 Jupyter 노트북을 만들 수 있습니다. 

DSVM에 사용할 수 있는 SparkR, Sparklyr, 또는 Microsoft R Server와 같은 라이브러리를 사용하여 R에서 Apache Spark을 사용할 수 있습니다. 앞의 표에서 샘플에 대한 포인터를 참조하세요. 

> [!NOTE]
> Microsoft R Server를 DSVM의 Spark 컨텍스트에서 실행하는 것은 Ubuntu Linux DSVM 버전에서만 지원됩니다. 



### <a name="setup"></a>설정
Ubuntu Linux DSVM 버전의 Microsoft R Server Spark 컨텍스트에서 실행하기 전에 로컬 단일 노드 Hadoop HDFS 및 Yarn 인스턴스를 사용하도록 설정하려면 일회성 설정 단계를 수행해야 합니다. 기본적으로 Hadoop 서비스는 설치되지만 DSVM에서 사용하지 않도록 설정됩니다. 활성화하기 위해 처음으로 루트로 다음 명령을 실행해야 합니다.

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

필요하지 않을 때 ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn````을 실행하여 서비스와 관련된 Hadoop을 중지할 수 있습니다. 원격 Spark 컨텍스트(DSVM의 독립 실행형 Spark 인스턴스)에서 MRS를 개발 및 테스트하는 방법을 보여주는 샘플은 `/dsvm/samples/MRS` 디렉터리에서 제공되고 사용할 수 있습니다. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>DSVM에 구성/설치 방법 
|플랫폼|설치 위치($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Azure Blob 또는 Azure Data Lake storage(ADLS)에서 데이터에 액세스하기 위한 라이브러리 및 Microsoft의 MMLSpark 기계 학습을 사용하여 라이브러리가 SPARK_HOME/jar에 미리 설치 됩니다. 이러한 JAR은 Apache Spark가 시작될 때 자동으로 로드됩니다. 기본적으로 Apache Spark는 로컬 디스크의 데이터를 사용합니다. DSVM에서 Spark 인스턴스가 Azure blob 또는 ADLS에 저장된 데이터에 액세스하려면 Azure blob 및 Azure Data Lake Storage에 대한 적절한 자격 증명을 사용하여 $SPARK_HOME/conf/core-site.xml.template(Blob 및 ADLS 구성에 대한 자리 표시자가 있음)에서 찾은 템플릿을 기반으로 하는 `core-site.xml` 파일을 생성/구성해야 합니다. [여기](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application)서 ADLS 서비스 자격 증명을 만드는 방법에 대한 자세한 단계를 찾을 수 있습니다. Azure blob 또는 ADLS에 대한 자격 증명을 core-site.xml 파일에 입력하고 나면 wasb:// 또는 adl://의 URI 접두사와 함께 해당 원본에 저장된 데이터를 참조할 수 있습니다. 

