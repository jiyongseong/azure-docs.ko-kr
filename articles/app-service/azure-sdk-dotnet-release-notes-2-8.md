---
title: Azure SDK for .NET 2.8 릴리스 정보
description: Azure SDK for .NET 2.8 릴리스 정보
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK for .NET 2.8, 2.8.1, 2.8.2
## <a name="overview"></a>개요
이 문서에는 Azure SDK for .NET 2.8, 2.8.1, 2.8.2 릴리스에 대한 알려진 문제와 주요 변경 내용을 포함하는 릴리스 정보가 포함되어 있습니다. 

이 릴리스의 새로운 기능 및 업데이트에 대한 전체 목록은 [Visual Studio 2013 및 Visual Studio 2015용 Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) 알림을 참조하세요. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8 다운로드
[Visual Studio 2015용 Azure SDK for .NET 2.8](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Visual Studio 2013용 Azure SDK for .NET 2.8](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>.NET 4.5.2 지원
#### <a name="known-issues"></a>알려진 문제
Azure .NET SDK 2.8을 사용하여 .NET 4.5.2 클라우드 서비스 패키지를 만들 수 있습니다. 그러나 .NET 4.5.2 프레임워크는 기본 게스트 OS 이미지에 설치되지 않습니다(게스트 OS 2016년 1월 버전까지). 그전에는 .NET 4.5.2 프레임워크를 별도 게스트 OS 릴리스 버전(2015년 11월-02)을 통해 사용할 수 있습니다. 이미지가 릴리스되는 시기를 추적하려면 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](../cloud-services/cloud-services-guestos-update-matrix.md) 페이지를 참조하세요.  2015년 11월-02 이미지가 릴리스되면 클라우드 서비스 구성 파일(.cscfg)을 업데이트하여 해당 이미지를 사용하도록 선택할 수 있습니다. 서비스 구성 파일에서 ServiceConfiguration 요소의 osVersion 특성을 문자열 "WA-GUEST-OS-4.26_201511-02"로 설정합니다. 이 이미지를 사용하도록 선택한 경우 더 이상 게스트 OS에 대한 자동 업데이트를 받지 않습니다. 자동 업데이트를 받으려면 osVersion이 “*”로 설정되어 있어야 하며 .NET 4.5.2는 2016년 1월 자동 업데이트를 통해서만 사용할 수 있습니다.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리
#### <a name="known-issues"></a>알려진 문제
샘플 데이터를 포함하는 **데이터 팩터리 템플릿** 프로젝트를 만드는 동안 컴퓨터에 설치된 Azure PowerShell 버전이 0.9.8 이후이면 Azure PowerShell 스크립트가 실패할 수 있습니다.

이 유형의 프로젝트를 성공적으로 만들려면 [Azure PowerShell 버전 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)을 설치해야 합니다.

### <a name="azure-resource-manager-tools"></a>Azure 리소스 관리자 도구
#### <a name="breaking-changes"></a>주요 변경 내용
Azure 리소스 그룹 프로젝트에서 제공한 PowerShell 스크립트가 새 Azure PowerShell cmdlet 버전 1.0을 사용하여 작업하도록 이 릴리스에서 업데이트되었습니다.  이 새로운 스크립트는 SDK 2.8 이전 버전을 사용하는 경우 Visual Studio에서 작동하지 않습니다.  

이전 버전의 SDK에서 만든 프로젝트의 스크립트는 SDK 2.8을 사용하면 Visual Studio 내에서 실행되지 않습니다.  모든 스크립트는 Azure PowerShell cmdlet의 적절한 버전을 사용하여 Visual Studio 외부에서 계속 작동합니다.  

SDK 2.8을 사용하려면 Azure PowerShell cmdlet 버전 1.0이 필요합니다.  다른 모든 버전의 SDK를 사용하려면 Azure PowerShell cmdlet 버전 0.9.8이 필요합니다.  자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkID=623011)를 참조하세요.

