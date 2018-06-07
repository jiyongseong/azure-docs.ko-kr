---
title: "Azure 스택 통합 시스템 연결 모델 | Microsoft Docs"
description: "배포 계획 multi-node Azure 스택에 대 한 결정을 확인 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e6c94ef1172ea6380a94d5907c24069ed8c48ff5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure 스택 통합 시스템 연결 모델
이해 해야 Azure 스택 통합 시스템에 관심이 있다면 [여러 데이터 센터에 대 한 통합 고려 사항](azure-stack-datacenter-integration.md) 시스템 데이터 센터에 들어가는지는 방법을 확인 하려면 Azure 스택 배포에 대 한 합니다. 또한 정확 하 게 통합 하는 방법은 됩니다 Azure 스택 하이브리드 클라우드 환경으로 결정 해야 합니다. 이 문서에서는 모델 결정 대금 청구 및 Azure 연결 id 저장소를 포함 하 여 이러한 중요 한 결정에 대 한 개요를 제공 합니다.

통합된 시스템을 구입 하려는 경우 (oem) 하드웨어 공급 업체는 데 도움이 됩니다 대부분 자세히 계획 프로세스의 과정을 안내 합니다. 실제 배포도 수행 합니다.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Azure 스택 배포 연결 모델을 선택 합니다.
Azure 스택 인터넷 (및 Azure) 연결 또는 연결 끊김 배포 하도록 선택할 수 있습니다. Azure 스택 및 Azure 간의 하이브리드 시나리오를 포함 하 여 Azure 스택에서 가장 효과적으로 활용 하는 배포 하려는 Azure에 연결 합니다. 여기에서이 선택한 id 저장소 (Azure Active Directory 또는 Active Directory Federation Services) 및 요금 청구 모델에 사용할 수 있는 옵션을 정의 합니다 (기반 사용 하면 비용을 지불 청구 또는 용량 기반 청구) 다음 다이어그램과 테이블에 요약 된 것 처럼: 

![Azure 스택 배포 및 청구 시나리오](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> 이 클래스는 키 의사 결정 지점! Active Directory Federation Services (AD FS) 또는 Azure Active Directory (Azure AD)를 선택 하는 것은 배포 시에 수행 해야 하는 일회성 결정입니다. 전체 시스템을 다시 배포 하지 않고이 작업을 나중에 변경할 수 없습니다.  


|옵션|Azure에 연결|Azure에서 분리|
|-----|-----|-----|
|Azure AD|![지원됨](media/azure-stack-connection-models/check.png)| |
|AD FS|![지원됨](media/azure-stack-connection-models/check.png)|![지원됨](media/azure-stack-connection-models/check.png)|
|사용량 기반 요금 청구|![지원됨](media/azure-stack-connection-models/check.png)| |
|용량 기반 요금 청구|![지원됨](media/azure-stack-connection-models/check.png)|![지원됨](media/azure-stack-connection-models/check.png)|
|Azure 스택에 직접 업데이트 패키지 다운로드|![지원됨](media/azure-stack-connection-models/check.png)|  |

Azure 스택 배포에 사용할 Azure 연결 모델에서 결정 하 고 한 다음 추가 연결 기반의 의사 결정은 id 저장소 및 결제 방법에 대해 수행 되어야 합니다. 

## <a name="next-steps"></a>다음 단계

[Azure의 연결 된 Azure 스택 배포 결정 사항](azure-stack-connected-deployment.md)

[Azure 연결 되지 않은 Azure 스택 배포 결정 사항](azure-stack-disconnected-deployment.md)
