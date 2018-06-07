---
title: "BizTalk Services 버전의 기능 알아보기 | Microsoft 문서"
description: "BizTalk 서비스 버전(Developer, Basic, Standard 및 Premium)의 기능을 비교합니다. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 718b57a801a9ba62a0154ae42da2ac0c0741f203
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-editions-chart"></a>BizTalk 서비스: 버전 차트

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk 서비스는 여러 버전을 제공합니다. 이 문서를 사용하여 시나리오 및 비즈니스 요구 사항에 적합한 버전을 확인할 수 있습니다.

## <a name="compare-the-editions"></a>버전 비교
**Free(Preview)**

하이브리드 연결을 만들고 관리하는 기능이 포함되어 있습니다. 하이브리드 연결은 Azure 웹 사이트를 SQL Server와 같은 온-프레미스 시스템에 연결하는 간편한 방법입니다.

**Developer**

하이브리드 연결, 사용하기 쉬운 거래 업체 관리 포털을 통한 EAI 및 EDI 메시지 처리, X12와 AS2를 통한 다양한 EDI 처리 및 공통 EDI 스키마 지원 등의 기능이 포함되어 있습니다. 메시지를 읽고 쓰기 위해 HTTP/S, REST, FTP, WCF 및 SFTP 프로토콜로 클라우드에서 서비스를 연결하는 일반 EAI 시나리오를 만들 수 있습니다.  미리 만들어진 SAP, Oracle eBusiness, Oracle DB, Siebel 및 SQL Server 어댑터로 온-프레미스 LOB 시스템에 연결할 수 있습니다. Visual Studio 도구가 포함된 개발자 중심 환경을 사용하여 손쉽게 개발하고 배포할 수 있습니다. SLA(서비스 수준 계약)가 없는 개발 및 테스트 용도로만 제한됩니다.

**Basic**

대부분의 Developer 기능이 포함되어 있으며, 더 많은 하이브리드 연결, EAI 브리지, EDI 계약 및 BizTalk Adapter Pack 연결을 지원합니다. 또한 SLA(서비스 수준 계약)를 통해 확장할 수 있는 옵션 및 고가용성을 제공합니다.

**Standard**

모든 Basic 기능이 포함되어 있으며, 더 많은 하이브리드 연결, EAI 브리지, EDI 계약 및 BizTalk Adapter Pack 연결을 지원합니다. 또한 SLA(서비스 수준 계약)를 통해 확장할 수 있는 옵션 및 고가용성을 제공합니다.

**Premium**

모든 Standard 기능이 포함되어 있으며, 더 많은 하이브리드 연결, EAI 브리지, EDI 계약 및 BizTalk Adapter Pack 연결을 지원합니다. 또한 SLA(서비스 수준 계약)를 통해 확장할 수 있는 옵션, 보관 및 고가용성을 제공합니다.

