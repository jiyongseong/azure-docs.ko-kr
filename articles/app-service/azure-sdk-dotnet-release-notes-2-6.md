---
title: "Azure SDK for .NET 2.6 릴리스 정보"
description: "Azure SDK for .NET 2.6 릴리스 정보"
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Azure SDK for .NET 2.6 릴리스 정보
이 문서에는 Azure SDK for .NET 2.6 릴리스의 릴리스 정보가 포함되어 있습니다. 

Azure SDK 2.6에서는 클라우드 서비스 역할에 대상 .NET Framework를 수동으로 설치한 경우 .NET 4.5.2 또는 .NET 4.6을 대상으로 하는 클라우드 서비스 응용 프로그램(PaaS)을 개발할 수 있습니다. [클라우드 서비스 역할에 .NET 설치](http://go.microsoft.com/fwlink/?LinkID=309796)를 참조하세요.

## <a name="service-bus-updates"></a>서비스 버스 업데이트
* 이벤트 허브: 
  
  * 이제 이벤트를 보낼 때 이벤트 허브에 대한 추가 게시자 끝점을 노출하여 대상별 액세스를 제어할 수 있습니다.
  * 이벤트 허브 기능에 안정성 및 향상된 기능이 추가되었습니다.
  * 메시징용 WebSocket 및 이벤트 허브를 통해 Amqp 프로토콜을 추가로 지원합니다.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Visual Studio용 HDInsight 도구 업데이트
* **IntelliSense 향상 기능**: 원격 메타데이터 제안
  
    이제 Visual Studio용 HDInsight 도구에서 하이브 스크립트를 편집할 때 원격 메타데이터 가져오기를 지원합니다. 예를 들어 **SELECT * FROM**을 입력하여 모든 테이블 이름을 표시할 수 있습니다. 또한 테이블을 지정한 후 열 이름이 표시됩니다.
* **HDInsight Emulator 지원**
  
    이제 Visual Studio용 HDInsight 도구에서 HDInsight Emulator 연결을 지원하므로 비용 없이 하이브 스크립트를 로컬로 개발한 다음 HDInsight 클러스터에 대해 이러한 스크립트를 실행할 수 있습니다. 
  
    자세한 내용은 [이 설명서](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)를 참조하세요.
* **일반 Hadoop 클러스터에 대한 Visual Studio용 HDInsight 도구 지원** (미리 보기)
  
    이제 Visual Studio용 HDInsight 도구에서 일반 Hadoop 클러스터를 지원하므로 Visual Studio용 HDInsight 도구를 사용하여 다음을 수행할 수 있습니다.
  
  * 클러스터에 연결 
  * 향상된 IntelliSense/자동 완성 지원으로 하이브 쿼리 작성 
  * 직관적인 UI에서 클러스터의 모든 작업 보기 
    
    자세한 내용은 [이 설명서](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)를 참조하세요.

## <a name="in-role-cache-updates"></a>In-Role Cache 업데이트
* **Microsoft Azure Storage SDK** 버전 4.3을 사용하도록 **In-Role Cache**가 업데이트되었습니다. 지금까지 **In-Role Cache** 는 Azure 저장소 SDK 버전 1.7을 사용했습니다.
  
    Azure SDK 2.5 이하를 사용하는 고객은 Azure SDK 2.6으로 업데이트하고 새 버전의 Azure Storage SDK로 전환해야 합니다. 
  
    이번에 Azure 저장소 버전 2011-08-18이 2016년 8월 1일에 제거될 예정입니다. 따라서 2.5 이하부터 2.6 버전의 Azure SDK에 대한 In-Role Cache의 모든 마이그레이션을 이번에 완료해야 합니다. Azure 저장소 버전 2011-08-18의 사용 중지에 대한 자세한 내용은 [Microsoft Azure 저장소 서비스 버전 제거 업데이트: 2016에 확장](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)을 참조하세요.

> [!IMPORTANT]
> 2016년 11월 30일부터 Azure 관리 캐시된 서비스 및 Azure In-Role Cache가 사용 중지됩니다. 이러한 사용 중지에 대비하기 위해 Azure Redis Cache로 마이그레이션하는 것이 좋습니다. 날짜 및 마이그레이션 지침에 대한 자세한 내용은 [내게 적합한 Azure 캐시 제품은 무엇인가요?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Azure 앱 서비스 도구
다음 항목은 Azure SDK 2.6 릴리스에서 업데이트되었습니다.

* Azure 게시 기능이 Azure API 앱을 배포 대상으로 포함하도록 향상되었습니다.
* API 앱 프로비저닝 기능이 사용자의 API 앱 만들기 및 프로비저닝 기능을 지원하도록 향상되었습니다.
* 웹, 모바일 및 API 앱이 리소스 그룹별로 그룹화된 새로운 앱 서비스 노드를 반영하도록 서버 탐색기가 변경되었습니다.
* 사용자의 Azure 구독에서 실행되는 Swagger 지원 API 앱을 자동으로 생성하는 Azure API 앱 클라이언트 추가 제스처가 대부분의 C# 프로젝트에 추가되었습니다.
* 서버 탐색기의 API 앱 도구 및 앱 서비스 노드는 Visual Studio 2013에서만 사용할 수 있습니다. 

## <a name="azure-resource-manager-tools-updates"></a>Azure 리소스 관리자 도구 업데이트
가상 컴퓨터, 네트워킹 및 저장소에 대한 템플릿을 포함하도록 Azure 리소스 관리자 도구가 업데이트되었습니다. 템플릿에 대한 새 개요 보기 및 JSON 조각을 사용한 템플릿 편집 기능을 포함하도록 JSON 편집 환경이 업데이트되었습니다. Visual Studio에서 배포된 템플릿은 프로젝트와 함께 제공되는 PowerShell 스크립트를 사용하므로 스크립트 변경 내용이 Visual Studio에서 사용됩니다.

## <a name="diagnostics-improvements-for-cloud-services"></a>클라우드 서비스에 대한 향상된 진단 기능
Azure SDK 2.6에서는 Azure 계산 에뮬레이터의 진단 로그를 수집하여 개발 저장소로 전송합니다. 응용 프로그램이 에뮬레이터에서 실행될 때 생성된 모든 진단 로그(응용 프로그램 추적 로그, ETW(Windows용 이벤트 추적) 로그, 인프라 로그 및 Windows 이벤트 로그 포함)를 개발 저장소로 전송하여 진단 로깅이 로컬 컴퓨터에서 작동하는지 확인할 수 있습니다. 

이제 서비스 구성(.cscfg) 파일에서 진단 저장소를 지정할 수 있으므로 환경마다 서로 다른 진단 저장소 계정을 보다 쉽게 사용할 수 있습니다. Azure SDK 2.4와 Azure SDK 2.6에서 연결 문자열이 작동하는 방식 간에 몇 가지 주목할 만한 차이점이 있습니다. 진단 저장소 연결 문자열을 사용하는 방법 및 프로젝트에 미치는 영향에 대한 자세한 내용은 [Azure 클라우드 서비스에 대한 진단 구성](http://go.microsoft.com/fwlink/?LinkID=532784)을 참조하세요.

## <a name="breaking-changes"></a>주요 변경 내용
### <a name="azure-resource-manager-tools"></a>Azure 리소스 관리자 도구
* Azure SDK 2.5에서 사용 가능한 **클라우드 배포 프로젝트**라는 프로젝트 유형의 이름이 **Azure 리소스 그룹**으로 바뀌었습니다.
* **클라우드 배포 프로젝트** 유형의 프로젝트를 2.6에서 사용할 수 있지만 Visual Studio에서 템플릿을 배포하지는 못합니다. 그러나 PowerShell 스크립트를 사용한 배포는 이전과 마찬가지로 여전히 작동합니다.  2.6에서 **클라우드 배포 프로젝트**를 사용하는 방법에 대한 자세한 내용은 이 [게시물](http://go.microsoft.com/fwlink/?LinkID=534086)을 참조하세요.

## <a name="known-issues"></a>알려진 문제
* 에뮬레이터에서 진단 로그를 수집하려면 64비트 운영 체제가 필요합니다. 32비트 운영 체제에서 실행할 때는 진단 로그가 수집되지 않습니다. 다른 에뮬레이터 기능은 영향을 받지 않습니다. 
* 2015/04/29에 릴리스된 Azure SDK 2.6의 두 가지 문제: 
  
  * Azure SDK 2.6를 컴퓨터에 설치할 때 Visual Studio 2015에서 유니버설 앱이 로드되지 않습니다.
  * Visual Studio 2013 및 Visual Studio 2015에서 클라우드 서비스 프로젝트 디버깅이 실패합니다. “에뮬레이터에 대한 진단 구성” 메시지 대화 상자가 표시되는 동안 Visual Studio가 응답하지 않고 충돌하게 됩니다.
    
    Azure SDK 2.6의 업데이트는 2015/05/18에 릴리스되었습니다. 업데이트 버전은 2.6.30508.1601이며, 위에서 설명한 두 가지 문제를 수정하였습니다. 제어판 -> 프로그램 및 기능 -> Microsoft Azure Tools for Microsoft Visual Studio 2013 – v 2.6에서 SDK의 빌드를 식별할 수 있습니다. 버전 열에 빌드 번호가 표시됩니다.
    
    여전히 위의 문제가 발생한다면 Azure 2.6 SDK for [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409), [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)의 최신 버전을 설치하십시오.

## <a name="see-also"></a>참고 항목
[.NET 및 API용 Azure SDK에 대한 지원 및 사용 중지 정보](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

