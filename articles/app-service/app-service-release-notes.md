---
title: "Azure SDK for .NET 2.5.1 릴리스 정보"
description: "Azure SDK for .NET 2.5.1 릴리스 정보"
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: 
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 0a422b02623a18ac6a1eef460bbada681672e69f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2017
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK for .NET 2.5.1 릴리스 정보
이 문서에는 Azure SDK for .NET 2.5.1 릴리스의 릴리스 정보가 포함되어 있습니다. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK for .NET 2.5.1 릴리스 정보
다음은 Azure SDK for .NET 2.5.1의 새로운 기능 및 업데이트입니다.

* **웹 도구 확장**과 관련된 새로운 기능\시나리오 
  
  * Azure Websites의 이름이 Azure App Service로 바뀌었습니다. 
  * Azure API Apps(미리 보기) 지원은 고객이 ASP.NET 프로젝트를 API Apps로 게시한 다음 C# 프로젝트에서 추가 > Azure API 앱 클라이언트 제스처를 사용하여 배포된 API 앱의 구조에 따라 코드를 생성할 수 있도록 추가되었습니다. 
  * 서버 탐색기의 Websites 노드는 더 이상 사용되지 않으며, 대신 리소스 그룹 기반의 Azure API Apps, Mobile Apps 및 Web Apps 그룹화 지원을 포함하는 Azure App Service 노드가 사용됩니다.
  * Azure Mobile Apps(미리 보기) 지원은 고객이 새 Mobile Apps 프로젝트를 만들고, Mobile Apps 컨트롤러를 추가하고, 프로젝트를 게시하고, 원격으로 응용 프로그램을 디버그할 수 있도록 추가되었습니다.
  * 이제 추가 > Azure API 앱 클라이언트 제스처에서 로컬 Swagger JSON 파일을 지원하므로 웹 API 개발자가 Swashbuckle와 같은 타사 NuGet을 사용하여 Swagger를 생성하거나 수동으로 작성할 수 있습니다. 이런 방식으로 클라이언트 개발자는 코드 생성 기능을 사용하여 C# 프로젝트에서 Swagger 끝점을 사용할 수 있습니다. 
  * 웹앱 및 API 앱 게시 대화 상자가 Azure Portal의 리소스 그룹화 개념을 지원하도록 향상되었으며 Azure 리소스 그룹 및 App Service 계획 선택/생성이 새로운 웹앱 및 API 앱 프로비저닝 대화 상자에 표시됩니다. 
  * Azure API Apps 서버 탐색기 노드는 Azure Portal의 API Apps 딥 링크에 대한 링크와 로그 스트리밍, 원격 디버깅 등의 기타 기능을 제공합니다.
    
    Azure SDK .NET 2.5.1의 알려진 문제와 현재 제한은 아래의 [이](app-service-release-notes.md#known_issues_2_5_1) 섹션을 참조하세요.
* 이 릴리스에서는 Visual Studio의 **HDInsight Tools**와 관련된 새로운 기능\시나리오를 사용할 수 있습니다. 
  
  * 하이브 스크립트의 로컬 유효성 검사. 스크립트에 오류가 있는지 확인하려면 도구 모음에서 스크립트 유효성 검사 단추를 클릭합니다. 
  * 향상된 하이브 작업 디버그. 이제 Visual Studio에서 Yarn 로그에 액세스하여 하이브 작업을 디버그할 수 있습니다. 응용 프로그램에 성능 문제가 있는 경우 YARN 로그를 조사하면 유용한 정보가 제공됩니다.
  * (공개 미리 보기) 하이브에 대한 키워드 자동 완성 및 IntelliSense 지원. 하이브 스크립트 작성을 돕기 위해 HDInsight Tools for Visual Studio에 하이브에 대한 키워드 자동 완성 및 IntelliSense 지원이 추가되었습니다.
  * Storm 지원. 이제 HDInsight Tools for Visual Studio를 사용하여 C#에서 Storm 토폴로지/Spout/Bolt를 개발할 수 있습니다. 그런 다음 개발된 토폴로지를 Storm 클러스터에 제출하고 토폴로지/bolt/spout 상태를 확인할 수 있습니다. 시스템 로그와 고객 로그를 사용하여 Storm 토폴로지/Spout/Bolt 문제를 해결할 수 있습니다. 또한 HDInsight의 Storm에서 기존 JAVA 자산을 사용할 수 있습니다.
    
    자세한 내용은 [Visual Studio용 HDInsight Hadoop 도구 사용 시작](../hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

## <a id="known_issues_2_5_1"></a>Azure SDK for .NET 2.5.1 알려진 문제 및 제한
* Azure API Apps는 Mobile Apps의 배포 대상으로 표시됩니다. 후속 릴리스가 출시될 때까지 Mobile Apps의 유일한 대상은 Web Apps이어야 합니다. 
* Azure API 앱 프로비저닝이 성공해도 Azure App Service 작업 창에서 진행률이 업데이트되지 않는 경우가 가끔 발생합니다. 해결 방법은 Azure Portal에서 새로운 Azure API 앱의 상태를 확인하는 것입니다. 
* default/index.html이 없으므로 파일 > 새 프로젝트 > API 앱 > F5 키를 클릭하면 HTTP 오류가 발생합니다. 해결 방법은 수동으로 /api/values URL을 찾는 것입니다. 
* 서버 탐색기 아이콘이 평면으로 표시되는 경우가 가끔 있습니다. VS를 다시 시작하면 이 문제가 해결됩니다. 
* 웹앱 또는 API 앱 프로비저닝 중 예외가 발생할 경우(예: 할당량 초과 오류 또는 중복된 Azure API 앱 게이트웨이 이름) 오류에 원시 JSON 텍스트가 일부 표시됩니다. 
* 프로젝트를 만드는 과정에서 Application Insights를 확인할 때 프로젝트 생성 문제가 가끔 발생합니다.
* 생성된 Azure API 앱 클라이언트 코드에 네임스페이스가 누락되는 경우가 가끔 있으며, 수동으로 포함하거나 Visual Studio 신호를 통해 자동으로 가져와야 코드가 컴파일됩니다. 
* 모바일 앱 프로젝트를 웹앱에 게시해야 하는데 모바일 앱 프로젝트에 데이터베이스가 필요하므로 Azure Portal에서 모바일 앱으로 만든 사이트를 선택해야 합니다. 
* Mobile Apps의 시작 페이지에서 "모바일 앱" 대신 "모바일 서비스" 용어가 사용됩니다. 
* 모바일 앱 프로젝트를 만드는 데 최대 1분 정도 걸릴 수 있습니다. 
* API 앱이 제대로 프로비전되었으며 사용할 준비가 완료되었는데 사용 권한을 제대로 설정할 수 없음을 나타내는 오류가 API 앱 프로비저닝 중 Azure API에서 반환되는 경우가 있습니다. Azure Portal에서 수동으로 사용 권한을 설정할 수 있습니다.
* API 앱 템플릿과 모바일 앱 템플릿에서 Application Insights가 지원되지 않습니다.
* API 앱 프로젝트를 클라우드 서비스 프로젝트와 함께 사용할 수 없습니다.
* API 앱 프로젝트 템플릿은 C#에서만 사용할 수 있습니다.
* "Azure API 앱 클라이언트 추가" 상황에 맞는 메뉴를 통한 API 앱 사용은 C#에서만 지원됩니다.