## <a name="editions-chart"></a>버전 차트
다음 표에는 차이점이 나와 있습니다.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free(Preview)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>시작 가격</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk Services 가격 책정</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure 요금 계산기</a></td>
</tr>
<tr>
<td><strong>기본 최소 구성</strong></td>
<td>Free 장치 1개</td>
<td>Developer 장치 1개</td>
<td>Basic 장치 1개</td>
<td>Standard 장치 1개</td>
<td>Premium 장치 1개</td>
</tr>
<tr>
<td><strong>규모</strong></td>
<td>확장 안 함</td>
<td>확장 안 함</td>
<td>1 Basic 장치씩 확장함</td>
<td>1 표준 장치씩 확장함</td>
<td>1 프리미엄 장치씩 확장함</td>
</tr>
<tr>
<td><strong>최대 허용 규모 확장</strong></td>
<td>확장 안 함</td>
<td>확장 안 함</td>
<td>최대 8개</td>
<td>최대 8개</td>
<td>최대 8개</td>
</tr>
<tr>
<td><strong>장치당 EAI 브리지</strong></td>
<td>포함되지 않음</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
TPM 계약 포함</td>
<td>포함되지 않음</td>
<td>포함됨. 장치당 계약 10개</td>
<td>포함됨. 장치당 계약 50개</td>
<td>포함됨. 장치당 계약 250개</td>
<td>포함됨. 장치당 계약 1000개</td>
</tr>
<tr>
<td><strong>장치당 하이브리드 연결 수</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>장치당 하이브리드 연결 데이터 전송(GB)</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>온-프레미스 LOB 시스템에 BizTalk 어댑터 서비스 연결</strong></td>
<td>포함되지 않음</td>
<td>1개 연결</td>
<td>2개 연결</td>
<td>5개 연결</td>
<td>25개 연결</td>
</tr>
<tr>
<td align="left"><strong>지원되는 프로토콜/시스템:</strong>
<ul>
<li>http</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>SB(서비스 버스)</li>
<li>Azure Blob</li>
<li>REST API</li>
</ul>
</td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>고가용성</strong>
<br/><br/>
Service Level Agreement(서비스 수준 약정)는 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services 가격 책정</a>을 참조하세요.
</td>
<td>포함되지 않음</td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>백업 및 복원</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>추적</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>보관</strong><br/><br/>
추적 메시지 다운로드 및 NRR(수신 부인 방지) 포함</td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함되지 않음</td>
<td>포함되지 않음</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>사용자 지정 코드 사용</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>변환 사용(사용자 지정 XSLT 포함)</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
</table>

> [!NOTE]
> 하드웨어 오류에 대한 복원력을 위해 고가용성은 단일 BizTalk 장치 내에 반드시 VM을 여러 개 가집니다.
> 
> 

## <a name="faqs"></a>FAQ
#### <a name="what-is-a-biztalk-unit"></a>BizTalk 장치란 무엇인가요?
"장치"는 원자 수준의 Azure BizTalk 서비스 배포입니다. 각 버전에는 계산 용량 및 메모리가 다른 장치가 제공됩니다. 예를 들어 Basic 장치는 Developer 장치보다 계산 용량이 더 크고, Standard 장치는 Basic 장치보다 계산 용량이 더 크며 등등합니다. BizTalk 서비스를 확장할 때 장치로 확장합니다.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>BizTalk 서비스와 Azure BizTalk VM 간의 차이점은 무엇인가요?
BizTalk 서비스는 클라우드에서 통합 솔루션을 구축하는 데 진정한 PaaS(Platform-as-a-Service) 아키텍처를 제공합니다. PaaS 모델을 사용하여 응용 프로그램 논리에 완전히 초점을 맞추며 다음을 포함한 모든 인프라 관리는 Microsoft에 맡깁니다.

* 가상 컴퓨터를 관리하거나 패치할 필요가 없습니다.
* Microsoft에서 가용성을 보증합니다.
* Azure 포털을 통해 용량을 더 많거나 더 적게 간단히 요청하여 주문형 확장을 제어합니다.

Azure 가상 컴퓨터의 BizTalk Server는 IaaS(Infrastructure-as-a-Service) 아키텍처를 제공합니다. 가상 컴퓨터를 만들고 온-프레미스 환경과 똑같이 구성하여 코드를 변경하지 않고도 클라우드에서 기존 응용 프로그램을 더 쉽게 실행할 수 있습니다. IaaS를 사용하여 가상 컴퓨터를 구성하고 관리하고(예: 소프트웨어 설치 및 OS 패치) 응용 프로그램의 고가용성을 구성해야 합니다.

