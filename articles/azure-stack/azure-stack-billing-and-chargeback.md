---
title: "고객의 요금 청구서 주소와 Azure 스택의 비용 정산 | Microsoft Docs"
description: "스택에서 Azure 리소스 사용 정보를 검색 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="usage-and-billing-in-azure-stack"></a>사용 및 청구 Azure 스택

이 문서에서는 Azure 스택 사용자 리소스 사용량에 대 한 청구 됩니다. 방법을 설명 합니다. 분석 워크 로드와 환불에 대 한 청구 정보 액세스 하는 방법을 배울 수 있습니다.

Azure 스택 수집을 사용 하는 모든 리소스에 대 한 사용 현황 데이터를 그룹화 및 Azure 상거래에이 데이터를 전달 합니다. Azure Commerce을 청구 있습니다 Azure 스택 사용에 대 한 동일한 방식으로 Azure 사용에 대 한 사용자를 청구 합니다.

사용 현황 데이터 및 내보내기에 고유한 결제 또는 요금 청구 어댑터를 사용 하 여 시스템을 백업 또는 Microsoft Power BI와 같은 비즈니스 인텔리전스 도구 내보낸 분석을 위해 사용 하 여 얻을 수 있습니다.


## <a name="usage-pipeline"></a>사용 현황 파이프라인

Azure 스택의 각 리소스 공급자 리소스 사용량 당 사용 현황 데이터를 내보냅니다. 사용 서비스를 정기적으로 (1 시간 마다 및 매일) 사용 현황 데이터를 집계 하 고 사용 데이터베이스에 저장 합니다. Azure 스택 연산자 및 사용자가 Azure 스택 리소스 사용량 Api를 통해 저장 된 사용 현황 데이터를 액세스할 수 있습니다. 

있는 경우 [Azure 스택 인스턴스를 Azure에 등록](azure-stack-register.md), Azure 스택 Azure 상거래를 사용 현황 데이터를 보내도록 구성 합니다. 데이터를 Azure에 업로드 한 후 청구 포털을 통해 또는 Azure 리소스 사용량 Api를 사용 하 여 액세스할 수 있습니다. 참조는 [사용 데이터 보고](azure-stack-usage-reporting.md) 더 어떤 사용에 대 한 데이터를 보고 Azure에 알아보려면 항목입니다.  

다음 이미지는 사용 현황 파이프라인의 주요 구성 요소를 보여 줍니다. 

![사용 현황 파이프라인](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>사용 현황 정보를 찾을 수 있나요, 및 방법?

Azure 스택 리소스 공급자의 경우 계산, 저장소 및 네트워크와 같은 각 구독에 대 한 시간 간격 사용 현황 데이터를 생성합니다. 사용 현황 데이터 사용 수량, 리소스 이름 및 구독을 사용 하는 등 사용 되는 리소스에 대 한 정보를 포함 합니다. 에 미터 ID 리소스에 대 한 자세한 참조는 [사용량 API FAQ](azure-stack-usage-related-faq.md) 문서.

사용 현황 데이터를 수집한 후에 [Azure에 보고 된](azure-stack-usage-reporting.md) Azure 청구 포털을 통해 볼 수 있는 청구서를 생성 하 합니다. 


> [!NOTE]
> 사용 데이터 보고는 Azure 스택 개발 키트용 및 용량 모델에서 구입한 Azure 스택 통합 시스템 사용자에 대 한 필요 하지 않습니다. Azure 스택에서 라이선스에 대 한 자세한 내용은 참조는 [패키징 및 가격](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) 데이터 시트입니다.

Azure 청구 포털 유료 리소스에 대 한 사용 현황 데이터를 보여 줍니다. 유료 리소스 외에도 Azure 스택은 광범위 한 REST Api 또는 PowerShell을 통해 Azure 스택 환경에서 액세스할 수 있는 리소스에 대 한 사용 현황 데이터를 캡처합니다. Azure 스택 운영자 사용자에 대 한 모든 구독에 대 한 사용 현황 데이터를 가져올 수 있습니다. 개별 사용자 에서만 해당 사용 정보를 가져올 수 있습니다. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>다중 테 넌 트 클라우드 서비스 공급자에 대 한 사용 보고

다중 테 넌 트 클라우드 서비스 공급자 (CSP) Azure 스택을 사용 하 여 많은 고객에 게 공급자 다른 Azure 구독에 사용 요금을 부과할 수 없도록 개별적으로 각 고객의 사용을 보고 하기 위해 지정할 수 있습니다. 

다른 Azure Active Directory (Azure AD) 테 넌 트를 나타내는 해당 id를 가진 각 고객 하려고 합니다. Azure 스택 각 Azure AD 테 넌 트에 할당 한 CSP 구독을 지원합니다. 기본 Azure 스택 등록을 위해 테 넌 트 및 해당 구독을 추가할 수 있습니다. 기본 등록 모든 Azure 스택에 대해 수행 됩니다. 테 넌 트에 대 한 구독 등록 되지 않은 경우에 사용자 Azure 스택을 계속 사용할 수 있으며 용도 기본 등록에 사용 되는 구독에 전송 됩니다. 


## <a name="next-steps"></a>다음 단계

[Azure 스택 등록](azure-stack-registration.md)

[Azure에 Azure 스택 사용 현황 데이터를 보고 합니다.](azure-stack-usage-reporting.md)

[공급자 리소스 사용량 API](azure-stack-provider-resource-api.md)

[테 넌 트 리소스 사용량 API](azure-stack-tenant-resource-usage-api.md)

[사용 관련 FAQ](azure-stack-usage-related-faq.md)