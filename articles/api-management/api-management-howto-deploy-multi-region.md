---
title: 여러 Azure 지역에 Azure API Management 서비스 배포 | Microsoft Docs
description: 여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ff0101bde54f99f99461d0f042af520b1642d0df
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법
API Management는 원하는 Azure 지역의 수에 상관 없이 단일 API Management 서비스를 배포하기 위해 API 게시자를 사용할 수 있는 여러 지역 배포를 지원합니다. 이를 통해 지역적으로 배포된 API 소비자가 느끼는 요청 대기 시간을 줄일 수 있으며 한 지역이 오프라인인 경우 가능한 서비스를 개선할 수도 있습니다. 

API Management 서비스가 처음 만들어질 때 한 [단위][unit]만 포함하며 단일 Azure 지역에 있어 기본 지역으로 지정됩니다. Azure Portal을 통해 추가 지역을 쉽게 추가할 수 있습니다. API Management 게이트웨이 서버는 각 지역에 배포되고 호출 트래픽이 가장 가까운 게이트웨이에 라우팅됩니다. 지역이 오프라인 상태가 되면 다음으로 가장 가까운 게이트웨이에 트래픽이 자동으로 리디렉션됩니다. 

> [!IMPORTANT]
> 다중 지역 배포는 **[프리미엄][Premium]** 계층에서만 사용할 수 있습니다.
> 
> 

## <a name="add-region"></a>새 지역에 API Management 서비스 인스턴스 배포
> [!NOTE]
> 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.
> 
> 

Azure Portal에서 API Management 서비스 인스턴스에 대한 **크기 조정 및 가격 책정** 탭으로 이동합니다. 

![크기 조정 탭][api-management-scale-service]

새 지역에 배포하려면 도구 모음에서 **+ 지역 추가**를 클릭합니다.

![지역 추가][api-management-add-region]

드롭다운 목록에서 위치를 선택하고 슬라이더를 사용하여 단위 수를 설정합니다.

![단위 지정][api-management-select-location-units]

**추가**를 클릭하여 위치 테이블에 선택 항목을 배치합니다. 

모든 위치를 구성할 때까지 이 프로세스를 반복한 후 도구 모음에서 **저장**을 클릭하여 배포 프로세스를 시작합니다.

## <a name="remove-region"></a>위치에 API Management 서비스 인스턴스 삭제

Azure Portal에서 API Management 서비스 인스턴스에 대한 **크기 조정 및 가격 책정** 탭으로 이동합니다. 

![크기 조정 탭][api-management-scale-service]

제거할 위치에 대해 테이블의 오른쪽 끝에 있는 **...** 단추를 사용하여 상황에 맞는 메뉴를 엽니다. **삭제** 옵션을 선택합니다.

삭제를 확인하고 **저장**을 클릭하여 변경 내용을 적용합니다.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

