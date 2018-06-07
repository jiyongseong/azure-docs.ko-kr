---
title: "Microsoft Azure의 Oracle 솔루션 | Microsoft Docs"
description: "Microsoft Azure의 Oracle 솔루션에 대한 지원되는 구성 및 제한 사항을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: 1bc03d15096e7f1d4538d6642a61aaee9bb572f7
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure의 Oracle 솔루션 및 배포
이 문서에서는 Microsoft Azure에서 여러 Oracle 솔루션을 성공적으로 배포하는 데 필요한 정보를 알아봅니다. 이러한 솔루션은 Oracle이 Azure Marketplace에 게시한 가상 컴퓨터 이미지를 기반으로 합니다. 현재 사용할 수 있는 이미지 목록을 가져오려면 다음 명령을 실행합니다.
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
2017년 6월 16일 현재, 이미지 목록은 다음과 같습니다.
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

이러한 이미지는 "사용자 라이선스가 필요"한 것으로 간주되며, 따라서 VM을 실행하는 데 발생한 계산, 저장소 및 네트워킹 비용만 청구됩니다.  Oracle 소프트웨어를 사용하는 데 적절한 라이선스가 있으며 현재 Oracle과의 지원 계약이 체결되어 있는 것으로 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. 라이선스 이동에 대한 자세한 내용은 게시된 [Oracle 및 Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 참고를 참조하세요. 

또한 개인은 솔루션이 Azure에서 처음부터 만들거나 온-프레미스 환경에서 업로드한 사용자 지정 이미지를 기반으로 하도록 선택할 수도 있습니다.

## <a name="support-for-jd-edwards"></a>JD Edwards 지원
Oracle 지원 참고 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)에 따라 JD Edwards EnterpriseOne 버전 9.2 이상은 특정 MTR(`Minimum Technical Requirements`)을 준수하는 **모든 공용 클라우드 제품**에서 지원됩니다.  OS 및 소프트웨어 응용 프로그램 호환성에 대한 해당 MTR 사양을 충족하는 사용자 지정 이미지를 만들어야 합니다. 

## <a name="oracle-database-virtual-machine-images"></a>Oracle Database 가상 컴퓨터 이미지
Oracle은 Oracle Linux 기반 가상 컴퓨터 이미지의 Azure에서 Oracle DB 12.1 Standard 및 Enterprise Edition을 실행하도록 지원합니다.  Azure에서 Oracle DB의 프로덕션 워크로드에 대한 최상의 성능을 위해 VM 이미지 크기를 적절히 조정하고 Premium Storage에서 지원되는 관리 디스크를 사용해야 합니다. Oracle에서 게시한 VM 이미지를 사용하여 Azure에서 Oracle DB를 신속하게 실행하는 방법에 대한 지침은 [Oracle DB 빠른 시작 연습](oracle-database-quick-create.md)을 참조하세요.

### <a name="attached-disk-configuration-options"></a>연결된 디스크 구성 옵션

