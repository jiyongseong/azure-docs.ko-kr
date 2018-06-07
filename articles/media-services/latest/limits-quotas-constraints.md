---
title: Azure Media Services v3의 할당량 및 제한 사항 | Microsoft Docs
description: 이 토픽은 Azure Media Services v3의 할당량 및 제한 사항에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure Media Services v3의 할당량 및 제한 사항

이 토픽은 Azure Media Services v3의 할당량 및 제한 사항에 대해 설명합니다.

| 리소스 | 기본 제한 | 
| --- | --- | 
| Azure Media Services 계정당 자산 | 1,000,000|
| 작업당 JobInputs | 100 |
| 작업당 JobOutputs | 30(고정) |
| 파일 크기| 일부 시나리오에서는 Media Services에서 처리를 위해 지원되는 최대 파일 크기에 제한이 있습니다. <sup>(1)</sup> |
| Media Services 계정 작업당 작업 | 50,000<sup>(2)</sup> |
| Media Services 계정당 LiveEvents |5|
| 단일 구독의 Media Services 계정 | 25(고정) |
| StreamingPolicies | 1,000,000<sup>(3)</sup> |
| LiveEvent당 실행 중인 상태의 LiveOutputs |3|
| LiveEvent 당 정지 상태인 LiveOutputs |50|
| Storage 계정 | 1,000<sup>(4)</sup>(고정) |
| Media Services 계정당 실행 상태인 스트리밍 엔드포인트|2|
| Media Services 계정당 변환 | 20 |
| 한번에 자산과 연결된 고유 StreamingLocators | 20<sup>(5)</sup> |
  
<sup>1</sup>현재 Azure Blob Storage에서 단일 blob에 대해 지원되는 최대 크기는 최대 5TB입니다. 그러나 서비스에서 사용되는 VM 크기에 따라 Azure Media Services에 추가 제한이 적용됩니다. 원본 파일이 260GB보다 크면 작업이 실패할 수 있습니다. 260GB 제한보다 큰 4K 콘텐츠가 있는 경우 시나리오 지원을 위해 amshelp@microsoft.com에 잠재적인 완화 방법을 문의하세요.

<sup>2</sup> 이 번호에 대기 중, 완료, 활성 및 취소된 작업도 포함됩니다. 삭제된 작업은 포함되지 않습니다. 

레코드의 총 수가 최고 할당량 미만인 경우에도 사용자 계정에 있는 90일이 지난 작업 레코드는 자동으로 삭제됩니다. 

<sup>3</sup>다양한 Media Services 정책(예: StreamingLocator 정책 또는 ContentKeyAuthorizationPolicy의 경우)에 대해 1,000,000개 StreamingPolicy 정책으로 제한됩니다. 

>[!NOTE]
> 항상 동일한 날짜/액세스 권한 등을 사용하는 경우 동일한 정책 ID를 사용해야 합니다. 

<sup>4</sup> 저장소 계정은 동일한 Azure 구독에 속해 있어야 합니다.

<sup>5</sup> StreamingLocators는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 서로 다른 액세스 권한을 부여하려면 DRM(Digital Rights Management)솔루션을 사용 합니다.

## <a name="support-ticket"></a>지원 티켓

고정되지 않는 리소스의 경우 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 열어 발생되는 할당량을 요청할 수 있습니다. 원하는 할당량 변경, 사용 사례 시나리오 및 필요한 영역에 대한 세부 정보를 요청에 포함합니다. <br/>더 높은 한도를 얻기 위해 추가 Azure Media Services 계정을 만들지 **마십시오**.

## <a name="next-steps"></a>다음 단계

[개요](media-services-overview.md)
