---
title: "Azure SDK for .NET 2.7 및 .NET 2.7.1 릴리스 정보"
description: "Azure SDK for .NET 2.7 및 .NET 2.7.1 릴리스 정보"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Azure SDK for .NET 2.7 및 .NET 2.7.1 릴리스 정보
## <a name="overview"></a>개요
이 문서에는 Azure SDK for .NET 2.7 릴리스의 릴리스 정보가 포함되어 있습니다. 

이 문서에는 Azure SDK for .NET 2.7.1 릴리스의 릴리스 정보도 포함되어 있습니다.

Azure SDK 2.7은 Visual Studio 2015 및 Visual Studio 2013에서만 지원됩니다. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) 은 Visual Studio 2012에 대해 마지막으로 지원되는 SDK입니다.

이 릴리스에 대한 자세한 내용은 [Azure SDK 2.7 발표 게시물](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) 및 [Azure SDK 2.7.1 발표 게시물](http://go.microsoft.com/fwlink/?LinkId=623850)을 참조하세요.

## <a name="azure-sdk-for-net-27"></a>Azure SDK for .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Visual Studio 2015의 향상된 로그인 기능
Visual Studio 2015용 Azure SDK 2.7은 Visual Studio 2015의 새 ID 관리 기능을 지원합니다.  역할 기반 액세스 제어, 클라우드 솔루션 공급자, DreamSpark, 기타 계정 및 구독 유형을 통해 Azure에 액세스하는 계정에 대한 지원이 포함되어 있습니다.

Azure SDK 2.7에 포함된 향상된 로그인 기능은 Visual Studio 2015에서만 사용할 수 있습니다. Visual Studio 2013에 대한 지원은 Azure SDK 2.7.1에 포함되어 있습니다.

### <a name="mobile-sdk"></a>모바일 SDK
최신 **NuGet 패키지** 및 설치 프로세스가 반영되도록 [모바일 앱](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 템플릿을 업데이트합니다.

### <a name="service-bus"></a>서비스 버스
일반 버그 수정 사항 및 향상된 기능입니다. 업데이트 및 기능에 대한 자세한 내용은 최신 [서비스 버스 NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)의 릴리스 정보를 참조하세요.

### <a name="hdinsight-tools"></a>HDInsight 도구
이 릴리스에서는 다음 사항이 업데이트되었습니다. 이러한 업데이트는 미리 보기 상태입니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=619108)를 참조하세요.

* Tez 기반 Hive 작업에 대한 Hive 그래프
* 완전한 Hive DML IntelliSense 지원
* Pig 템플릿 지원
* Azure 서비스용 Storm 템플릿

#### <a name="breaking-changes"></a>주요 변경 내용
* 이 버전의 도구를 사용하는 경우 이전 **Storm** 프로젝트를 업그레이드해야 합니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=619108)를 참조하세요.
* Visual Studio Web Express는 더 이상 지원되지 않습니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=619108)를 참조하세요.

### <a name="azure-app-service-tools"></a>Azure 앱 서비스 도구
이 릴리스에서는 웹 도구 확장에 대해 다음 사항이 업데이트되었습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)를 참조하세요. 

