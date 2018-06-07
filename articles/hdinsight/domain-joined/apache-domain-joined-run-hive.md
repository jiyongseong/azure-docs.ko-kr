---
title: 도메인에 가입된 HDInsight에서 Hive 정책 구성 - Azure
description: 유용한 정보
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: bd99e5fda80663b37c60d972742b16c27b92cf55
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>도메인에 가입된 HDInsight에서 Hive 정책 구성
Hive에 대한 Apache Ranger 정책을 구성하는 방법에 대해 알아봅니다. 이 문서에서는 hivesampletable에 대한 액세스를 제한하는 두 개의 Ranger 정책을 만들 수 있습니다. hivesampletable은 HDInsight 클러스터와 함께 제공됩니다. 정책을 구성한 경우 Excel 및 ODBC 드라이버를 사용하여 HDInsight의 Hive 테이블에 연결합니다.

## <a name="prerequisites"></a>필수 조건
* 도메인에 가입된 HDInsight 클러스터 [도메인에 가입된 HDInsight 클러스터 구성](apache-domain-joined-configure.md)을 참조하세요.
* Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus를 포함한 워크스테이션

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 관리 UI에 연결
**Ranger 관리 UI에 연결하려면**

1. 브라우저에서 Ranger 관리 UI에 연결합니다. URL은 https://&lt;ClusterName>.azurehdinsight.net/Ranger/입니다.

   > [!NOTE]
   > Ranger는 다른 Hadoop 클러스터가 아닌 자격 증명을 사용합니다. 브라우저가 캐시된 Hadoop 자격 증명을 사용하지 않도록 방지하려면 새 InPrivate 브라우저 창을 사용하여 Ranger 관리 UI에 연결합니다.
   >
   >
