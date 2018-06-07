---
title: Azure Analysis Services 개요 | Microsoft Docs
description: Azure에서 Analysis Services의 큰 그림을 가져옵니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: overview
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27b394a6b2d2f85afeb809fcad3b88564f3054e7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193818"
---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services란?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services는 클라우드에서 엔터프라이즈급 데이터 모델링을 제공합니다. Azure 데이터 플랫폼 서비스와 통합된 서비스로서 완벽하게 관리되는 플랫폼(PaaS)입니다. 

Analysis Services를 사용하면 여러 데이터 원본의 데이터를 매시업하고 결합하며 메트릭을 정의하며 단일한, 신뢰할 수 있는 유의적 데이터 모델에서 데이터를 보호할 수 있습니다. 데이터 모델은 사용자가 Power BI, Excel, Reporting Services, 써드파티 및 사용자 지정 앱과 같은 클라이언트 응용 프로그램을 통해 방대한 양의 데이터를 검색할 수 있는 보다 쉽고 빠른 방법을 제공합니다.

![데이터 원본](./media/analysis-services-overview/aas-overview-data-sources.png)

[이 비디오](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4)를 통해 Azure Analysis Services에서 전반적인 Microsoft BI 기능과 연동하는 방법과 데이터 모델을 클라우드로 가져옴으로써 얻을 수 있는 이점에 대해 알아보세요.

## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services에 구축
Azure Analysis Services는 이미 SQL Server Analysis Services Enterprise Edition에 있은 훌륭한 기능과 호환 가능하며, 1200 및 1400 [호환성 수준](analysis-services-compat-level.md)의 테이블 형식 모델을 지원합니다. 파티션, 행 수준 보안, 양방향 관계 및 변환을 모두 지원합니다. 메모리 내 및 DirectQuery 모드는 복잡한 대용량 데이터 집합에 대해 매우 빠른 쿼리를 제공합니다.

