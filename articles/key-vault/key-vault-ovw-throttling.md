---
title: Azure Key Vault 제한 지침
description: Key Vault 제한은 리소스의 과용을 방지하기 위해 동시 호출 수를 제한합니다.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 제한 지침

제한은 리소스의 과용을 방지하기 위해 Azure 서비스에 대한 동시 호출 수를 제한하는 사용자 시작 프로세스입니다. Azure Key Vault(AKV)는 많은 양의 요청을 처리하도록 설계되었습니다. 매우 많은 수의 요청이 발생할 경우 클라이언트의 요청을 제한하면 AKV 서비스의 최적의 성능 및 안정성을 유지하는 데 도움이 됩니다.

제한 한도는 시나리오에 따라 다릅니다. 예를 들어, 대용량 쓰기를 수행하는 경우 읽기만 수행하는 경우보다 제한 가능성이 높습니다.

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault가 한도를 처리하는 방법

Key Vault의 서비스 한도는 리소스의 오용을 방지하고 모든 Key Vault 클라이언트의 서비스 품질을 보장하기 위한 것입니다. 서비스 임계값이 초과되면 Key Vault가 해당 클라이언트에서 들어오는 추가 요청을 일정 시간 동안 제한합니다. 이 경우 Key Vault는 HTTP 상태 코드 429(너무 많은 요청)를 반환하고 요청은 실패합니다. 또한 429를 반환한 실패한 요청은 Key Vault에서 추적하는 제한 한도에 포함됩니다. 

더 높은 제한 한도에 대한 유효한 비즈니스 사례가 있을 경우 Microsoft에 연락해 주세요.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>서비스 한도에 대응하여 앱을 제한하는 방법

다음은 앱 제한에 대한 **모범 사례**입니다.
- 요청당 작업 수를 줄입니다.
- 요청의 빈도를 줄입니다.
- 즉시 재시도를 방지합니다. 
    - 모든 요청은 사용량 한도에 누적됩니다.

앱의 오류 처리를 구현할 때는 HTTP 오류 코드 429를 사용하여 클라이언트 측 제한이 필요한지 감지하세요. HTTP 429 오류 코드와 함께 요청이 다시 실패하더라도 Azure 서비스 한도에 도달하게 됩니다. 권장되는 클라이언트 측 제한 방법을 계속 사용하여 성공할 때까지 요청을 다시 시도해 보세요.

### <a name="recommended-client-side-throttling-method"></a>권장되는 클라이언트 측 제한 방법

HTTP 오류 코드 429가 발생할 경우 지수 백오프 접근법을 사용하여 클라이언트 제한을 시작하세요.

1. 1초를 기다렸다가 요청을 다시 시도합니다.
2. 그래도 제한된 경우 2초를 기다렸다가 요청을 다시 시도합니다.
3. 그래도 제한된 경우 4초를 기다렸다가 요청을 다시 시도합니다.
4. 그래도 제한된 경우 8초를 기다렸다가 요청을 다시 시도합니다.
5. 그래도 제한된 경우 16초를 기다렸다가 요청을 다시 시도합니다.

이때 HTTP 429 응답 코드가 발생해서는 안 됩니다.

## <a name="see-also"></a>참고 항목

Microsoft Cloud의 제한에 대한 더 자세한 소개는 [제한 패턴](https://docs.microsoft.com/azure/architecture/patterns/throttling)을 참조하세요.

