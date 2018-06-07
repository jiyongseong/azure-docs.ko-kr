---
title: Hadoop 구성 요소 및 버전 - Azure HDInsight | Microsoft Docs
description: HDInsight의 Hadoop 구성 요소 및 버전과 Hortonworks Data Platform의 이 클라우드 배포판에서 사용할 수 있는 서비스 수준에 대해 알아봅니다.
keywords: hadoop 버전, hadoop 에코시스템 구성 요소, hadoop 구성 요소, hadoop 버전을 확인하는 방법
services: hdinsight
editor: cgronlun
manager: asadk
author: kkampf
tags: azure-portal
documentationcenter: ''
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: kakampf
ms.openlocfilehash: 3d98ed2be7a040ad889d1d504f303569c265360b
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271594"
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight에서 사용할 수 있는 Hadoop 구성 요소 및 버전은 무엇인가요?

엔터프라이즈 보안 패키지뿐만 아니라 Microsoft Azure HDInsight의 Apache Hadoop 에코시스템 구성 요소 및 버전에 대해 알아봅니다. 또한 HDInsight에서 Hadoop 구성 요소 버전을 확인하는 방법을 알아봅니다. 

각 HDInsight 버전은 클라우드 배포판의 HDP(Hortonworks Data Platform) 버전입니다.

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>각 HDInsight 버전에서 제공되는 Hadoop 구성 요소
Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP 배포 및 배포에 포함된 구성 요소 집합이 만들어집니다. 2017년 2월 17일을 기준으로 Azure HDInsight에서 사용하는 기본 클러스터 버전은 3.5이지만 HDP 2.5에 기반합니다.

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 나열되어 있습니다. 

> [!NOTE]
> HDInsight 서비스의 기본 버전은 예고 없이 변경될 수 있습니다. 버전 종속성이 있는 경우 Azure PowerShell 및 Azure CLI를 사용하여 .NET SDK에서 클러스터를 만들 때 HDInsight 버전을 지정합니다.

| 구성 요소 | HDInsight 3.6(기본값) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop 및 YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive 및 HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0, 2.2.0 |1.6.2, 2.0 |1.6.0 |1.5.2 |1.3.1(Windows만 해당) |-|-|
| Apache Livy |0.4 |0.3 |0.3 |0.2 |-|-|-|
| Apache Kafka | 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>현재 Hadoop 구성 요소 버전 정보 확인

HDInsight 클러스터 버전과 연결된 Hadoop 에코시스템 구성 요소 버전은 HDInsight에 대한 업데이트로 변경될 수 있습니다. Hadoop 구성 요소를 확인하고 클러스터에 대해 사용 중인 버전을 확인하려면 Ambari REST API를 사용합니다. **GetComponentInformation** 명령은 서비스 구성 요소에 대한 정보를 검색합니다. 자세한 내용은 [Ambari 설명서][ambari-docs]를 참조하세요.

Windows 클러스터의 경우 구성 요소 버전을 확인하는 다른 방법은 원격 데스크톱을 사용하여 클러스터에 로그인하고 C:\apps\dist\ 디렉터리의 콘텐츠를 검사하는 것입니다.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight에서 Windows 사용 중지](#hdinsight-windows-retirement)를 참조하세요.

### <a name="release-notes"></a>릴리스 정보

최신 HDInsight 버전과 관련된 추가 릴리스 정보에 대해서는 [HDInsight 릴리스 정보](hdinsight-release-notes.md) 를 참조하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전
다음 표에서는 HDInsight 버전을 보여줍니다. 각 HDInsight 버전에 해당하는 HDP 버전은 제품 출시 날짜와 함께 나열되어 있습니다. 알려진 경우 지원 만료 날짜와 사용 중지 날짜도 나와 있습니다.

### <a name="available-versions"></a>사용 가능한 버전

다음 표에서는 PowerShell 및 .NET SDK와 같은 다른 배포 메서드를 비롯하여 Azure Portal에서 사용할 수 있는 HDInsight 버전을 나열합니다.

| HDInsight 버전 | HDP 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal에 대한 사용 가용성 | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |2017년 4월 4일 | | |예 |예 |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016년 9월 30일 |2017년 9월 5일 |2018년 6월 28일 |예 |예 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015년 12월 2일 |2016년 6월 27일 |2018년 7월 31일 |예 |아니오 |

> [!NOTE]
> 버전에 대한 지원이 만료되면 Microsoft Azure Portal을 통해 사용하지 못할 수도 있습니다. 그러나 클러스터 버전은 버전 사용 중지 날짜까지 Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) 명령의 `Version` 매개 변수 및 .NET SDK를 사용하여 계속 사용할 수 있습니다.
>

