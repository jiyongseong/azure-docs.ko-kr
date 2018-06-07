---
title: "Azure App Service 로컬 캐시 개요 | Microsoft Docs"
description: "이 문서에서는 Azure 앱 서비스 로컬 캐시 기능을 사용하도록 설정하고, 크기를 조정하고, 상태를 쿼리하는 방법을 설명합니다."
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: 
tags: optional
keywords: 
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-local-cache-overview"></a>Azure 앱 서비스 로컬 캐시 개요
Azure 웹앱의 콘텐츠는 Azure 저장소에 저장되며 영구적 방식으로 콘텐츠 공유로 표시됩니다. 이 디자인은 다양한 앱으로 작업하기 위한 것이며, 다음과 같은 특성을 가집니다.  

* 콘텐츠는 웹앱의 여러 VM(가상 컴퓨터) 인스턴스 간에 공유됩니다.
* 콘텐츠는 영구적이며 웹앱을 실행하여 수정할 수 있습니다.
* 동일한 공유 콘텐츠 폴더 아래에서 로그 파일 및 진단 데이터 파일을 사용할 수 있습니다.
* 새 콘텐츠를 직접 게시하면 콘텐츠 폴더가 업데이트됩니다. SCM 웹 사이트 및 실행 중인 웹앱을 통해 동일한 콘텐츠를 즉시 볼 수 있습니다. 일반적으로 ASP.NET과 같은 일부 기술은 몇 가지 파일 변경 내용에 대해 웹앱을 다시 시작하여 최신 콘텐츠를 가져옵니다.

많은 웹앱에서 이러한 기능 중 하나 또는 모두를 사용하지만 일부 웹앱에서는 고가용성으로 실행할 수 있는 읽기 전용 콘텐츠 저장소 성능만 뛰어나면 됩니다. 이러한 앱은 특정 로컬 캐시의 VM 인스턴스 이점을 활용할 수 있습니다.

Azure 앱 서비스 로컬 캐시 기능은 콘텐츠의 웹 역할 보기를 제공합니다. 이 콘텐츠는 사이트 시작 시 비동기적으로 만들어지는 저장소 콘텐츠의 쓰기-삭제(write-but-discard) 캐시입니다. 캐시가 준비되면 캐시된 콘텐츠에 대해 실행되도록 사이트가 전환됩니다. 로컬 캐시에서 실행되는 웹앱은 다음과 같은 이점이 있습니다.

* Azure 저장소의 콘텐츠에 액세스할 때 발생하는 대기 시간의 영향을 받지 않습니다.
* 콘텐츠 공유를 지원하는 서버에서 발생하는 계획된 업그레이드 또는 계획되지 않은 가동 중지 시간 및 그 밖의 Azure 저장소 중단에 영향을 받지 않습니다.
* 저장소 공유 변경으로 인해 다시 시작되는 앱이 더 적습니다.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>로컬 캐시가 앱 서비스 동작을 변경하는 방식
* 로컬 캐시는 웹앱의 /site 및 /siteextensions 폴더의 복사본입니다. 웹앱 시작 시 로컬 VM 인스턴스에서 만들어집니다. 웹앱당 로컬 캐시의 크기는 기본적으로 300MB로 제한되지만 최대 2GB로 증가될 수 있습니다.
* 로컬 캐시는 읽기/쓰기가 가능합니다. 그러나 웹앱이 가상 컴퓨터를 이동하거나 다시 시작된 경우 모든 수정 내용이 삭제됩니다. 중요 업무용 데이터를 콘텐츠 저장소에 저장하는 앱에 로컬 캐시를 사용해서는 안 됩니다.
* 웹앱은 현재와 마찬가지로 로그 파일 및 진단 데이터를 계속 기록할 수 있습니다. 그러나 로그 파일 및 데이터는 VM에 로컬로 저장됩니다. 그런 다음 공유 콘텐츠 저장소에 주기적으로 복사됩니다. 공유 콘텐츠 저장소로의 복사는 최상의 시나리오를 전제로 하기 때문에 VM 인스턴스의 갑작스러운 작동 중단으로 인해 나중 쓰기가 손실될 수 있습니다.
* 로컬 캐시를 사용하는 웹앱의 경우 LogFiles 및 Data 폴더의 폴더 구조가 변경되었습니다. 이제 저장소 "LogFiles" 및 "Data" 폴더에 "고유 식별자" + 타임스탬프 명명 패턴을 따르는 하위 폴더가 있습니다. 각 하위 폴더는 웹앱을 실행 중이거나 실행한 VM 인스턴스에 해당합니다.  
* 게시 메커니즘 중 하나를 통해 웹앱에 대한 변경 내용을 게시할 경우 공유 콘텐츠 저장소에 게시됩니다. 이는 게시된 콘텐츠를 영구적으로 유지하기 위해 의도된 것입니다. 웹앱의 로컬 캐시를 새로 고치려면 다시 시작해야 합니다. 지나친 단계인 것 같나요? 수명 주기를 원활하게 하려면 이 문서의 뒷부분에 나오는 정보를 참조하세요.
* D:\Home은 로컬 캐시를 가리킵니다. D:\local은 임시 VM 관련 저장소를 계속 가리킵니다.
* SCM 사이트의 기본 콘텐츠 보기는 공유 콘텐츠 저장소의 기본 콘텐츠 보기로 유지됩니다.

## <a name="enable-local-cache-in-app-service"></a>앱 서비스에서 로컬 캐시 사용
로컬 캐시는 예약된 앱 설정 조합을 사용하여 구성됩니다. 다음과 같은 방법으로 이러한 앱 설정을 구성할 수 있습니다.

