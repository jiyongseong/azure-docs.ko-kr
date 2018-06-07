---
title: "BizTalk 서비스에 BizTalk 서버 EDI 솔루션 마이그레이션 기술 가이드 | Microsoft Docs"
description: "MABS(Microsoft Azure BizTalk 서비스)에 EDI를 마이그레이션 "
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>BizTalk 서비스에 BizTalk Server EDI 솔루션 마이그레이션: 기술 가이드

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

작성자: Tim Wieman 및 Nitin Mehrotra

검토자: Karthik Bharthy

Microsoft Azure BizTalk 서비스를 사용하여 작성 – 2014년 2월 릴리스.

## <a name="introduction"></a>소개
EDI (전자 데이터 교환)은 기업간에 전자적으로 데이터를 교환하는 가장 널리 퍼진 방법 중 하나이며 기업간 또는 B2B 트랜잭션이라는 용어로도 사용됩니다. BizTalk Server은 BizTalk Sever의 최초 릴리스 때부터 수 십 년 동안 EDI 지원을 하였습니다. BizTalk 서비스를 사용하여 Microsoft는 Microsoft Azure Platform에서 EDI 솔루션을 계속지원합니다. B2B 트랜잭션은 대부분 조직 외부에 대한 것이며, 따라서 클라우드 플랫폼에서 구현된 경우 보다 쉽게 구현할 수 있습니다. Microsoft Azure는 BizTalk 서비스를 통해 이 기능을 제공합니다.

일부 고객은 BizTalk 서비스를 새로운 EDI 솔루션을 위한 "최적의" 플랫폼으로 보지만, 많은 고객은 현재 BizTalk Server EDI 솔루션을 Azure에 마이그레이션하기를 원합니다. BizTalk 서비스 EDI는 BizTalk Server EDI 아키텍처(거래 파트너, 엔터티, 규약)와 동일한 주요 엔터티에 기반을 둔 아키텍처로 설계되었으며, BizTalk Server EDI 아키텍처를 BizTalk 서비스로 마이그레이션할 수 있습니다.