* DreamSpark 계정에 대한 지원 추가
* 새 Azure 리소스 관리 API를 지원하도록 전체 Azure Tools 변경
* [Cloud Explorer](#cloud_explorer)에 Azure 앱 서비스에 대한 지원 추가

#### <a name="known-issues"></a>알려진 문제
웹앱 배포 슬롯 노드가 서버 탐색기의 슬롯 노드 아래에 표시되지 않으며, 웹앱 배포 슬롯 자식 노드가 Cloud Explorer 아래에 로드되지 않습니다. 이 문제는 해결되었으며 다음 SDK 릴리스에 포함되도록 준비되었습니다. 

### <a name="cloud_explorer"></a>Visual Studio 2015용 Cloud Explorer
Azure SDK 2.7에는 Azure 리소스를 보고, 해당 속성을 검사하고, Visual Studio 내에서 핵심 개발자 작업을 수행할 수 있는 Visual Studio 2015용 Cloud Explorer가 포함되어 있습니다. 

Cloud Explorer는 다음을 지원합니다.

* Azure 리소스에 대한 리소스 그룹 및 리소스 종류 뷰 
* 이름별 리소스 검색(리소스 종류 뷰에서 사용 가능)
* RBAC(역할 기반 액세스 제어)가 적용되는 구독 및 리소스에 대한 지원 
* 선택한 리소스와 관련된 개발자 중심 작업을 보여주는 통합 작업 패널. 예: 리소스 관리자 스택을 사용하여 만든 가상 컴퓨터에 대한 원격 디버거 연결, 가상 컴퓨터에 대한 진단 데이터 보기 등
* 개발/테스트 중 일반적으로 필요한 개발자 중심 속성을 보여주는 통합 속성 패널 
* 리소스 열거 시 사용할 계정의 빠른 전환(도구 모음에서 설정 명령 사용) 
* 리소스 열거 시 사용할 구독 필터링(도구 모음에서 설정 명령 사용) 
* 리소스 및 리소스 그룹 관리를 위한 Azure 포털 딥 링크 

### <a name="azure-resource-manager-tools"></a>Azure 리소스 관리자 도구
Azure 리소스 관리자 도구가 RBAC(역할 기반 액세스 제어) 및 새 구독 유형과 작동하도록 업데이트되었습니다.  기존 저장소 외에도 배포하는 동안 새 저장소 계정을 사용하여 아티팩트를 저장하는 기능이 변경 내용에 포함되어 있습니다.  

SDK 2.7과 함께 이전 버전의 SDK에서 Azure 리소스 그룹 프로젝트를 사용하는 경우 새 배포 스크립트는 기존 저장소가 아닌 새 저장소 계정을 사용하여 배포해야 합니다.  프로젝트가 변경되기 전에 새 스크립트를 추가하라는 메시지가 표시됩니다.  이전 스크립트는 이름이 변경되며 새 스크립트는 수동으로 수정해야 합니다.

### <a name="storage-explorer-tools"></a>저장소 탐색기 도구
* 추가 Blob 보기 지원입니다. 자세한 내용은 [이 블로그 게시물](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)에 있습니다. 
* 서버 탐색기를 통한 프리미엄 저장소 계정 보기 지원입니다. 서버 탐색기는 프리미엄 저장소 계정에 대해 페이지 Blob만 표시합니다. 프리미엄 저장소 계정에 대해 지원되는 유일한 형식이기 때문입니다.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Visual Studio용 Azure Data Factory 도구
Visual Studio용 **Azure Data Factory 도구**를 소개합니다. 사용할 수 있는 기능은 다음과 같습니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=617530)를 참조하세요.

* **템플릿 기반 작성**: 사용 사례 기반 템플릿, 데이터 이동 템플릿 또는 데이터 처리 템플릿을 선택하여 종단 간 데이터 통합 솔루션을 배포하고 Data Factory 실습을 빠르게 시작합니다. 
* **Data Factory 엔터티 작성 및 배포를 위해 솔루션 탐색기와 통합**: 파이프라인 및 관련 엔터티를 Visual Studio 프로젝트로 만들고 배포합니다. 
* **작성 중 시각적 상호 작용을 위해 다이어그램 뷰와 통합**: 다이어그램 뷰 지원을 통해 시각적으로 파이프라인 및 데이터 집합을 작성합니다. 
* **이미 배포된 엔터티 찾아보기 및 상호 작용을 위해 서버 탐색기와 통합**: 서버 탐색기를 활용하여 이미 배포된 데이터 팩터리 및 해당 엔터티를 찾아봅니다. 배포된 데이터 팩터리 또는 엔터티(파이프라인, 연결된 서비스, 데이터 집합)를 프로젝트로 가져옵니다. 
* **스키마 유효성 검사와 다양한 기능의 Intellisense를 사용하는 JSON 편집**: 다양한 기능의 Intellisense와 스키마 유효성 검사를 사용하여 Data Factory 엔터티의 JSON 문서를 효율적으로 구성하고 편집합니다. 
* **다중 환경 게시**: 환경마다 별도의 구성 파일을 만들어 개발, 테스트 또는 프로덕션 환경에 작성된 파이프라인을 게시합니다.
* **Pig, Hive 및 .Net 기반 데이터 처리 지원**: Data Factory 프로젝트의 Pig 및 Hive 스크립트에 대한 지원입니다. .NET 작업 관리에 필요한 C# 프로젝트 참조에 대한 지원입니다.