2. 클러스터 관리자 도메인 사용자 이름 및 암호를 사용하여 로그인합니다.

    ![HDInsight 도메인에 가입된 Ranger 홈페이지](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Ranger는 현재 Yarn 및 Hive에서만 작동합니다.

## <a name="create-domain-users"></a>도메인 사용자 만들기
hiveruser1 및 hiveuser2를 만드는 방법에 대한 내용은 [도메인에 가입된 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster)를 참조하세요. 이 자습서에서는 두 개의 사용자 계정을 사용합니다.

## <a name="create-ranger-policies"></a>Ranger 정책 만들기
이 섹션에서는 hivesampletable에 액세스하기 위한 두 개의 Ranger 정책을 만듭니다. 다른 열 집합에 대한 선택 사용 권한을 제공합니다. 두 사용자는 모두 [도메인 가입 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster)를 사용하여 생성됩니다. 다음 섹션에서는 Excel에 있는 두 개의 정책을 테스트합니다.

**Ranger 정책을 만들려면**

1. Ranger 관리 UI를 엽니다. [Apache Ranger 관리 UI에 연결](#connect-to-apache-ranager-admin-ui)을 참조하세요.
2. **Hive**에서 **&lt;ClusterName>_hive**를 클릭합니다. 두 개의 미리 구성 정책이 표시되어야 합니다.
3. **새 정책 추가**를 클릭하고 다음 값을 입력합니다.

   * 정책 이름: read-hivesampletable-all
   * Hive 데이터베이스: 기본값
   * 테이블: hivesampletable
   * Hive 열: *
   * 사용자 선택: hiveuser1
   * 사용 권한: 선택

     ![HDInsight 도메인에 가입된 Ranger Hive 정책 구성](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png)에서도 확인할 수 있습니다.

     > [!NOTE]
     > 사용자 선택에서 도메인 사용자가 채워지지 않으면 Ranger가 AAD와 동기화되기를 몇 분 정도 기다립니다.
     >
     >
4. **추가** 를 클릭하여 정책을 저장합니다.
5. 다음 속성을 가진 다른 정책을 만들려면 마지막 두 단계를 반복합니다.

   * 정책 이름: read-hivesampletable-devicemake
   * Hive 데이터베이스: 기본값
   * 테이블: hivesampletable
   * Hive 열: clientid, devicemake
   * 사용자 선택: hiveuser2
   * 사용 권한: 선택

## <a name="create-hive-odbc-data-source"></a>Hive ODBC 데이터 원본 만들기
[Hive ODBC 데이터 원본 만들기](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)에서 지침을 찾을 수 있습니다.  

 | 자산  |설명 |
 | --- | --- |
 | 데이터 원본 이름 | 데이터 원본에 이름 지정 |
 | 호스트 | &lt;HDInsightClusterName>.azurehdinsight.net을 입력합니다. 예를 들면 myHDICluster.azurehdinsight.net과 같습니다. |
 | 포트 | **443**을 사용합니다. (이 포트는 563에서 443으로 변경됨) |
 | 데이터베이스 | **기본값**을 사용합니다. |
 | Hive 서버 유형 | **Hive 서버 2** 선택 |
 | 메커니즘 | **Azure HDInsight Service** 선택 |
 | HTTP 경로 | 비워 둠 |
 | 사용자 이름 | hiveuser1@contoso158.onmicrosoft.com을 입력합니다. 사용자 이름이 다른 경우 도메인 이름을 업데이트합니다. |
 | 암호 | hiveuser1의 암호를 입력합니다. |

데이터 원본을 저장하기 전에 **테스트**를 클릭해야 합니다.

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight에서 Excel로 데이터 가져오기
마지막 섹션에서 두 개의 정책을 구성했습니다.  hiveuser1에는 모든 열에 대한 선택 사용 권한이 있으며 hiveuser2에는 두 열에 대한 선택 사용 권한이 있습니다. 이 섹션에서는 데이터를 Excel로 가져오는 두 사용자를 가장할 수 있습니다.

1. Excel에서 새 통합 문서나 기존 통합 문서를 엽니다.
2. **데이터** 탭에서 **다른 데이터 원본에서**를 클릭한 다음 **데이터 연결 마법사에서**를 클릭하여 **데이터 연결 마법사**를 시작합니다.

    ![데이터 연결 마법사 열기][img-hdi-simbahiveodbc.excel.dataconnection]
3. 데이터 원본으로 **ODBC DSN**을 선택한 후 **다음**을 클릭합니다.
4. ODBC 데이터 원본에서 이전 단계에서 만든 데이터 원본 이름을 선택한 후 **다음**을 클릭합니다.
5. 마법사에서 클러스터의 암호를 다시 입력한 후 **확인**을 클릭합니다. **데이터베이스 및 테이블 선택** 대화 상자가 열릴 때까지 기다립니다. 몇 초 정도 걸릴 수 있습니다.
6. **hivesampletable**을 선택하고 **다음**을 클릭합니다.
7. **Finish**를 클릭합니다.
8. **데이터 가져오기** 대화 상자에서 쿼리를 변경하거나 지정할 수 있습니다. 이렇게 하려면 **속성**을 클릭합니다. 몇 초 정도 걸릴 수 있습니다.
9. **정의** 탭을 클릭합니다. 명령 텍스트는 다음과 같습니다.

       SELECT * FROM "HIVE"."default"."hivesampletable"

   정의한 Ranger 정책으로 hiveuser1에는 모든 열에 대한 선택 사용 권한이 생성됩니다.  따라서 이 쿼리를 hiveuser1의 자격 증명에서 사용할 수 있지만 hiveuser2의 자격 증명과 함께 사용할 수 없습니다.

   ![연결 속성][img-hdi-simbahiveodbc-excel-connectionproperties]
10. **확인** 을 클릭하여 연결 속성 대화 상자를 닫습니다.
11. **확인**을 클릭하여 **데이터 가져오기** 대화 상자를 닫습니다.  
12. hiveuser1의 암호를 다시 입력하고 **확인**을 클릭합니다. 데이터를 Excel로 가져올 때까지 몇 초 정도 걸립니다. 작업이 완료되면 11개 열의 데이터가 표시됩니다.

지난 섹션에서 만든 두 번째 정책(read-hivesampletable-devicemake)을 테스트하려면

1. Excel에서 새 시트를 추가합니다.
2. 데이터를 가져오는 마지막 절차를 따릅니다.  유일한 차이점은 hiveuser1의 자격 증명 대신 hiveuser2의 자격 증명을 사용하는 것입니다. hiveuser2에 두 열을 표시하는 사용 권한이 있기 때문에 작업에 실패합니다. 다음 오류가 표시됩니다.

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. 데이터를 가져오는 동일한 절차를 따릅니다. 또한 이번에는 hiveuser2의 자격 증명을 사용하여 다음에서 select 문을 수정합니다.

        SELECT * FROM "HIVE"."default"."hivesampletable"

    to:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    작업이 완료되면 가져온 두 개 열의 데이터가 표시됩니다.

## <a name="next-steps"></a>다음 단계
* 도메인에 가입된 HDInsight 클러스터 구성에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 구성](apache-domain-joined-configure.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터 관리에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 관리](apache-domain-joined-manage.md)를 참조하세요.
* 도메인에 가입된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하려면 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.
* Hive JDBC를 사용하여 Hive를 연결하는 자세한 내용은 [Hive JDBC 드라이버를 사용하여 Azure HDInsight에서 Hive에 연결](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)을 참조하세요.
* Hive ODBC를 사용하여 Hadoop에 Excel을 연결하는 자세한 내용은 [Microsoft Hive ODBC 드라이브와 함께 Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 참조하세요.
* 파워 쿼리를 사용하여 Hadoop에 Excel을 연결하는 자세한 내용은 [파워 쿼리를 사용하여 Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-power-query.md)을 참조하세요.