인프라 관리 노력을 최소화하는 새로운 통합 솔루션을 구축하려면 BizTalk 서비스를 사용하세요. 기존 BizTalk 솔루션을 마이그레이션하거나 BizTalk Server 응용 프로그램을 개발하고 테스트하기 위해 주문형 환경을 찾으려면 Azure 가상 컴퓨터에서 BizTalk Server를 사용하십시오.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>BizTalk 어댑터 서비스와 하이브리드 연결 간의 차이점은 무엇입니까?
BizTalk 어댑터 서비스는 Azure BizTalk 서비스에서 사용됩니다. BizTalk 어댑터 서비스에서는 BizTalk Adapter Pack을 사용하여 온-프레미스 LOB(기간 업무) 시스템에 연결합니다. 하이브리드 연결은 Azure 앱 서비스 및 Azure 모바일 서비스의 웹앱 기능과 같은 Azure 응용 프로그램을 온-프레미스 리소스에 연결할 수 있는 간단하고 편리한 방법을 제공합니다.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>"장치당 하이브리드 연결 데이터 전송(GB)"은 무엇을 의미하나요? 분/시간/일/주/월 단위입니까? 한도에 도달하면 어떻게 됩니까?
장치당 하이브리드 연결 비용은 BizTalk 서비스 버전에 따라 다릅니다. 즉, 전송하는 데이터 양에 따라 비용이 달라집니다. 예를 들어 매일 10GB 데이터를 전송하는 것이 매일 100GB를 전송하는 것보다 비용이 적게 듭니다. BizTalk 서비스용 [가격 계산기](https://azure.microsoft.com/pricing/calculator/?scenario=full) 를 사용하여 특정 비용을 확인할 수 있습니다. 일반적으로 제한은 일별로 적용됩니다. 한도를 초과하면 초과분에 대해 GB당 1달러의 요금이 청구됩니다.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>BizTalk 서비스에서 계약을 생성할 때 왜 브리지 수가 하나가 아닌 두 개씩 증가합니까?
각 계약은 두 개의 다른 브리지(송신 측 통신 브리지와 수신 측 통신 브리지)로 구성되어 있습니다.

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>브리지 또는 계약 개수가 할당량 한도를 초과하면 어떻게 되나요?
새 브리지를 배포하거나 새 계약을 만들 수 없습니다. 더 많이 배포하려면 BizTalk 서비스의 장치를 좀 더 강화하거나 더 높은 버전으로 업그레이드해야 합니다.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>BizTalk 서비스의 한 단계에서 어떻게 다른 단계로 마이그레이션합니까?
무료 버전은 마이그레이션하거나 다른 계층으로 '확장'할 수 없고 백업 및 다른 계층으로 복원할 수 없습니다. 다른 계층이 필요한 경우 새 계층을 사용하여 새 BizTalk 서비스를 만듭니다. 하이브리드 연결을 비롯한 무료 버전을 사용하여 만든 아티팩트는 새 BizTalk 서비스에서 다시 만들어야 합니다. 

나머지 버전의 경우 백업을 사용하고 아티팩트를 마이그레이션하기 위해 한 계층에서 다른 계층으로 복원합니다. 예를 들어 표준 계층에서 아티팩트를 백업한 다음 프리미엄 계층으로 복원합니다. [BizTalk 서비스: 백업 및 복원](biztalk-backup-restore.md) 은 지원되는 마이그레이션 경로를 설명하고 백업된 아티팩트를 나열합니다. 하이브리드 연결이 백업되지 않는다는 점에 유의합니다. 백업 및 새 계층으로 복원한 후에 하이브리드 연결을 다시 만듭니다.  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>이 서비스에 BizTalk 어댑터 서비스도 포함되어 있습니까? 소프트웨어를 어떻게 받습니까?
네. BizTalk 어댑터 서비스는 BizTalk Adapter Pack과 함께 Azure BizTalk Services SDK [다운로드](http://www.microsoft.com/download/details.aspx?id=39087)에 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계
Azure 포털에서 Azure BizTalk 서비스를 만들려면 [BizTalk 서비스: Azure 포털을 사용하여 프로비전](biztalk-provision-services.md)으로 이동하세요. 응용 프로그램을 만들려면 [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=235197)로 이동하십시오.

## <a name="additional-resources"></a>추가 리소스
* [BizTalk 서비스: Azure 포털을 사용하여 프로비전](biztalk-provision-services.md)<br/>
* [BizTalk Services: 프로비전 상태 차트](biztalk-service-state-chart.md)<br/>
* [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [BizTalk Services: Backup and restore](biztalk-backup-restore.md)<br/>
* [BizTalk 서비스: 제한](biztalk-throttling-thresholds.md)<br/>
* [BizTalk 서비스: 발급자 이름 및 발급자 키](biztalk-issuer-name-issuer-key.md)<br/>
* [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

