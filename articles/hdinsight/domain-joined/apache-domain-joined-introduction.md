---
title: HDInsight - 도메인에 가입된 HDInsight 클러스터 - Azure
description: 유용한 정보
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 6225bd824e3bcff24b84c79f39ce209f16caafd8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>도메인 조인 HDInsight 클러스터 소개를 사용한 Hadoop 보안 소개

오늘날까지 Azure HDInsight는 단일 사용자 로컬 관리자만을 지원했습니다. 따라서 소규모 응용 프로그램 팀이나 부서의 경우에 유용하게 작동했습니다. Hadoop 기반 워크로드가 기업 부문에서 더 많은 인기를 얻게 되면서 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 액세스 제어와 같은 기업 수준 기능에 대한 필요성이 점점 더 중요해졌습니다. 도메인에 가입된 HDInsight 클러스터를 사용하면 Active Directory 도메인에 가입된 HDInsight 클러스터를 만들고 HDInsight 클러스터에 로그온하는 Azure Active Directory를 통해 인증할 수 있는 기업의 직원 목록을 구성할 수 있습니다. 기업 외부의 사용자는 HDInsight 클러스터에 로그온하거나 액세스할 수 없습니다. 기업 관리자는 [Apache Ranger](http://hortonworks.com/apache/ranger/)를 사용하여 Hive 보안용 역할 기반 액세스 제어를 구성할 수 있게 되어 필요한 만큼 데이터에 대한 액세스를 제한할 수 있습니다. 마지막으로 관리자는 직원의 데이터 액세스 및 액세스 제어 정책에 대한 변경 내용을 감사할 수 있게 되어 회사 리소스를 고도로 관리할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명하는 새 기능은 Hadoop, Spark 및 대화형 쿼리와 같은 클러스터 형식에서만 사용할 수 있습니다. HBase, Storm 및 Kafka와 같은 다른 워크로드는 이후 릴리스에서 사용할 수 있습니다.

> [!IMPORTANT]
> 도메인에 연결된 HDInsight에서는 Oozie를 사용할 수 없습니다.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="benefits"></a>이점
기업 보안에는 네 가지 주요 요소인 경계 보안, 인증, 권한 부여 및 암호화가 포함됩니다.

![도메인에 가입된 HDInsight 클러스터는 기본 요소를 제공합니다.](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png)에서도 확인할 수 있습니다.

### <a name="perimeter-security"></a>경계 보안
HDInsight에서 경계 보안은 가상 네트워크 및 게이트웨이 서비스를 사용하여 이루어집니다. 오늘날의 기업 관리자는 가상 네트워크 내에서 HDInsight 클러스터를 만들고 네트워크 보안 그룹(인바운드 또는 아웃바운드 방화벽 규칙)을 사용하여 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 인바운드 방화벽 규칙에 정의된 IP 주소만이 HDInsight 클러스터와 통신할 수 있게 되므로 경계 보안을 제공할 수 있습니다. 다른 경계 보안 계층은 게이트웨이 서비스를 사용하여 이루어집니다. 게이트웨이는 HDInsight 클러스터에 들어오는 모든 요청에 대한 첫 번째 방어선의 역할을 하는 서비스입니다. 요청을 받아들이고, 유효성 검사를 수행하고 요청을 클러스터의 다른 노드로 전달하도록 허용하여 클러스터에서 다른 이름 및 데이터 노드에 대한 경계 보안을 제공합니다.

### <a name="authentication"></a>인증
기업 관리자는 [가상 네트워크](https://azure.microsoft.com/services/virtual-network/)에서 도메인에 가입된 HDInsight 클러스터를 만들 수 있습니다. HDInsight 클러스터의 노드는 기업에서 관리하는 도메인에 가입됩니다. 이 작업은 [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md)를 사용하여 이루어집니다. 클러스터의 모든 노드는 기업에서 관리하는 도메인에 가입됩니다. 이 설정을 통해 기업 직원은 해당 도메인 자격 증명을 사용하여 클러스터 노드에 로그온할 수 있습니다. 또한 클러스터와의 상호 작용하기 위해 해당 도메인 자격 증명을 사용하여 Hue, Ambari 뷰, ODBC, JDBC, PowerShell 및 REST API와 같은 다른 승인된 엔드포인트로 인증할 수도 있습니다. 관리자는 이러한 끝점을 통해 클러스터와 상호 작용하는 사용자의 수를 완벽히 제한합니다.

### <a name="authorization"></a>권한 부여
대부분의 기업이 선택한 모범 사례는 모든 직원에게 기업 리소스에 대한 모든 액세스를 부여하지 않는 것입니다. 마찬가지로 이 릴리스에서 관리자는 클러스터 리소스에 대한 역할 기반 액세스 제어 정책을 정의할 수 있습니다. 예를 들어, 관리자는 [Apache Ranger](http://hortonworks.com/apache/ranger/)를 구성하여 Hive에 대한 액세스 제어 정책을 설정할 수 있습니다. 이 기능을 사용하여 직원이 작업을 수행하는 데 필요한 만큼의 데이터에만 액세스할 수 있도록 합니다. 클러스터에 대한 SSH 액세스는 관리자에게만 제한됩니다.

### <a name="auditing"></a>감사
데이터는 권한이 없는 사용자에게서 HDInsight 클러스터 리소스를 보호하고 데이터 보안을 유지하며 클러스터 리소스에 대한 모든 액세스를 감사하는 작업과 함께 리소스의 무단 또는 실수로 실행된 액세스를 추적해야 합니다. 관리자는 HDInsight 클러스터 리소스 및 데이터에 대한 모든 액세스를 확인하고 보고할 수 있습니다. 관리자는 Apache Ranger가 지원되는 끝점에서 수행한 액세스 제어 정책에 대한 모든 변경 내용을 확인하고 보고할 수도 있습니다. 도메인에 가입된 HDInsight 클러스터는 Apache Ranger UI를 사용하여 감사 로그를 검색합니다. Ranger는 백 엔드에서 로그를 저장하고 검색하는 데 [Apache Solr](http://hortonworks.com/apache/solr/)을 사용합니다.

### <a name="encryption"></a>암호화
데이터 보호는 권한이 없는 직원의 데이터 액세스 제한만큼이나 조직 보안 및 규정 준수 요구 사항을 충족하기 위해 중요하므로 암호화하여 보호되어야 합니다. HDInsight 클러스터, Azure Storage Blob 및 Azure Data Lake Storage의 데이터 저장소는 모두 투명한 서버 쪽 [미사용 데이터의 암호화](../../storage/common/storage-service-encryption.md)를 지원합니다. HDInsight 클러스터의 보안 유지는 미사용 데이터 용량의 서버 쪽 암호화에서 원활하게 작동합니다.

## <a name="next-steps"></a>다음 단계
* 도메인에 가입된 HDInsight 클러스터 구성에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 구성](apache-domain-joined-configure.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터 관리에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 관리](apache-domain-joined-manage.md)를 참조하세요.
* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하려면 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.
* VSCode를 사용하여 도메인에 가입된 클러스터에 연결하려면 [VSCode를 사용하여 도메인에 가입된 클러스터에 연결](../hdinsight-for-vscode.md#linkcluster)을 참조하세요.
* IntelliJ를 사용하여 도메인에 가입된 클러스터에 연결하려면 [IntelliJ를 사용하여 도메인에 가입된 클러스터에 연결](../spark/apache-spark-intellij-tool-plugin.md#linkcluster)을 참조하세요.
* Eclipse를 사용하여 도메인에 가입된 클러스터에 연결하려면 [Eclipse를 사용하여 도메인에 가입된 클러스터에 연결](../spark/apache-spark-eclipse-tool-plugin.md#linkcluster)을 참조하세요.