이 문서에서는 BizTalk Server EDI를 BizTalk 서비스에 마이그레이션 하는 데 관련된 차이점 중 일부에 대해 설명합니다. 이 문서에서는 BizTalk Server EDI 처리 및 거래 파트너 규약에 대한 실무 지식이 있다고 가정 합니다. BizTalk Server EDI에 대한 자세한 내용은 [BizTalk Server를 사용하여 거래 파트너 관리](https://msdn.microsoft.com/library/bb259970.aspx)를 참조하세요.

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>BizTalk 서비스에 어떤 버전의 BizTalk Server EDI 아티팩트를 마이그레이션할 수 있나요?
파트너, 프로필 및 규약을 포함하도록 리모델링 되었을 때 BizTalk Server EDI 모듈이 BizTalk Server 2010에 대해 크게 향상 되었습니다. BizTalk 서비스는 거래 파트너와 해당 거래 파트너 내 사업부를 구성하는 데 동일한 모델을 사용합니다. 결과적으로, BizTalk Server 2010 이상 버전의 EDI 아티팩트를 BizTalk 서비스에 마이그레이션하는 것이 훨씬 더 수월해졌습니다. BizTalk Server 2010 이전 버전에 관련된 EDI 아티팩트를 마이그레이션하려면 먼저 BizTalk Server 2010으로 업그레이드한 다음 BizTalk 서비스에 EDI 아티팩트를 마이그레이션합니다.

## <a name="scenariosmessage-flow"></a>시나리오/메시지 흐름
BizTalk Server와 함께 BizTalk Server의 EDI 처리는 TPM(거래 파트너 관리) 솔루션 주위에 설치됩니다. TPM 솔루션에는 다음의 주요 구성 요소가 있습니다.

* 거래 파트너는 B2B 트랜잭션의 조직을 나타냅니다.
* 프로필은 거래 파트너 내의 부서를 나타냅니다.
* 거래 파트너 규약 (또는 규약들)은 두 파트너/프로필 간의 비즈니스 규약을 나타냅니다.

다음 그림은 BizTalk Server EDI 솔루션 및 BizTalk 서비스 EDI 솔루션 간의 유사점뿐만 아니라 차이점을 보여줍니다.

![][EDImessageflow]

BizTalk Server 및 BizTalk 서비스의 EDI 솔루션 흐름 차이점과 유사점은 다음과 같습니다.

* BizTalk Server가 EDI 메시지를 수신을 위해 EDIReceive 파이프라인을 사용하고 EDI 메시지를 전송하기 위해 EDISend 파이프라인을 사용하는 것처럼, BizTalk 서비스는 수신을 위해  EDI Receive 브리지를 사용하여 EDI 메시지를 전송하기 위해 EDI Send 브리지를 사용합니다. BizTalk Server에서 파이프라인은 포트 송신 및 수신을 사용하여 규약과 연결됩니다. BizTalk 서비스에서 규약은 자체적으로 브리지 를 송신 또는 수신을 사용합니다.
* BizTalk Server에서는 EDIReceive 파이프라인이 EDI 메시지를 처리한 후 메시지는 SQL Server 데이터베이스에 덤프됩니다. 이 때 EdiSend 파이프라인은 SQL Server 데이터베이스에서 나오는 메시지를 선택하고 처리한 다음 거래 파트너에게 보냅니다.
  
    BizTalk 서비스에서는 EDI 수신 브리지가 EDI 메시지를 처리한 후, 외부 프로세스에 메시지를 라우팅합니다. 외부 프로세스는 Microsoft Azure 또는 온-프레미스에서 실행 중일 수 있습니다. 외부 프로세스는 EDI 송신 브리지에 메시지를 라우팅해야 합니다. 송신 브리지는 메시지를 기본적으로 끌어오지 않습니다. 메시지를 처리 한 후 EDI 송신 브리지는 거래 파트너에게 메시지를 라우팅합니다.

BizTalk 서비스는 Microsoft Azure 계산 인스턴스(웹 또는 작업자 역할), Microsoft Azure SQL 데이터베이스 또는 Microsoft Azure 저장소 계정을 구성하지 않고 거래 파트너 간에 B2B 규약을 신속하게 만들고 배포하는 사용이 쉬운 구성 환경을 제공합니다. 거래 파트너 규약의 "가장자리”, 즉 거래 파트너 규약 EDI 브리지 처리 이전 또는 이후의 워크플로 또는 기타 서비스 처리를 시도하는 경우 보다 복잡한 시나리오가 필요합니다. 세부적으로 BizTalk 서비스에서 EDI 메시지를 처리 하는 동안 다음과 같은 이벤트의 시퀀스가 발생합니다.

1. EDI 메시지가 거래 파트너인 Fabrikam에서 수신됩니다.  거래 파트너로부터 EDI 메시지를 수신하는 경우 BizTalk 서비스는 FTP, SFTP, AS2 및 HTTP/S.와 같은 전송 프로토콜 지원합니다.
2. 거래 파트너 규약 수신측 처리는 EDI 메시지를 XML 형식으로 역어셈블합니다.  역어셈블된 EDI 메시지 (XML 형식)을 서비스 버스 릴레이 끝점, 서비스 버스 토픽, 서비스 버스 큐 또는 BizTalk 서비스 브리지와 같은 서비스 버스 끝점으로 라우팅할 수 있습니다.
3. 그런 다음 역어셈블된 XML 메시지는 추가 사용자 지정 처리를 위해 끝점에서 수신 할 수 있습니다.  이러한 끝점은 예를 들어 Windows WF (Workflow) 또는 Windows Communication Foundation (WCF) 서비스에서 해당 메시지를 추가 처리하기 위해 온-프레미스 구성 요소별 또는 Microsoft Azure 계산 인스턴스로 처리할 수 있습니다.
4. 이 때 거래 파트너 규약의 "송신측 처리"는 XML 메시지를 EDI 형식으로 어셈블하고 계약 거래 파트너인 Contoso로 보냅니다.  거래 파트너에 EDI 메시지를 전송하는 경우  BizTalk 서비스는 EDI 메시지를 수신하기 위해 사용되는 동일한 프로토콜을 지원합니다.

이 문서는 BizTalk 서비스에 다른 BizTalk Server EDI 아티팩트 중 일부를 마이그레이션하는 데 대한 개념적 지침을 제공합니다.

## <a name="sendreceive-ports-to-trading-partners"></a>거래 파트너에 대한 송신/수신 포트
BizTalk Server에서 사용자는 거래 파트너에서 EDI/XML 메시지를 수신하는 수신 위치 및 수신 포트를 설정하고 거래 파트너에게 EDI/XML 메시지를 보낼 송신 포트를 설정합니다. 그런 다음 BizTalk Server 관리 콘솔을 사용하 여 이 포트를 거래 파트너 규약에 연결합니다. BizTalk 서비스에서 거래 파트너로부터 메시지를 수신하고 거래 파트너에 메시지를 전송하는 는 위치는 BizTalk 서비스 포털에서  거래 파트너 규약 자체의 일부(전송 설정의 일부)로 구성됩니다.  따라서 BizTalk 서비스에 그 자체로는 "송신 포트" 및 "수신 위치"의 개념이 실제로는 없습니다. 자세한 내용은 [규약 만들기](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx)를 참조하세요.

## <a name="pipelines-bridges"></a>파이프라인 (브리지)
BizTalk Server EDI에서 파이프라인은 응용 프로그램에서 필요에 따라 특정 처리 능력에 대한 사용자 지정 논리를 포함할 수 있는 메시지 처리 엔터티입니다. BizTalk 서비스의 경우, 해당하는 서비스는 EDI 브리지일 것입니다. 그러나 지금은 BizTalk 서비스에서 EDI 브리지는 "닫혀" 있습니다.  즉, 사용자 고유의 사용자 지정 활동을 EDI 브리지에 추가할 수 없습니다. 모든 사용자 지정 처리는 메시지를 거래 파트너 규약의 일부로 구성된 브리지에 입력하기 이전 또는 이후에 응용 프로그램에서 EDI 브리지 외부로 수행해야 합니다. EAI 브리지에는 사용자 지정 처리를 수행하는 옵션이 제공 됩니다. 사용자 지정 처리를 원하는 경우, 메시지를 EAI 브리지로 처리하기 이전 또는 이후에 EAI 브리지를 사용할 수 있습니다. 자세한 내용은 [브리지에 사용자 지정 코드를 포함하는 방법](https://msdn.microsoft.com/library/azure/dn232389.aspx)을 참조하세요.

사용자 지정 코드 및/또는 거래 업체 규약이 메시지를 수신하기 전 또는 규약이 메시지를 처리하고 서비스 버스 끝점으로 라우팅 후 메시징 큐 및 항목 서비스 버스를 사용하여 게시/구독 흐름을 삽입할 수 있습니다.

메시지 흐름 패턴에 대해서는 이 토픽의 **시나리오/메시지 흐름** 을 참조하세요.

## <a name="agreements"></a>규약
EDI 처리에 사용되는 BizTalk Server 2010 거래 파트너 규약에 익숙한 경우 BizTalk 서비스 거래 파트너 규약이 친숙해 보일 것입니다. 대부분의 규약 설정은 동일하고 동일한 용어를 사용합니다. 일부 경우, 계약 설정은 BizTalk Server에서 동일한 설정에 비해 훨씬 간단합니다. Microsoft Azure BizTalk 서비스는 x12, EDIFACT 및 AS2 전송을 지원합니다.

또한 Microsoft Azure BizTalk 서비스는 BizTalk Server 거래 파트너 모듈에서 BizTalk 서비스 포털로 거래 파트너 규약을 마이그레이션하는 **TPM 데이터 마이그레이션** 도구를 제공합니다. TPM 데이터 마이그레이션 도구는 도구 패키지의 일부로 사용 가능하며, [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057)에서 다운로드할 수 있습니다. 패키지에는 도구를 사용하는 방법을 제공하는 추가 정보와 도구에 대한 문제해결 정보가 포함됩니다.

## <a name="schemas"></a>스키마
BizTalk 서비스는 BizTalk 서비스 솔루션에서 사용할 수 있는 EDI 스키마를 제공합니다.  또한 EDI 스키마의 루트 노드는 BizTalk 서버와 BizTalk 서비스에서 동일하기 때문에 BizTalk Server EDI 스키마는 BizTalk 서비스와 함께 사용할 수 있습니다. 따라서 BizTalk Server EDI 스키마를 직접 가져와서 BizTalk 서비스를 사용하여 개발하는 EDI 솔루션에서 사용할 수 있습니다. [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057)에서 스키마를 다운로드할 수 있습니다.

## <a name="maps-transforms"></a>매핑(변환)
BizTalk Server의 매핑은 BizTalk 서비스의 변환이라고 합니다. BizTalk Server에서 BizTalk 서비스로 매핑을 마이그레이션하는 것은 (맵 복잡성에 따라) 달성하기 위해 더 복잡한 작업 중 하나일 수 있습니다. BizTalk 서비스에 사용되는 매핑 도구는 BizTalk 매퍼와 다릅니다. 매퍼는 대부분 유사해 보이지만 기본 매핑 형식에는 차이가 있습니다. 사용자에 대해 가능한 펑토이드 (BizTalk 서비스에서는 **매핑 작업** 이라고 함)도 다릅니다.  실제로 BizTalk 서비스에서 직접 BizTalk 매핑을 사용할 수 없습니다. 또한 BizTalk Server에서 사용할 수 있는 모든 펑토이드를 BizTalk 서비스에 대한 매핑 작업으로 사용할 수 있는 것은 아닙니다.

### <a name="new-transform-operations"></a>새로운 변환 작업
사용 가능한 변환 매핑 작업 목록이 BizTalk Server 매퍼와 매우 다르게 보일 수 있지만, BizTalk 서비스 변환에는 동일한 작업을 수행하는 새로운 방법이 있습니다. 예를들어, BizTalk 서비스 변환에서는 **목록 작업** 을 사용할 수 있습니다. 이것은 BizTalk 매퍼에서는 사용할 수 없습니다.  목록이 일련의 항목 (라고도 "행")인 경우와 각 항목에서 여러 멤버 ("열"이라고도 함)가 있을 수 있는 경우에 **목록 작업** 으로 사용자는 목록을 만들고 작업할 수 있습니다.  목록을 정렬하고, 조건에 따라 항목을 선택할 수 있습니다.

BizTalk 서비스 변환의 새로운 기능의 다른 예로는 **루프 작업**이 있습니다.  BizTalk Server 매퍼에서는 중첩 된 루프를 만들기 어렵습니다.  따라서 루프 매핑 작업이 BizTalk 서비스 변환에 대한 추가됩니다.

또 다른 예는 **If Then Else** 식 매핑 작업입니다.  if then else 작업은 BizTalk 매퍼에서 수행할 수 있지만 간단해 보이는 작업을 수행 하는 데 여러 펑토이드가 필요합니다.

### <a name="migrating-biztalk-server-maps"></a>BizTalk Server 맵 마이그레이션
Microsoft Azure BizTalk 서비스는  BizTalk 서비스 변환 BizTalk Server 맵을 마이그레이션하는 도구를 제공합니다. **BTMMigrationTool**은 [BizTalk 서비스 SDK 다운로드](http://go.microsoft.com/fwlink/p/?LinkId=235057)와 함께 제공되는 **도구** 패키지의 일부로 사용할 수 있습니다. 이 도구에 대한 자세한 내용은 [BizTalk 맵을 BizTalk 서비스 변환으로 변환](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx)을 참조하세요.

[BizTalk Server 맵을 BizTalk 서비스 변환으로 마이그레이션](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx)하는 방법에 대해서는 BizTalk MVP인 Sandro Pereira가 제공한 샘플을 살펴볼 수도 있습니다.

## <a name="orchestrations"></a>오케스트레이션
BizTalk Server 오케스트레이션 처리를 Microsoft Azure에 마이그레이션해야 하는 경우 Microsoft Azure는 BizTalk Server 오케스트레이션 실행을 지원하지 않으므로 오케스트레이션은 다시 작성해야 합니다.  오케스트레이션 기능은 Windows Workflow Foundation 4.0 (WF4) 서비스에서 다시 작성할 수 있습니다.  현재 BizTalk Server 오케스트레이션에서 WF4로 마이그레이션이 없으므로, 오케스트레이션 기능은 완전히 다시 작성해야 합니다. 다음은 Windows 워크플로에 대한 몇가지 리소스입니다.

* [*WCF 워크플로 서비스를 서비스 버스 큐 및 항목과 통합하는 방법*](https://msdn.microsoft.com/library/azure/hh709041.aspx) Paolo salvatori 작성. 
* 빌드 2011 컨퍼런스의 [*Windows Workflow Foundation 및 Azure로 앱을 빌드하기* 세션](http://go.microsoft.com/fwlink/p/?LinkId=237314).
* MSDN의 [*Windows Workflow Foundation 개발자 센터*](http://go.microsoft.com/fwlink/p/?LinkId=237315).
* MSDN의 [*WF4(Windows Workflow Foundation 4) 설명서*](https://msdn.microsoft.com/library/dd489441.aspx).

## <a name="other-considerations"></a>기타 고려 사항
BizTalk 서비스를 사용하는 동안 확인 해야 하는 몇가지 고려 사항은 다음과 같습니다.

### <a name="fallback-agreements"></a>대체(fallback) 계약
BizTalk Server EDI 처리에는 "대체 계약"의 개념이 있습니다.  BizTalk 서비스에는 지금까지 대체 규약 개념이 **없습니다** .  BizTalk Server에서 대체 계약을 사용하는 방법에 대한 자세한 내용은 BizTalk 설명서 항목인 [EDI 처리에서 계약의 역할](http://go.microsoft.com/fwlink/p/?LinkId=237317) 및 [전역 구성 또는 대체 규약 속성](https://msdn.microsoft.com/library/bb245981.aspx)을 참조하세요.

### <a name="routing-to-multiple-destinations"></a>여러 대상으로 라우팅
현재 상태에서 BizTalk 서비스 브리지는 게시-구동 모델을 사용하여 여러 대상에 메시지 라우팅을 지원하지 않습니다. 대신 BizTalk 서비스 브리지에서 둘 이상의 끝점에서 메시지를 수신하는 여러 구독을 가질 수 있는 서비스 버스 토픽에 메시지를 라우팅할 수 있습니다.

## <a name="conclusion"></a>결론
Microsoft Azure BizTalk 서비스는 많은 기능을 추가하기 위해 중요 시점에 정기적으로 업데이트됩니다. 각 업데이트를 통해 BizTalk 서비스 및 다른 Azure 기술을 사용하여 종단간 솔루션을 만드는 것을 쉽게 만들기 위해 향상된 기능을 지원합니다.

## <a name="see-also"></a>참고 항목
[Azure로 엔터프라이즈 응용 프로그램 개발](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
