---
title: "App Service Environment에 대한 사용자 지정 설정"
description: "App Service Environment에 대한 사용자 지정 구성 설정"
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2017
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service Environment에 대한 사용자 지정 구성 설정
## <a name="overview"></a>개요
App Service Environment는 단일 고객에게 격리되므로 App Service Environment에만 적용할 수 있는 특정 구성 설정이 있습니다. 이 문서에서는 App Service Environment에 사용 가능한 다양한 App Service Environment 관련 사용자 지정 항목을 설명합니다.

App Service Environment가 없는 경우 [App Service Environment를 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md)을 참조하세요.

새 **clusterSettings** 특성의 배열을 사용하여 App Service Environment 사용자 지정을 저장할 수 있습니다. 이 특성은 *hostingEnvironments* Azure Resource Manager 엔터티의 "속성" 사전에 있습니다.

다음 약식 리소스 관리자 템플릿 코드 조각에는 **clusterSettings** 특성이 표시됩니다.

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

**clusterSettings** 특성을 리소스 관리자 템플릿에 포함하여 App Service Environment를 업데이트할 수 있습니다.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Azure 리소스 탐색기를 사용하여 App Service Environment 업데이트
또는 [Azure 리소스 탐색기](https://resources.azure.com)를 사용하여 App Service Environment를 업데이트할 수 있습니다.  

1. 리소스 탐색기에서 App Service Environment 노드(**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**)로 이동합니다. 그런 다음 업데이트하려는 특정 App Service Environment를 클릭합니다.
2. 오른쪽 창의 위쪽 도구 모음에서 **읽기/쓰기** 를 클릭하여 리소스 탐색기에서 대화형 편집을 허용합니다.  
3. 파란색 **편집** 단추를 클릭하여 리소스 관리자 템플릿을 편집 가능하도록 설정합니다.
4. 오른쪽 창의 아래쪽으로 스크롤합니다. **clusterSettings** 특성은 거의 맨 아래쪽에 있습니다. 여기에서 해당 값을 입력하거나 업데이트할 수 있습니다.
5. **clusterSettings** 특성에서 원하는 구성 값 배열을 입력하거나 복사하여 붙여넣습니다.  
6. 오른쪽 창의 위쪽에 있는 녹색 **PUT** 단추를 클릭하여 App Service Environment에 대한 변경 내용을 커밋합니다.

변경 내용을 제출한 후 변경 내용이 적용되려면 App Service Environment의 프런트 엔드 수에 약 30 분을 곱한 만큼의 시간이 필요합니다.
예를 들어 App Service Environment에 4개의 프런트 엔드가 있는 경우 구성 업데이트를 완료하는 데 약 2시간이 걸립니다. 구성 변경이 롤아웃되는 동안 App Service Environment에서 다른 크기 조정 작업 또는 구성 변경 작업을 수행할 수 없습니다.

## <a name="disable-tls-10"></a>TLS 1.0 사용 안 함
고객, 특히 PCI 규정 준수 감사를 다루는 고객으로부터의 되풀이되는 질문은 자신의 앱에 TLS 1.0을 명시적으로 사용하지 않도록 설정하는 방법입니다.

다음 **clusterSettings** 항목을 통해 TLS 1.0을 사용하지 않도록 설정할 수 있습니다.

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>TLS 암호화 그룹 순서 변경
고객의 또 다른 질문은 고객이 자신의 서버와 협의한 암호화 목록을 수정할 수 있는지에 대한 것으로 아래 표시된 것처럼 **clusterSettings** 를 수정하여 이 작업을 수행할 수 있습니다. 사용 가능한 암호화 그룹 목록은 [이 MSDN 문서](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx)에서 검색할 수 있습니다.

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> 암호화 그룹에 대해 SChannel에서 이해할 수 없는 잘못된 값이 설정된 경우 서버에 대한 모든 TLS 통신의 작동이 중지될 수 있습니다. 이러한 경우 *clusterSettings* 에서 **FrontEndSSLCipherSuiteOrder** 항목을 제거하고 업데이트된 Resource Manager 템플릿을 제출하여 기본 암호화 제품군 설정으로 되돌려야 합니다.  이 기능을 주의하여 사용하세요.
> 
> 

## <a name="get-started"></a>시작
Azure 빠른 시작 Resource Manager 템플릿 사이트에는 [App Service Environment 만들기](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)에 대한 기본 정의에 템플릿이 포함되어 있습니다.

<!-- LINKS -->

<!-- IMAGES -->
