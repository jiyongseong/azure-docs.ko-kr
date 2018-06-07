---
title: 도메인에 연결된 Azure HDInsight 아키텍처 | Microsoft Docs
description: 도메인에 가입된 HDInsight를 계획하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 20d6dbad6fa1914c8b12f47bb48f6efba3895887
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight에서 Azure 도메인에 가입된 Hadoop 클러스터 계획

표준 HDInsight 클러스터는 단일 사용자 클러스터입니다. 대량 데이터 워크로드를 구축하는 작은 응용 프로그램 팀이 있는 대부분의 회사에 적합합니다. Hadoop이 인기를 끌면서 대부분의 기업이 클러스터를 IT 팀에서 관리하고 여러 응용 프로그램 팀이 클러스터를 공유하는 모델로 이동하고 있습니다. 따라서 다중 사용자 클러스터가 관련된 기능은 Azure HDInsight에서 가장 많이 요청되는 기능입니다.

고유한 다중 사용자 인증 및 권한 부여를 구축하는 대신 HDInsight는 가장 인기 있는 ID 공급자인 AD(Active Directory)를 사용합니다. AD의 강력한 보안 기능을 사용하여 HDInsight의 다중 사용자 권한 부여를 관리할 수 있습니다. HDInsight를 AD와 통합하면 AD 자격 증명을 사용하여 클러스터와 통신할 수 있습니다. HDInsight의 VM은 AD에 도메인 가입되고 이는 HDInsight에서 실행 중인 모든 서비스(Ambari, Hive 서버, Ranger, Spark thrift 서버 등)가 인증된 사용자에 대해 원활하게 작동하도록 AD 사용자를 로컬 Hadoop 사용자에 매핑하는 방식입니다. 관리자는 Apache 레인저를 사용하여 HDInsight의 리소스에 대한 역할 기반 액세스 제어를 제공하는 강력한 권한 부여 정책을 만들 수 있습니다.


## <a name="integrate-hdinsight-with-active-directory"></a>Active Directory와 HDInsight 통합

Active Directory와 HDInsight를 통합하면 HDInsight 클러스터 노드는 AD 도메인에 가입됩니다. Kerberos 보안은 클러스터의 Hadoop 구성 요소에 대해 구성됩니다. 각 Hadoop 구성 요소의 경우 서비스 사용자는 Active Directory에서 생성됩니다. 또한 해당 컴퓨터 보안 주체는 도메인에 가입된 각 컴퓨터에 대해 만들어집니다. 이러한 서비스 사용자 및 컴퓨터 보안 주체는 Active Directory를 채울 수 있습니다. 결과적으로, 이러한 보안 주체가 위치하는 Active Directory 내에 OU(조직 구성 단위)를 제공해야 합니다. 

요약하면 다음으로 환경을 설정해야 합니다.

- LDAPS가 구성된 Active Directory 도메인 컨트롤러
- HDInsight의 Virtual Network에서 Active Directory 도메인 컨트롤러에 대한 연결
- Active Directory에서 만든 조직 구성 단위
- 다음에 대한 사용 권한을 가진 서비스 계정:

    - OU에 서비스 사용자를 만듭니다.
    - 컴퓨터를 도메인에 가입하고 OU에 컴퓨터 보안 주체를 만듭니다.

다음 스크린샷은 contoso.com에서 만든 OU를 보여 줍니다. 서비스 사용자 및 컴퓨터 보안 주체 중 일부도 스크린샷에 표시됩니다.

![도메인에 가입된 HDInsight 클러스터 OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)에서도 확인할 수 있습니다.

### <a name="the-way-of-bringing-your-own-active-directory-domain-controllers"></a>고유한 Active Directory 도메인 컨트롤러를 가져오는 방법

- **Azure Active Directory Domain Services**: 이 서비스는 Windows Server Active Directory와 완벽하게 호환되는 관리되는 Active Directory 도메인을 제공합니다. Microsoft에서 AD 도메인 관리, 패치 및 모니터링을 담당합니다. 도메인 컨트롤러를 유지 관리할 걱정 없이 클러스터를 배포할 수 있습니다. 사용자가 회사 자격 증명을 사용하여 클러스터에 로그인할 수 있도록 사용자, 그룹 및 암호가 Azure Active Directory에서 동기화됩니다. 자세한 내용은 [Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
* 도메인에 가입된 HDInsight 클러스터를 관리하려면 [도메인에 가입된 HDInsight 클러스터 관리](apache-domain-joined-manage.md)를 참조하세요.
* Hive 정책을 구성하고 Hive 쿼리를 실행하려면 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하려면 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