## <a name="azure-sdk-for-net-271"></a>Azure SDK for .NET 2.7.1
다음 섹션에는 Azure SDK for .NET 2.7.1 릴리스에서 도입된 업데이트가 포함되어 있습니다.

### <a name="hdinsight-tools"></a>HDInsight 도구
HDInsight 도구 업데이트에 대한 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=623831)를 참조하세요.

* Hive 작업 운영자 뷰(새로운 기능)
  
    Hive 쿼리를 파악하는 데 도움이 되도록 Hive 운영자 뷰 기능이 추가되었습니다. 꼭짓점 내의 모든 운영자를 보려면 작업 그래프의 꼭짓점을 두 번 클릭합니다. 특정 운영자에 대한 자세한 정보를 보려면 해당 운영자를 마우스로 가리킵니다.
* Hive 오류 표식(새로운 기능)
  
    문법 오류를 즉시 볼 수 있도록 Hive 오류 표식 기능이 추가되었습니다. 또한 오류 메시지가 향상되었으며, 이제 자세한 문법 오류를 즉시 확인할 수 있습니다. 이전 릴리스에서는 Hive 스크립트를 클러스터에 제출한 후 오류 메시지에 대한 세부 정보를 받을 때까지 일정 시간 동안 기다려야 했습니다.  
* Storm 토폴로지 그래프(새로운 기능)
  
    토폴로지가 예상대로 작동하는지 확인하려는 경우 시각화가 매우 중요합니다. 이 릴리스에서는 Storm 그래프에 대한 시각화가 추가되었습니다. 토폴로지에 대한 중요한 메트릭을 시각화할 수 있습니다(예: 색은 특정 Bolt가 "사용 중"인지 여부를 나타냄). Bolt/Spou를 두 번 클릭하여 자세한 정보를 볼 수도 있습니다.
* Azure 포털에서 생성된 HDInsight 클러스터에 대한 지원(버그 수정)
  
    이제 Visual Studio를 사용하여 클러스터가 생성된 위치에 관계없이 모든 HDInsight 클러스터를 보고 작업을 제출할 수 있습니다.
* 추가 IntelliSense 지원 및 Hive 메타데이터 로드 속도 개선(향상)
  
    사용자에게 보다 편리한 제안을 추가하여 IntelliSense가 향상되었습니다. 예를 들어 이제 테이블 별칭도 IntelliSense에서 제안되므로 쿼리를 보다 쉽게 작성할 수 있습니다. 또한 Hive 메타데이터 로드 속도가 개선되어 Hive Metastore의 모든 데이터베이스, 테이블 및 열을 나열하는 데 몇 초밖에 걸리지 않습니다.

HDInsight 도구 업데이트에 대한 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=623831)를 참조하세요.

### <a name="improvements-in-visual-studio-2013"></a>Visual Studio 2013의 향상된 기능
* Azure SDK 2.7.1을 사용하면 Visual Studio 2013이 역할 기반 액세스 제어, 클라우드 솔루션 공급자 및 Dreamspark를 통해 Azure 계정 및 구독에 액세스할 수 있습니다.
* Azure SDK 2.7.1부터 이제 Visual Studio 2013에서도 새 클라우드 탐색기 도구 창을 사용할 수 있습니다.

### <a name="known-issues"></a>알려진 문제
영어 이외의 운영 체제에 Visual Studio Community 2013용 Azure SDK 2.6 또는 2.7.1을 설치하면 Visual Studio의 영어 리소스와 영어 이외의 리소스가 일치하지 않을 수 있다는 경고가 표시됩니다. 이 경고는 해제해도 됩니다. 컴퓨터에 Visual Studio Community 2013의 이전 설치가 포함되어 있지 않고 영어 이외의 운영 체제에 SDK를 설치하는 경우에만 발생합니다. 이 경고는 언어 팩이 Visual Studio에 RTM 리소스를 적용한 후, 업데이트 4를 적용하기 전에 표시됩니다. 경고를 해제하면 언어 팩이 계속해서 언어 팩 콘텐츠의 업데이트 4 버전 적용을 완료합니다.

LightSwitch 프로젝트는 이 릴리스와 호환되지 않습니다. 이 문제는 다음 SDK 릴리스에서 해결됩니다.

## <a name="also-see"></a>참고 항목
[Azure SDK 2.7.1 발표 게시물](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 발표 게시물](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[.NET 및 API용 Azure SDK에 대한 지원 및 사용 중지 정보](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