테이블 형식 모델은 신속한 개발을 제공하며 손쉽게 사용자 지정할 수 있습니다. 개발자의 경우 테이블 형식 모델에는 모델 개체를 설명하는 TOM(테이블 형식 개체 모델)이 포함됩니다. TOM은 JSON에서 [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 네임스페이스를 통해 [TMSL(테이블 형식 모델 스크립팅 언어)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) 및 AMO 데이터 정의 언어로 공개되어 있습니다.

## <a name="better-with-azure"></a>Azure를 통해 더욱 향상된 환경
Azure Analysis Services는 다양한 Azure Services와 통합되어 정교한 분석 솔루션을 빌드할 수 있습니다. [Azure Active Directory](../active-directory/active-directory-whatis.md)와 통합되어 중요한 데이터에 안전한 역할 기반 액세스를 제공합니다. 모델에 데이터를 로드하는 활동을 포함하여 [Azure Data Factory](../data-factory/introduction.md) 파이프라인에 통합합니다. [Azure Automation](../automation/automation-intro.md) 및 [Azure Functions](../azure-functions/functions-overview.md)는 사용자 지정 코드를 사용하여 모델의 간단한 오케스트레이션을 수행하는 데 사용할 수 있습니다.

## <a name="get-up-and-running-quickly"></a>빠른 준비 및 실행
Azure Portal에서는 수분 내에 [서버를 만들 수 있습니다](analysis-services-create-server.md). 그리고 Azure Resource Manager [템플릿](../azure-resource-manager/resource-manager-create-first-template.md)과 PowerShell을 사용하면 선언적 템플릿을 통해 서버를 프로비전할 수 있습니다. 단일 템플릿을 사용하면 저장소 계정 및 Azure Functions과 같은 다른 Azure 구성 요소와 함께 여러 서비스를 배포할 수 있습니다. 

서버를 만들었으면 Azure Portal에서 테이블 형식 모델을 바로 만들 수 있습니다. 새로운 [웹 디자이너](analysis-services-create-model-portal.md)(미리 보기) 기능을 사용하면 Azure SQL Database, Azure SQL Data Warehouse 데이터 원본에 연결하거나 .pbix Power BI Desktop 파일을 가져올 수 있습니다. 테이블 간의 관계를 자동으로 만들므로 브라우저에서 직접 측정값을 만들거나 json 형식의 model.bim 파일을 편집할 수 있습니다.

## <a name="scale-to-your-needs"></a>사용자의 요구 사항에 맞게 규모 조정

### <a name="the-right-tier-when-you-need-it"></a>필요할 때 올바른 계층

Azure Analysis Services는 개발자, 기본 및 표준 계층에서 사용할 수 있습니다. 각 계층 내에서 계획 비용은 처리 능력, CPU 및 메모리 크기에 따라 달라집니다. 서버를 만들 때 계층 내에서 계획을 선택합니다. 동일한 계층 내에서 계획을 위 또는 아래로 변경하거나 상위 계층으로 업그레이드할 수 있지만, 상위 계층에서 하위 계층으로 다운그레이드할 수는 없습니다.

서버를 위쪽, 아래쪽으로 이동하거나 일시 중지합니다. Azure Portal을 사용하거나 즉시로 완전히 제어하려면 PowerShell을 사용합니다. 사용한 양만큼만 요금을 지급합니다. 다양한 계획 및 계층에 대해 자세히 알아보고 가격 계산기를 사용하여 올바른 계획을 결정하려면 [Azure Analysis Services 가격](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요.

### <a name="scale-out-resources-for-fast-query-responses"></a>빠른 쿼리 응답에 대한 리소스 확장

Azure Analysis Services 확장으로 클라이언트 쿼리가 쿼리 풀의 여러 *쿼리 복제본*에 배포됩니다. 쿼리 복제본은 테이블 형식 모델의 복사본을 동기화했습니다. 쿼리 작업을 확산하여 높은 쿼리 작업 동안 응답 시간을 줄일 수 있습니다. 모델 처리 작업은 클라이언트 쿼리가 작업 처리에 의해 부정적인 영향을 받지 않도록 쿼리 풀에서 구분될 수 있습니다. 최대 7개의 추가 쿼리 복제본으로 쿼리 풀을 만들 수 있습니다(총 8개, 서버 포함). 

계층 변경과 마찬가지로 필요에 따라 쿼리 복제본을 확장할 수 있습니다. 포털에서 또는 REST API를 사용하여 확장을 구성합니다. 자세한 내용은 [Azure Analysis Services 확장](analysis-services-scale-out.md)을 참조하세요.

## <a name="keep-your-data-close"></a>데이터를 닫은 상태로 유지
Azure Analysis Services 서버를 만들 수 있는 [Azure 지역](https://azure.microsoft.com/regions/)은 다음과 같습니다.

| 아메리카 | 유럽 | 아시아 태평양 |
|----------|--------|--------------|
|  브라질 남부<br> 캐나다 중부<br> 미국 동부<br> 미국 동부 2<br> 미국 중북부<br> 미국 중부<br> 미국 중남부<br> 미국 중서부<br> 미국 서부<br> 미국 서부2 | 북유럽<br> 영국 남부<br> 서유럽 |   오스트레일리아 남동부<br> 일본 동부<br> 동남아시아<br> 인도 서부  |

새로운 영역이 항상 추가되고 있으므로 이 목록이 완전하지 않을 수도 있습니다. Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 서버를 만들 때 위치를 선택합니다. 최상의 성능을 얻으려면 가장 큰 사용자 근거지와 가장 가까운 위치를 선택합니다. 여러 지역의 중복 서버에 모델을 배포하여 [고가용성](analysis-services-bcdr.md)을 보장합니다.

## <a name="migrate-your-existing-tabular-models"></a>기존 테이블 형식 모델 마이그레이션
이미 온-프레미스 SQL Server Analysis Services 모델 솔루션이 있는 경우 중요한 변경 없이 Azure Analysis Services로 마이그레이션할 수 있습니다. 마이그레이션하려면 SSDT를 사용하여 서버에 모델을 배포할 수 있습니다. 또는 SSMS에서 백업 및 복원 또는 TMSL을 사용할 수 있습니다.

온-프레미스 데이터 원본이 있는 경우 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치하고 구성해야 합니다. 역할 및 역할 멤버가 이미 구성되어 있으면 경우 역할은 마이그레이션되지만 SSMS 또는 PowerShell을 사용하여 역할 멤버를 다시 추가해야 합니다.

## <a name="connect-to-popular-data-sources"></a>자주 사용하는 데이터 원본에 연결
Azure Analysis Services는 조직의 온-프레미스 및 클라우드의 [데이터 원본에 대한 연결](analysis-services-datasource.md)을 지원합니다. 하이브리드 솔루션을 위해 온-프레미스 및 클라우드 데이터 원본의 데이터를 모두 결합합니다. 

새로운 1400 테이블 형식 모델은 M 수식 쿼리 언어를 기반으로 하여 SSDT의 최신 데이터 가져오기 기능을 사용합니다. 데이터 가져오기를 사용하면 더 많은 데이터 변환 및 매시업 기능을 사용하고 고급 M 수식 언어 쿼리를 만들고 편집할 수 있습니다. 예를 들어 1400 테이블 형식 모델을 사용하면 Azure Blob Storage의 데이터 파일을 모델링할 수 있습니다.

## <a name="use-the-tools-you-already-know"></a>이미 알고 있는 도구 사용

![BI 개발자 도구](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>Visual Studio용 SSDT(SQL Server Data Tools)
[Visual Studio용 SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx) 평가판을 사용하여 모델을 개발하고 배포합니다. SSDT에는 빠르게 준비하고 실행할 수 있는 Analysis Services 프로젝트 템플릿이 포함되어 있습니다. 이제는 SSDT에 1400 테이블 형식 모델에 대한 최신 데이터 가져오기 데이터 원본 쿼리 및 매시업 기능도 포함되어 있습니다. Power BI Desktop 및 Excel 2016에서 데이터 가져오기에 익숙한 경우 고도로 사용자 지정된 데이터 원본 쿼리를 만드는 것이 얼마나 쉬운지 이미 알고 있습니다.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
[SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)를 사용하여 서버 및 모델 데이터베이스를 관리합니다. 클라우드에서 서버에 연결합니다. XMLA 쿼리 창에서 TMSL 스크립트를 직접 실행하고 TMSL 스크립트를 사용하여 작업을 자동화합니다. SSMS가 매월 업데이트되므로 새 특징과 기능이 빠르게 출시됩니다.

#### <a name="powershell"></a>PowerShell
서버 만들기, 서버 작업 일시 중단/다시 시작 또는 서비스 수준(계층) 변경과 같은 서버 관리 작업에서는 AzureRM(Azure Resource Manager) cmdlet을 사용합니다. 역할 멤버 추가/제거, 처리 또는 TMSL 스크립트 실행과 같은 기타 데이터베이스 관리 작업에서는 SqlServer 모듈에 포함된 cmdlet을 사용합니다. AzureRM 및 SQLServer 모듈은 모두 [PowerShell 갤러리(영문)](https://www.powershellgallery.com/)에서 사용할 수 있습니다.


## <a name="your-data-is-secure"></a>데이터가 안전함
![데이터 시각화](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>인증
Azure Analysis Services에 대한 사용자 인증은 [AAD(Azure Active Directory)](../active-directory/active-directory-whatis.md)에서 처리됩니다. Azure Analysis Services 데이터베이스에 로그인하려고 시도할 때 사용자는 액세스하려는 데이터베이스에 대한 액세스 권한이 있는 조직의 계정 ID를 사용합니다. 이러한 사용자 ID는 구독에서 Azure Analysis Services 서버가 있는 기본 Azure Active Directory의 구성원이어야 합니다. 자세한 내용은 [인증 및 사용자 권한](analysis-services-manage-users.md)을 참조하세요.

#### <a name="data-security"></a>데이터 보안
Azure Analysis Services에서는 Azure Blob 저장소를 활용하여 Analysis Services 데이터베이스의 저장소와 메타데이터를 유지합니다. Blob에 있는 데이터 파일은 Azure Blob SSE(Server Side Encryption)를 통해 암호화됩니다. 직접 쿼리 모드를 사용하는 경우 메타데이터만 저장됩니다. 실제 데이터는 쿼리 시 데이터 원본에서 액세스됩니다.

#### <a name="firewall"></a>방화벽

Azure Analysis Services 방화벽은 규칙에 지정된 것 이외의 모든 클라이언트 연결을 차단합니다. 개별 클라이언트 IP 또는 범위로 허용된 IP 주소를 지정하는 규칙을 구성합니다. Power BI(서비스) 연결은 허용되거나 차단될 수 있습니다. 

#### <a name="on-premises-data-sources"></a>온-프레미스 데이터 원본
[온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치하고 구성하여 조직의 온-프레미스에 있는 데이터에 대한 액세스를 보호합니다. 게이트웨이는 직접 쿼리 모드와 메모리 내 모드 둘 다의 데이터 액세스를 제공합니다. Azure Analysis Services 모델이 온-프레미스 데이터 원본에 연결되는 경우 쿼리는 온-프레미스 데이터 원본에 대해 암호화된 자격 증명과 함께 만들어집니다. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 Azure Service Bus에 대한 요청을 보냅니다. 온-프레미스 데이터 게이트웨이에서는 보류 중인 요청을 위해 Azure Service Bus를 폴링합니다. 그러면 게이트웨이에서 쿼리를 가져오고, 자격 증명의 암호를 해독한 다음 쿼리를 실행할 데이터 원본에 연결합니다. 끝으로 그 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후에 Azure Analysis Services 데이터베이스로 전송됩니다.

Azure Analysis Services는 [Microsoft Online Services 약관](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) 및 [Microsoft Online Services 개인 정보 취급 방침](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)에 준하여 사용할 수 있습니다.
Azure 보안에 대한 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/trustcenter/Security/AzureSecurity)를 참조하세요.



## <a name="supports-the-latest-client-tools"></a>최신 클라이언트 도구 지원
![데이터 시각화](./media/analysis-services-overview/aas-overview-clients.png)

Power BI, Excel, SQL Server 2017 Reporting Services 및 타사 도구와 같은 최신 데이터 탐색 및 시각화 도구가 지원되며, 모델 데이터에 대해 시각적으로 풍부한 대화형 정보를 사용자에게 제공합니다. 

클라이언트는 MSOLAP, AMO 또는 ADOMD [클라이언트 라이브러리](analysis-services-data-providers.md)를 사용하여 Analysis Services 서버에 연결합니다. Power BI Desktop 및 Excel과 같은 Microsoft 클라이언트 응용 프로그램은 이러한 세 가지 클라이언트 라이브러리를 모두 설치합니다. 그러나 업데이트 버전 또는 빈도에 따라 클라이언트 라이브러리가 Azure Analysis Services에 필요한 최신 버전이 아닐 수도 있습니다. 사용자 지정 응용 프로그램, 또는 AsCmd, TOM, ADOMD.NET과 같은 다른 인터페이스에도 마찬가지입니다. 이러한 응용 프로그램에서는 일반적으로 패키지의 일부로 라이브러리를 수동으로 설치해야 합니다.


## <a name="get-help"></a>도움말 보기

#### <a name="documentation"></a>문서화
Azure Analysis Services에서는 간단하게 설정하고 관리할 수 있습니다. 서버 서비스를 만들고 관리하는 데 필요한 모든 정보는 여기서 찾을 수 있습니다. 서버에 배포할 데이터 모델을 만드는 것은 온-프레미스 서버에 배포할 데이터 모델을 만드는 것과 매우 비슷합니다. 개념, 절차, 자습서 및 참조 문서에 대한 광범위한 자료는 [SQL Server Analysis Services 도움말](https://docs.microsoft.com/sql/analysis-services/analysis-services)에 있습니다.

#### <a name="videos"></a>동영상
[Channel 9의 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services)에서 유용한 동영상을 확인하세요.

#### <a name="blogs"></a>블로그
상황이 빠르게 변화하고 있습니다. 최신 정보는 언제든지 [Analysis Services 팀 블로그](https://blogs.msdn.microsoft.com/analysisservices/) 및 [Azure 블로그](https://azure.microsoft.com/blog/)에서 얻을 수 있습니다.

#### <a name="community"></a>커뮤니티
Analysis Services에는 활발한 사용자 커뮤니티가 있습니다. [Azure Analysis Services 포럼](https://aka.ms/azureanalysisservicesforum)에서 주최하는 대화에 참여해 보세요.

## <a name="feedback"></a>사용자 의견
제안할 사항이나 요청하려는 기능이 있습니까? 여러분의 의견을 [Azure Analysis Services 피드백](https://aka.ms/azureanalysisservicesfeedback)에 남겨주세요.

문서에 대해 제안할 사항이 있습니까? 각 문서의 아래쪽에 있는 Livefyre를 사용하여 주석을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure에서 [서버를 만드는 방법](analysis-services-create-server.md)을 알아봅니다.   
서버를 만든 후 [샘플 모델을 추가합니다](analysis-services-create-sample-model.md).   