### <a name="retired-versions"></a>사용 중지된 버전

다음 표는 현재 Azure Portal에서 사용할 수 **없는** HDInsight 버전을 나열합니다.

| HDInsight 버전 | HDP 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal에 대한 사용 가용성 | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016년 3월 29일 |2016년 12월 29일 |2018년 1월 9일 |예 |아니오 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015년 12월 2일 |2016년 6월 27일 |2017년 7월 31일 |예 |아니오 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 또는 Windows Server 2012 R2 |2015년 2월 18일 |2016년 3월 1일 |2017년 4월 1일 |예 |아니오 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014년 6월 24일 |2015년 5월 18일 |2016년 6월 30일 |예 |아니오 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014년 2월 11일 |2014년 9월 17일 |2015년 6월 30일 |예 |아니오 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013년 10월 28일 |2014년 5월 12일 |2015년 5월 31일 |예 |아니오 |
| HDInsight 1.6 |HDP 1.1 | |2013년 10월 28일 |2014년 4월 26일 |2015년 5월 31일 |아니오 |아니오 |

> [!NOTE]
> 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 버전 2.1 이상에 배포됩니다. HDInsight 버전 1.6 클러스터에서는 사용할 수 없습니다.

## <a name="enterprise-security-package-for-hdinsight"></a>HDInsight용 엔터프라이즈 보안 패키지

엔터프라이즈 보안은 HDInsight 클러스터에서 클러스터 워크플로 생성의 일부로 추가할 수 있는 선택적 패키지입니다. 엔터프라이즈 보안 패키지는 다음을 지원합니다.

