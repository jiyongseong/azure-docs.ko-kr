---
title: "Azure CDN 리소스의 상태 모니터링 | Microsoft Docs"
description: "Azure 리소스 상태를 사용하여 Azure CDN 리소스의 상태를 모니터링하는 방법을 알아봅니다."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Azure CDN 리소스의 상태 모니터링
  
Azure CDN 리소스 상태는 [Azure 리소스 상태](../resource-health/resource-health-overview.md)의 하위 집합입니다.  Azure 리소스 상태를 사용하여 CDN 리소스의 상태를 모니터링하고 문제 해결을 위한 실행 지침을 받을 수 있습니다.

>[!IMPORTANT] 
>Azure CDN 리소스 상태는 현재 글로벌 CDN 배달 및 API 기능의 상태만 고려합니다.  Azure CDN 리소스 상태는 개별 CDN 끝점을 확인하지 않습니다.
>
>Azure CDN 리소스 상태를 피드하는 신호는 최대 15분 지연될 수 있습니다.

## <a name="how-to-find-azure-cdn-resource-health"></a>Azure CDN 리소스 상태를 찾는 방법

1. [Azure Portal](https://portal.azure.com)에서 CDN 프로필로 이동합니다.

2. **Settings** 단추를 클릭합니다.

    ![설정 단추](./media/cdn-resource-health/cdn-profile-settings.png)

3. *지원 + 문제 해결*에서 **리소스 상태**를 클릭합니다.

    ![CDN 리소스 상태](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>*도움말 + 지원* 블레이드의 *리소스 상태* 타일에 나열된 CDN 리소스를 찾을 수도 있습니다.  포털 오른쪽 위에 있는 원 안에 표시된 **?**를 클릭하여 *도움말 + 지원*으로 신속하게 이동할 수 있습니다.
>
> ![도움말 + 지원](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN 관련 메시지

아래에서 Azure CDN 리소스 상태와 관련된 상태를 확인할 수 있습니다.

|Message | 권장 작업 |
|---|---|
|하나 이상의 CDN 끝점을 중지, 제거 또는 잘못 구성했을 수 있습니다. | 하나 이상의 CDN 끝점을 중지, 제거 또는 잘못 구성했을 수 있습니다.|
|죄송합니다. CDN 관리 서비스를 현재 사용할 수 없습니다. | 여기에서 상태 업데이트를 다시 확인하세요. 예상 해결 시간 이후에도 문제가 지속되는 경우 지원에 문의하세요.|
|죄송합니다. CDN 끝점이 일부 CDN 공급자와 관련하여 진행 중인 문제로 인해 영향을 받을 수 있습니다. | 여기에서 상태 업데이트를 다시 확인하세요. 문제 해결 도구를 사용하여 원본 및 CDN 끝점을 테스트하는 방법을 확인할 수 있습니다. 예상 해결 시간 이후에도 문제가 지속되는 경우 지원에 문의하세요. |
|죄송합니다. CDN 끝점 구성 변경 사항에 전파 지연이 발생했습니다. | 여기에서 상태 업데이트를 확인하세요. 구성 변경 내용이 예상 시간 내에 완전히 전파되지 않는 경우 지원에 문의하세요.|
|죄송합니다. 보조 포털을 로드하는 동안 문제가 발생했습니다. | 여기에서 상태 업데이트를 다시 확인하세요. 예상 해결 시간 이후에도 문제가 지속되는 경우 지원에 문의하세요.|
죄송합니다. 일부 CDN 공급자에게 문제가 발생했습니다. | 여기에서 상태 업데이트를 다시 확인하세요. 예상 해결 시간 이후에도 문제가 지속되는 경우 지원에 문의하세요. |

## <a name="next-steps"></a>다음 단계

- [Azure 리소스 상태 개요 읽기](../resource-health/resource-health-overview.md)
- [CDN 압축 관련 문제 해결](./cdn-troubleshoot-compression.md)
- [404 오류 관련 문제 해결](./cdn-troubleshoot-endpoint.md)