연결된 디스크는 Azure Blob 저장소 서비스에 의존합니다. 각 표준 디스크는 이론적으로 최대 초당 500개의 입력/출력 작업(IOPS)을 할 수 있습니다. 프리미엄 디스크 제품은 고성능 데이터베이스 워크로드에 적합하며 디스크당 최대 5000개의 IOPS를 제공할 수 있습니다. 이러한 성능 요구를 충족하는 경우에는 단일 디스크를 사용할 수 있지만, 여러 개의 연결된 디스크를 사용하여 데이터베이스를 분산시킨 후, Oracle 자동 저장소 관리(ASM)를 사용하면 효과적으로 IOPS 성능 향상을 개선할 수 있습니다. Oracle ASM 관련 정보는 [Oracle 자동 저장소 개요](http://www.oracle.com/technetwork/database/index-100339.html)를 참조하세요. Linux Azure VM에서 Oracle ASM을 설치 및 구성하는 방법에 대한 예제는 [Oracle Automated Storage Management 설치 및 구성](configure-oracle-asm.md) 자습서를 참조하세요.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle RAC(Oracle Real Application Cluster)
Oracle RAC는 온-프레미스 다중 노드 클러스터 구성에서 단일 노드의 오류를 완화하도록 고안되었습니다. 이 기능은 하이퍼스케일(hyper-scale) 공용 클라우드 환경에 네이티브가 아닌 두 가지 온-프레미스 기술인 네트워크 멀티캐스트 및 공유 디스크에 의존합니다. 데이터베이스 솔루션이 Azure에서 Oracle RAC를 필요로 하는 경우 이러한 기술을 사용하려면 타사 소프트웨어가 필요합니다.  FlashGrid Inc.에서 게시한 [FlashGrid Node for Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview)라고 하는 **Microsoft Azure Certified** 제품은 Azure Marketplace에서 사용할 수 있습니다. 이 솔루션에 대한 자세한 내용과 Azure에서의 작동 방법은 [FlashGrid 솔루션 페이지](https://www.flashgrid.io/oracle-rac-in-azure/)를 참조하세요.

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항
Azure에서 Oracle 데이터베이스를 사용할 때, 사용자는 모든 가동 중지 시간을 방지하기 위해 높은 가용성과 재해 복구 솔루션을 구현하는 일을 담당합니다. 

Oracle Database Enterprise Edition의 고가용성 및 재해 복구는 Azure에서 [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) 또는 [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate)를 사용하여 두 개의 개별 가상 머신에 있는 두 개의 데이터베이스로 구현할 수 있습니다. 두 가상 컴퓨터는 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 동일한 [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에 있어야 합니다.  또한 Azure가 개별 장애 도메인 및 업그레이드 도메인에 가상 컴퓨터를 배치할 수 있도록 동일한 가용성 집합에 VM을 배치하는 것이 좋습니다.  지리적 중복을 포함하려는 경우 다른 두 지역 간에 이 두 데이터베이스를 복제하고 VPN Gateway로 두 인스턴스를 연결할 수 있습니다.

Azure에서 이를 평가해 볼 수 있도록 기본 설치 절차를 안내하는 "[Azure에서 Oracle DataGuard 구현](configure-oracle-dataguard.md)" 자습서가 있습니다.  

Oracle 데이터 가드로, 가상 컴퓨터에서 주 데이터베이스, 또 다른 가상 컴퓨터에서 보조(대기) 데이터 베이스에서 고가용성을 얻을 수 있으며, 양 데이터베이스 간에 단방향 복제를 설정할 수 있습니다. 결과는 해당 데이터 베이스의 복사본에 대한 읽기 엑세스입니다. Oracle 골든 게이트로, 두 개의 데이터베이스 간의 양방향 복제를 구성할 수 있습니다. 이러한 도구를 사용하여 데이터베이스에 대한 고가용성 솔루션을 설정하는 방법을 알아보려면 Oracle 웹 사이트에서 [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) 및 [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 설명서를 참조하세요. 데이터베이스의 복사본에 대한 읽기-쓰기 권한이 필요한 경우 [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)를 사용할 수 있습니다.

Azure에서 이를 평가해 볼 수 있도록 기본 설치 절차를 안내하는 "[Azure에서 Oracle GoldenGate 구현](configure-oracle-golden-gate.md)" 자습서가 있습니다.

Azure에 HA 및 DR 솔루션이 설계되어 있음에도 불구하고 백업 전략을 사용하여 데이터베이스를 복원할 수 있습니다.  일관된 백업 설정에 대한 기본 절차를 안내하는 [Oracle 데이터베이스 Backup 및 복구](oracle-backup-recovery.md) 자습서가 있습니다.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 가상 컴퓨터 이미지
* **클러스터링은 Enterprise Edition에서만 지원됩니다.** WebLogic Server Enterprise Edition을 사용할 때만 WebLogic 클러스터링을 사용하도록 허가됩니다. WebLogic Server Standard Edition으로 클러스터링을 사용하지 마십시오.
* **UDP 멀티 캐스트는 지원되지 않습니다.** Azure는 UDP 유니캐스트를 지원하지만 멀티 캐스팅 및 브로드캐스팅은 지원하지 않습니다. WebLogic 서버는 Azure UDP 유니캐스 기능에 의존할 수 있습니다. UDP 유니캐스트에 의존하는 최상의 결과를 위해, WebLogic 클러스터 크기를 정적으로 유지하거나 클러스터에 포함된 서버는 10개 이하로 유지하는 것이 좋습니다.
* **WebLogic Server는 T3 액세스(예: Enterprise JavaBeans를 사용할 때)에 대해 공용 및 개인 포트가 동일한 것으로 예상합니다.** **SLWLS**라는 vNet에 있는, 둘 이상의 VM으로 구성된 WebLogic Server 클러스터에서 서비스 계층(EJB) 응용 프로그램이 실행되는 다중 계층 시나리오를 고려해 보세요. 동일한 vNet의 다른 서브넷에 있는 클라이언트 계층은 서비스 계층에서 EJB 호출을 시도하는 간단한 Java 프로그램을 실행합니다. 서비스 계층을 부하 분산하는 작업이 필요하므로, 부하 분산된 공용 끝점은 WebLogic Server 클러스터에 있는 Virtual Machines에 대해 생성될 필요가 있습니다. 지정한 개인 포트가 공용 포트와 다른 경우(예: 7006:7008), 다음과 같은 오류가 발생합니다.

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   이는 모든 원격 T3 액세스에 대해, WebLogic Server가  부하 분산 장치 포트 및 WebLogic 관리 서버가 동일한 것으로 간주하기 때문입니다. 앞의 경우 클라이언트는 포트 7006(부하 분산 장치 포트)에 액세스하고 관리되는 서버는 7008(개인 포트)에서 수신 대기합니다. 이 제한은 T3 액세스에만 적용가능하며 HTTP에는 적용할 수 없습니다.

   이 문제를 방지하려면, 다음 해결 방법중 하나를 사용합니다.

  * T3 액세스 전용 부하 분산 끝점에 대해 동일한 개인 및 공용 포트 번호를 사용합니다.
  * WebLogic Server를 시작하는 경우 다음 JVM 매개변수를 포함합니다.

         -Dweblogic.rjvm.enableprotocolswitch=true

관련 정보는 <http://support.oracle.com>에서 KB 문서 **860340.1**을 참조하세요.

* **동적 클러스터링 및 부하 분산 제한** WebLogic 서버에서 동적 클러스터를 사용하고 Azure에서 단일, 공용 부하 분산 끝점을 통해 노출한다고 가정해 보세요. 이 작업은 각 관리 서버(범위에서 동적으로 할당되지 않은)에 대해 고정된 포트 번호를 사용하고 관리자가 추적하는 서버(가상 컴퓨터 하나당 하나 이상의 관리 서버를 둘 수 없습니다)보다 더 많은 관리 서버를 시작하지 않는 한 수행될 수 있습니다. 사용자의 구성이 가상 머신 개수 보다 많은 WebLogic 서버가 시작되고 있는 결과를 낳게 되면(즉, 여러 개의 WebLogic server의 인스턴스가 동일한 가상 머신을 공유), 주어진 포트 번호에 바인딩하는 인스턴스 서버는 WebLogic 중 하나 이상이 될 수 없으므로, 해당 가상 머신에서 나머지는 실패합니다.

   관리되는 서버에 고유한 포트 번호를 할당하도록 관리 서버를 구성하면 이 구성에 필요한 것처럼 Azure가 단일 공용 포트에서 다수의 개인 포트로 매핑을 지원하지 않기 때문에 부하 분산이 불가능합니다.
* **가상 컴퓨터에서 Weblogic 서버의 여러 인스턴스입니다.** 배포 요구사항에 따라 가상 컴퓨터이 충분히 큰 경우, 동일한 가상 컴퓨터 내에서 WebLogic Server의 다중 인스턴스를 실행하는 옵션을 고려해볼 수 있습니다. 예를 들면 두 개의 코어를 갖고 있는 중간 크기의 가상 컴퓨터에서 WebLogic Server의 두 개의 인스턴스 실행을 선택할 수 있습니다. 주의: 그러나 하나가 망가지면 전체가 망가지는 오류를 막는 것이 여전히 권장됩니다. 이러한 사례로서, 단지 하나의 가상 컴퓨터를 사용하면서 WebLogic Server의 여러 인스턴스를 실행하고 있는 경우를 들 수 있습니다. 두 개의 가상 컴퓨터를 사용하는 것이 더 나은 접근법이며, 각 가상 컴퓨터는 여러 WebLogic Server의 인스턴스를 실행할 수 있습니다. 각 WebLogic Sever의 인스턴스는 여전히 동일한 클러스터의 일부입니다. 주의: 그러나 Azure 부하 분산 장치가 고유한 가상 컴퓨터들 간에 부하가 분산된 서버를 요구하므로, 동일한 가상 컴퓨터 내의 WebLogic 서버 배포로 노출되는 부하 분산 끝점을 로드하기 위해 Azure를 사용하는 것은 현재 불가능합니다. 

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 가상 컴퓨터 이미지
* **JDK 6 및7 최신 업데이트** Java의 최신 공용 지원 버전(현재 Java 8) 사용이 권장되지만, Azure 또한 JDK 6 및 7 이미지를 사용할 수 있게 합니다. 이는 JDK 8으로 아직 업그레이드 될 준비가 되지 않은 레거시 응용 프로그램을 위한 것입니다. 이전 JDK 이미지에 대한 업데이트가 일반 대중에게 더 이상 제공되지 않지만, Oracle과 Microsoft 사이의 파트너 관계로 인하여 Azure에 의해 제공되는 JDK 6 및 7 이미지들은 Oracle이 지원하는 고객중 오직 선택 그룹의 고객들을 위한 가장 최신의 비-공용 업데이트를 포함합니다. JDK 이미지의 최신 버전은 JDK 6 및 7의 업데이트된 릴리스 시간이 지난 후 제공될 것입니다.

   이 JDK 6 및 7 이미지들 내에서 JDK를 사용할 수 있으며, 파생된 가상 컴퓨터 및 이미지는 오직 Azure 내에서만 사용할 수 있습니다.
* **64비트 JDK.** Azure가 제공하는 Oracle WebLogic Sever 가상 컴퓨터 이미지 및 Oracle JDK 가상 컴퓨터 이미지는 64비트 버전 Windows Server와 JDK 모두를 포함합니다.

## <a name="next-steps"></a>다음 단계
이제 Microsoft Azure의 현재 Oracle 솔루션에 대한 개요를 살펴보았습니다. 다음 단계에서는 Azure에서 첫 번째 Oracle 데이터베이스를 배포합니다.
- [Azure에서 Oracle 데이터베이스 만들기](oracle-database-quick-create.md) 자습서로 시작합니다.