* [Azure 포털](#Configure-Local-Cache-Portal)
* [Azure 리소스 관리자](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>방법: Azure 포털을 사용하여 로컬 캐시 구성
<a name="Configure-Local-Cache-Portal"></a>

앱 설정 `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure 포털 앱 설정: 로컬 캐시](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>방법: Azure Resource Manager를 사용하여 로컬 캐시 구성
<a name="Configure-Local-Cache-ARM"></a>

```

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

"properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>로컬 캐시에서 크기 설정 변경
기본적으로 로컬 캐시 크기는 **300MB**입니다. 여기에는 Site 폴더, 콘텐츠 저장소에서 복사된 SiteExtensions 폴더, 로컬로 만든 모든 로그 및 데이터 폴더가 포함됩니다. 이 한도를 늘리려면 앱 설정 `WEBSITE_LOCAL_CACHE_SIZEINMB`를 사용합니다. 웹앱당 최대 **2GB** (2000MB)로 늘릴 수 있습니다.

## <a name="best-practices-for-using-app-service-local-cache"></a>앱 서비스 로컬 캐시 사용에 대한 모범 사례
로컬 캐시는 [스테이징 환경](../app-service/web-sites-staged-publishing.md) 기능과 함께 사용하는 것이 좋습니다.

* 값이 `Always`인 *고정* 앱 설정 `WEBSITE_LOCAL_CACHE_OPTION`을 **프로덕션** 슬롯에 추가합니다. `WEBSITE_LOCAL_CACHE_SIZEINMB`를 사용하는 경우 이것도 프로덕션 슬롯에 고정 설정으로 추가합니다.
* **스테이징** 슬롯을 만들고 사용자의 스테이징 슬롯에 게시합니다. 스테이징 슬롯은 프로덕션 슬롯에 대한 로컬 캐시의 이점을 활용하지만 일반적으로 스테이징 중 원활한 빌드-배포-테스트 수명 주기를 지원하기 위해 로컬 캐시를 사용하지는 않습니다.
* 스테이징 슬롯에 대해 사이트를 테스트합니다.  
* 준비가 되면 스테이징 슬롯과 프로덕션 슬롯 간의 [교환 작업](../app-service/web-sites-staged-publishing.md#Swap)을 실행합니다.  
* 고정 설정은 이름 순이며, 슬롯에 고정됩니다. 스테이징 슬롯이 프로덕션으로 교환되면 로컬 캐시 앱 설정을 상속합니다. 새로 교환된 프로덕션 슬롯은 몇 분 후 로컬 캐시에 대해 실행되며, 교환 후 슬롯 준비의 일부로 준비됩니다. 따라서 슬롯 교환이 완료되면 프로덕션 슬롯이 로컬 캐시에 대해 실행됩니다.

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>내 웹앱에 로컬 캐시가 적용되는지 어떻게 알 수 있나요?
웹앱에 높은 성능의 안정적인 콘텐츠 저장소가 필요하지만 콘텐츠 저장소를 사용하여 런타임에 중요한 데이터를 기록하지 않고 총 크기가 2GB 미만인 경우에는 적용할 수 있습니다. /site 및 /siteextensions 폴더의 총 크기를 확인하려면 사이트 확장 "Azure Web Apps 디스크 사용량"을 사용하면 됩니다.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>사이트가 로컬 캐시를 사용하도록 전환되었는지 어떻게 알 수 있나요?
스테이징 환경에서 로컬 캐시 기능을 사용하는 경우 로컬 캐시가 준비될 때까지 교환 작업이 완료되지 않습니다. 사이트가 로컬 캐시에 대해 실행되고 있는지 알아보려면 작업자 프로세스 환경 변수 `WEBSITE_LOCALCACHE_READY`를 확인하세요. [작업자 프로세스 환경 변수](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) 페이지의 지침을 사용하여 여러 인스턴스에서 작업자 프로세스 환경 변수에 액세스할 수 있습니다.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>방금 새 변경 내용을 게시했지만 웹앱에 없는 것 같습니다. 그 이유는
웹앱에서 로컬 캐시를 사용하는 경우 최신 변경 내용을 가져오려면 사이트를 다시 시작해야 합니다. 프로덕션 사이트에 변경 내용을 게시하고 싶지 않으신가요? 이전 모범 사례 섹션에서 슬롯 옵션을 참조하세요.

### <a name="where-are-my-logs"></a>로그는 어디에 있습니까?
로컬 캐시를 사용하는 경우 로그 폴더와 데이터 폴더가 서로 약간 다르게 표시됩니다. 그러나 하위 폴더의 구조는 하위 폴더가 "고유한 VM 식별자" + 타임스탬프 형식의 하위 폴더 아래에 중첩된다는 점을 제외하고는 동일하게 유지됩니다.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>로컬 캐시를 사용하도록 설정했지만 웹앱이 여전히 다시 시작됩니다. 그 이유는 무엇입니까? 로컬 캐시는 빈번한 앱 다시 시작에 도움이 된다고 생각했습니다.
로컬 캐시는 저장소 관련 웹앱 다시 시작을 방지하는 데 도움이 됩니다. 그러나 VM의 계획된 인프라 업그레이드 중에는 웹앱이 여전히 다시 시작될 수 있습니다. 로컬 캐시를 사용하는 경우에 발생하는 전체 앱 다시 시작은 횟수가 줄어야 합니다.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>로컬 캐시는 더 빠른 로컬 드라이브로 복사할 대상에서 디렉터리를 제외합니까?
저장소 콘텐츠를 복사하는 단계의 일부로 리포지토리로 이름이 지정된 모든 폴더가 제외됩니다. 이는 사용자 사이트 콘텐츠에 웹앱의 일상적인 작업에 불필요할 수도 있는 소스 제어 리포지토리가 포함될 수 있는 시나리오에 유용합니다. 