### <a name="web-tools-extensions"></a>웹 도구 확장
#### <a name="known-issues"></a>알려진 문제
다음과 같이 알려진 문제는 다음 릴리스에서 해결된 예정입니다.

* 비프로덕션 환경(예: Azure China 또는 Azure Stack 고객)에서는 App Service 관련 클라우드 및 서버 탐색기 제스처가 작동하지 않습니다. 이러한 영향을 받는 지역의 고객은 Azure 포털에서 게시 프로필을 다운로드하면 게시 기능을 사용할 수 있습니다. 향후 릴리스에서 Azure China 및 스택 고객에 대한 "디버거 연결" 및 "스트리밍 로그 보기"와 같은 제스처가 복구됩니다. 
* 배포하려는 App Insights 인스턴스가 미국 동부 이외의 지역에 있는 경우 App Service를 만드는 동안 오류가 표시될 수 있습니다. 이러한 시나리오에서는 포털에서 App Service를 만들고 게시 프로필을 다운로드하면 게시 시나리오를 사용할 수 있습니다. 

### <a name="azure-hdinsight-tools"></a>Azure HDInsight 도구
#### <a name="new-updates"></a>새 업데이트
* 거의 오버헤드 없이 HiveServer2를 통해 클러스터에서 Hive 쿼리를 실행하고 작업 기록을 실시간으로 볼 수 있습니다.
* 새 Hive 작업 실행 보기를 사용하여 작업을 자세히 살펴볼 수 있으며 자세한 정보를 찾아 잠재적인 문제를 식별할 수 있습니다.

자세한 내용은 [Visual Studio 2013 및 Visual Studio 2015용 Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)을 참조하세요. 

## <a name="azure-sdk-for-net-281"></a>Azure SDK for .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 및 Visual Studio 2015에 대해 알려진 문제
1. 트리거된 WebJob이 슬롯에 게시되면 오류가 표시되면서 예약이 설정되지 않지만 WebJob을 Azure에 푸시합니다. 예약된 작업이 필요한 고객은 Azure Portal을 사용하여 WebJob에 대해 예약을 설정할 수 있습니다. 
2. Python 고객에게 디버거 문제가 발생할 수 있습니다. 서비스 팀은 이 문제에 대한 픽스를 내놓고 있지만 고객이 영향을 받는 경우 포럼 또는 공지 블로그나 릴리스 정보 의견 섹션을 통해 Microsoft에 알려 주세요. 
3. 특정 지역(예: 인도 남부)의 고객에게 App Service 프로비저닝 오류가 발생합니다. 이는 포털과 일치하며 이 문제가 발생하는 고객은 Azure 포털을 사용하여 이러한 지역에 게시하기 위해 액세스를 요청할 수 있습니다. Azure 포털을 사용하여 이러한 지역에 대한 액세스를 요청하면 프로비저닝이 작동합니다. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK for .NET 2.8.2
2.8.2 도구를 설치한 후 고객에게 다음과 같은 문제가 발생할 수 있습니다.         

* Internet Explorer를 설치하지 않고 Windows 10을 사용하는 경우 "Internet Explorer를 찾을 수 없습니다"라는 오류가 발생할 수 있습니다.
  이 문제를 해결하려면 Windows 구성 요소 추가/제거 대화 상자를 사용하여 Internet Explorer를 설치합니다.

이러한 문제를 확인하면 웃는 얼굴 보내기 기능을 사용하여 보고합니다.

자세한 내용은 [이 게시물](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) 을 참조하세요.

## <a name="other-updates"></a>다른 업데이트
다른 업데이트는 [Azure SDK 2.8 발표 게시물](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)을 참조하세요.

## <a name="also-see"></a>참고 항목
[Azure SDK 2.8 발표 게시물](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[.NET 및 API용 Azure SDK에 대한 지원 및 사용 중지 정보](https://msdn.microsoft.com/library/azure/dn479282.aspx)