- 인증을 위한 Active Directory와 통합

    이전에 로컬 관리 사용자 및 로컬 SSH 사용자인 경우에만 HDInsight 클러스터를 만들 수 있었습니다. 로컬 관리 사용자는 모든 파일, 폴더, 테이블 및 열에 액세스할 수 있습니다.  엔터프라이즈 보안 패키지에서 HDInsight 클러스터를 고유한 Active Directory와 통합하여 역할 기반 액세스 제어를 사용할 수 있습니다. 여기에는 온-프레미스 Active Directory, Azure Active Directory Domain Services 또는 IaaS 가상 머신의 Active Directory가 포함됩니다. 클러스터의 도메인 관리자는 사용자가 클러스터에 액세스하도록 고유한 회사 (도메인) 사용자 이름 및 암호를 부여할 수 있습니다. 

    자세한 내용은 다음을 참조하세요.

    - [도메인 조인 HDInsight 클러스터 소개를 사용한 Hadoop 보안 소개](./domain-joined/apache-domain-joined-introduction.md)
    - [HDInsight에서 Azure 도메인에 가입된 Hadoop 클러스터 계획](./domain-joined/apache-domain-joined-architecture.md)
    - [도메인 가입 샌드박스 환경 구성](./domain-joined/apache-domain-joined-configure.md)
    - [Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- 데이터에 대한 권한 부여

    - Hive, Spark SQL 및 Yarn 큐의 권한 부여를 위해 Apache Ranger와 통합합니다.
    - 파일 및 폴더에 대한 액세스 제어를 설정할 수 있습니다.

    자세한 내용은 다음을 참조하세요.

    - [도메인에 가입된 HDInsight에서 Hive 정책 구성](./domain-joined/apache-domain-joined-run-hive.md)

- 감사 로그를 보고 액세스 및 구성된 정책을 모니터링합니다. 

### <a name="supported-cluster-types"></a>지원되는 클러스터 형식

현재 다음과 같은 클러스터 형식만이 엔터프라이즈 보안 패키지를 지원합니다.

- Hadoop(HDInsight 3.6에만 해당)
- Spark
- 대화형 쿼리

### <a name="support-for-azure-data-lake-store"></a>Azure Data Lake 저장소에 대한 지원

엔터프라이즈 보안 패키지는 Azure Data Lake Store를 기본 저장소 및 추가 기능 저장소 둘 다로 사용하도록 지원합니다.

### <a name="pricing-and-sla"></a>가격 및 SLA
엔터프라이즈 보안 패키지의 가격 책정 및 SLA에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="hdinsight-windows-retirement"></a>HDInsight Windows 사용 중지
Microsoft Azure HDInsight 버전 3.3은 Windows에 대한 HDInsight의 마지막 버전이었습니다. Windows HDInsight의 사용 중지 날짜는 2018년 7월 31일입니다. 또는 Windows 3.3 이전 버전의 HDInsight 클러스터가 설치된 경우 2018년 7월 31일 이전에 Linux HDInsight로 마이그레이션해야 합니다(HDInsight 버전 3.5 이상). Linux OS로 마이그레이션하면 HDInsight 클러스터를 만들거나 크기를 조정하는 기능을 유지할 수 있습니다. Windows의 HDInsight 버전 3.3에 대한 지원은 2016년 6월 27일에 만료되었습니다.

HDInsight 버전 3.4부터 Microsoft는 Linux OS에서만 HDInsight를 릴리스했습니다. 결과적으로 HDInsight 내의 구성 요소 중 일부가 Linux에서만 제공됩니다. 기본 파일 시스템으로 Apache Ranger, Kafka, 대화형 쿼리, Spark, HDInsight 응용 프로그램 및 Azure Data Lake Store가 포함됩니다. HDInsight의 이후 릴리스는 Linux OS에서만 사용할 수 있습니다. Windows HDInsight는 향후에 더 이상 릴리스되지 않습니다. 

## <a name="faqs"></a>FAQ

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Windows HDInsight의 사용 중지 타임라인은 어떻게 되나요?
Windows HDInsight의 사용 중지 날짜는 2018년 7월 31일입니다. 해당 지역의 계획된 사용 중지 날짜가 다른 경우 별도로 알림을 받습니다. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Windows HDInsight의 사용 중지는 기존 고객에게 어떤 영향을 주나요?
Windows HDInsight가 사용 중지된 후에 새로운 Windows HDInsight를 만들거나 기존 Windows HDInsight의 크기를 조정할 수 없습니다. Windows HDInsight 버전 3.3에 대한 지원은 2016년 6월 27일에 만료되었습니다. 따라서 HDInsight 3.3 이전 버전에 대한 지원이나 버그 수정은 없습니다. HDInsight의 이후 릴리스는 Linux OS에서만 사용할 수 있습니다. Windows HDInsight는 향후에 더 이상 릴리스되지 않습니다.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>영향을 받는 Windows HDInsight 버전은 무엇인가요?
Azure HDInsight 버전 3.3은 Windows용 HDInsight의 마지막 버전이었습니다. Windows HDInsight가 사용 중지되기 전에 모든 HDInsight Windows 클러스터 버전 3.3 이전은 Linux HDInsight 버전 3.5 이상으로 마이그레이션되어야 합니다. Linux HDInsight로 클러스터를 마이그레이션하면 새 클러스터를 만들거나 기존 클러스터 크기를 조정하는 기능을 유지할 수 있습니다. 

### <a name="what-do-i-need-to-do"></a>수행하려면 무엇이 필요한가요?
2018년 7월 31일 이전에 지원되는 HDInsight Linux 클러스터에 HDInsight Windows 클러스터를 마이그레이션합니다. [HDInsight 마이그레이션 문서](hdinsight-migrate-from-windows-to-linux.md)에서 자세히 알아봅니다. Azure HDInsight 버전에 대한 자세한 내용은 [지원되는 버전](hdinsight-component-versioning.md#supported-hdinsight-versions) 목록을 참조하세요. 

### <a name="where-do-i-find-the-cluster-os-type"></a>클러스터 OS 형식은 어디서 확인할 수 있나요?
Azure Portal에서 HDInsight 클러스터 개요 페이지로 이동하여 **Essentials** 아래에서 **클러스터 형식**을 찾습니다. 클러스터 OS 형식은 해당 페이지에 나열됩니다. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>2018년 7월 31일부터 HDInsight Linux 클러스터에 마이그레이션할 수 없습니다. HDInsight Windows 클러스터에 대한 영향은 무엇인가요?
HDInsight Windows 클러스터가 그대로 실행되지만 새로운 HDInsight Windows를 만들거나 기존 HDInsight Windows의 크기를 조정할 수 없습니다. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>내 클러스터에 .NET 종속성이 있습니다. Linux에서 이 종속성을 해결하려면 어떻게 하나요?
[Mono 프로젝트](http://www.mono-project.com/)를 사용하여 Linux 클러스터 종속성을 확인할 수 있습니다. 이 .NET의 오픈 소스 구현은 HDInsight Linux 클러스터에 사용할 수 있습니다. [HDInsight 마이그레이션 문서](hdinsight-migrate-from-windows-to-linux.md)에서 자세히 알아봅니다. 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>사용자가 Windows HDInsight에 새 고객입니다. HDInsight Windows 클러스터를 만들려면 어떻게 할까요?
2017년 7월 3일을 기준으로 기존 HDInsight Windows 고객만이 새로운 HDInsight Windows 클러스터를 만들 수 있습니다. 신규 고객은 PowerShell 또는 SDK를 사용하여 Azure Portal에서 HDInsight Windows 클러스터를 만들 수 없습니다. 신규 고객은 Linux HDInsight 클러스터를 만드는 것이 좋습니다. 기존 고객은 Windows HDInsight 사용 중지 날짜까지 새로운 HDInsight Windows 클러스터를 만들 수 있습니다. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Windows의 HDInsight에서 Linux의 HDInsight로 이동할 경우 가격 책정에 영향을 주나요?
아니요, 가격 책정은 두 OS의 HDInsight에 대해 동일합니다. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Linux에서 HDInsight를 사용하도록 변경한 고객에게 어떤 장점이 있나요?
* HDInsight 서비스를 통해 오픈 소스 빅 데이터 기술의 출시 시간 단축
* 지원을 위한 대규모 커뮤니티 및 에코시스템
* Hadoop 및 기타 빅 데이터 기술에 대한 오픈 소스 커뮤니티의 활발한 개발 기능

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Linux HDInsight는 Windows HDInsight에서 사용할 수 있는 기능 이외의 추가 기능을 제공하나요?
HDInsight 버전 3.4부터 Microsoft는 Linux OS에서만 HDInsight를 릴리스했습니다. 결과적으로 HDInsight 내의 구성 요소 중 일부가 Linux에서만 제공됩니다. 기본 파일 시스템으로 Apache Ranger, Kafka, 대화형 쿼리, Spark, HDInsight 응용 프로그램 및 Azure Data Lake Store가 포함됩니다. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 클러스터 버전의 서비스 수준 약정
SLA(서비스 수준 약정)는 _지원 기간_ 면에서 정의됩니다. 지원 기간은 Microsoft 고객 지원에서 HDInsight 클러스터 버전을 지원하는 기간을 나타냅니다. 버전의 _지원 만료 날짜_가 날짜를 지난 경우 HDInsight 클러스터는 지원 기간이 종료된 것입니다. 지원되는 버전에 대한 자세한 내용은 [지원되는 HDInsight 클러스터 버전](hdinsight-migrate-from-windows-to-linux.md) 목록을 참조하세요. 새로운 X+1 버전이 사용 가능해진 후에 지정된 HDInsight 버전의 지원 만료 날짜는 다음 수식 중 후자로 계산됩니다.  

* 수식 1: HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
* 수식 2: HDInsight 클러스터 버전 X+1이 Azure Portal에서 사용 가능한 날짜에 90일을 추가합니다.

_사용 중지 날짜_는 HDInsight에서 해당 클러스터 버전을 만들 수 없게 되는 날짜입니다. 2017년 7월 31일부터 사용 중지 날짜 이후에는 HDInsight 클러스터의 크기를 조정할 수 없습니다. 

> [!NOTE]
> HDInsight Windows 클러스터(버전 2.1, 3.0, 3.1, 3.2 및 3.3 포함)는 Azure 게스트 OS 제품군 버전 4에서 실행되고 64비트 버전의 Windows Server 2012 R2를 사용합니다. Azure 게스트 OS 제품군 버전 4는 .NET Framework 버전 4.0, 4.5, 4.5.1 및 4.5.2를 지원합니다.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>HDInsight 버전과 관련된 Hortonworks 릴리스 정보

섹션에서는 HDInsight에서 사용되는 Hortonworks Data Platform 배포 및 Apache 구성 요소의 릴리스 정보에 대한 링크를 제공합니다.
* HDInsight 클러스터 버전 3.6에서는 [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html)을 기반으로 하는 Hadoop 배포를 사용합니다.
* HDInsight 클러스터 버전 3.5에서는 [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html)를 기반으로 하는 Hadoop 배포를 사용합니다. HDInsight 클러스터 버전 3.5는 Azure Portal에서 만들어진 _기본_ Hadoop 클러스터입니다.
* HDInsight 클러스터 버전 3.4에서는 [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)를 기반으로 하는 Hadoop 배포를 사용합니다.
* HDInsight 클러스터 버전 3.3에서는 [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)(영문)를 기반으로 하는 Hadoop 배포를 사용합니다.

  * [Apache Storm 릴리스 정보](https://storm.apache.org/2015/11/05/storm0100-released.html)는 Apache 웹 사이트에서 사용할 수 있습니다.
  * [Apache Hive 릴리스 정보](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)는 Apache 웹 사이트에서 사용할 수 있습니다.
* HDInsight 클러스터 버전 3.2에서는 [Hortonworks Data Platform 2.2][hdp-2-2]를 기반으로 하는 Hadoop 배포를 사용합니다.

  * 특정 Apache 구성 요소에 대한 릴리스 정보는 다음과 같이 사용할 수 있습니다. [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight 클러스터 버전 3.1에서는 [Hortonworks Data Platform 2.1.7][hdp-2-1-7]를 기반으로 하는 Hadoop 배포를 사용합니다. 2014년 11월 7일 이전에 만들어진 HDInsight 3.1 클러스터는 [Hortonworks Data Platform 2.1.1][hdp-2-1-1]을 기반으로 합니다.
* HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0][hdp-2-0-8]을 기반으로 하는 Hadoop 배포를 사용합니다.
* HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3][hdp-1-3-0]을 기반으로 하는 Hadoop 배포를 사용합니다.
* HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1][hdp-1-1-0]을 기반으로 하는 Hadoop 배포를 사용합니다.






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>클러스터에 대한 기본 노드 구성 및 가상 머신 크기
다음 표에는 HDInsight 클러스터에 대한 기본 VM(가상 머신) 크기가 나와 있습니다.

> [!IMPORTANT]
> 클러스터에 필요한 작업자 노드 수가 32개를 초과하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
> 
> 

* 브라질 남부 및 일본 서부를 제외하고 지원되는 모든 지역:

  | 클러스터 유형 | Hadoop은 | HBase | 대화형 쿼리 | Storm | Spark | R 서버 |
  | --- | --- | --- | --- | --- | --- | --- |
  | 헤드: 기본 VM 크기 |D3 v2 |D3 v2 | D13, D14 |A3 |D12 v2 |D12 v2 |
  | 헤드: 권장되는 VM 크기 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | 작업자: 기본 VM 크기 |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | 작업자: 권장되는 VM 크기 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | Zookeeper: 기본 VM 크기 | |A3 | |A2 | | |
  | Zookeeper: 권장되는 VM 크기 | |A3, A4, A5 | | A2, A3, A4 | | |
  | Edge: 기본 VM 크기 | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: 권장되는 VM 크기 | | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* 브라질 남부 및 일본 서부만 해당(v2 크기 제외):

  | 클러스터 유형 | Hadoop은 | HBase | 대화형 쿼리 |Storm | Spark | R 서버 |
  | --- | --- | --- | --- | --- | --- | --- |
  | 헤드: 기본 VM 크기 |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | 헤드: 권장되는 VM 크기 |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | 작업자: 기본 VM 크기 |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | 작업자: 권장되는 VM 크기 |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | Zookeeper: 기본 VM 크기 | |A2 | | A2 | | |
  | Zookeeper: 권장되는 VM 크기 | |A2, A3, A4 | |A2, A3, A4 | | |
  | Edge: 기본 VM 크기 | | | | | |Windows: D12; Linux: D4 |
  | Edge: 권장되는 VM 크기 | | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> - 헤드는 Storm 클러스터 유형에 대한 *Nimbus*로 알려져 있습니다.
> - 작업자는 Storm 클러스터 형식에 대한 *감독자*로 알려져 있습니다.
> - 작업자는 HBase 클러스터 형식에 대한 *지역*으로 알려져 있습니다.

## <a name="next-steps"></a>다음 단계
- [HDInsight의 Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC에서 HDInsight의 Hadoop 작업](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
