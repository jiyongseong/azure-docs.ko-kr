---
title: "Azure Portal에서 Service Bus 네임스페이스를 만드는 방법 | Microsoft Docs"
description: "Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: a24fa21848005d9768d26ae865680a4851e1dd81
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Azure Portal을 사용하여 Service Bus 네임스페이스 만들기

네임스페이스는 모든 메시징 구성 요소에 대한 영역 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스 내에 있을 수 있으며 네임스페이스는 종종 응용 프로그램 컨테이너로 사용됩니다. 두 가지 방법으로 Service Bus 네임스페이스를 만들 수 있습니다.

1. Azure Portal(이 문서)
2. [Resource Manager 템플릿][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal에서 네임스페이스 만들기

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

축하합니다! 이제 Service Bus 메시징 네임스페이스를 만들었습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 더 많은 고급 기능 중 일부를 보여 주는 [GitHub 샘플][github-samples]을 확인합니다.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
