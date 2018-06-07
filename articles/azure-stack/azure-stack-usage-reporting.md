---
title: Azure에 Azure 스택 사용 데이터 보고 | Microsoft Docs
description: Azure 스택의 보고 기능 사용 현황 데이터를 설정 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: daaaf6c574c4b169c19ebec42ad68e2d818ca1cb
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603705"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Azure에 Azure 스택 사용 현황 데이터를 보고 합니다. 

사용 데이터를 소비 데이터 라고도 사용한 리소스의 크기를 나타냅니다. 

사용량 기반 청구 모델을 사용 하는 azure 스택 다중 노드 시스템 청구 목적을 위해 Azure에 사용 현황 데이터를 보고 해야 합니다.  Azure 스택 운영자는 해당 Azure 스택 인스턴스가 Azure에 사용 데이터 보고를 구성 해야 합니다.

> [!NOTE]
> 사용 데이터 보고는 사용량 기준 과금으로-있습니다-사용 가능한 모델에서 허가 받은 Azure 스택 multi-node 사용자 필요 합니다. 용량 모델을 구입한 고객에 대 한 선택 사항 (참조는 [페이지를 구입 하는 방법](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)합니다. Azure 스택 개발 키트 사용자에 대 한 Azure 스택 운영자는 사용 현황 데이터를 보고 하 고 기능 테스트 수 있습니다. 그러나 사용자가 그로 인해 발생 하는 모든 사용에 대 한 청구 되지 됩니다. 


![청구 흐름](media/azure-stack-usage-reporting/billing-flow.png)

사용 현황 데이터는 Azure 스택에서 Azure 브리지를 통해 Azure에 전송 됩니다. Azure에서 상거래 시스템 사용 현황 데이터를 처리 하 고 청구서를 생성 합니다. Azure 구독 소유자를 볼 있고 사이트에서 다운로드할 청구서를 생성 한 후의 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)합니다. Azure 스택은 허가 하는 방법에 대해 알아보려면 참조는 [Azure 스택 패키징 및 문서 가격](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409)합니다.

## <a name="set-up-usage-data-reporting"></a>사용 데이터 보고 설정

사용 데이터 보고를 설정 하려면 [Azure를 사용한 Azure 스택 인스턴스 등록](azure-stack-register.md)합니다. Azure 스택 Azure에 연결 하 고 사용 현황 데이터를 전송 하는 Azure 스택의 Azure 브리지 구성 요소는 등록 프로세스의 일부로 구성 됩니다. 다음 사용 현황 데이터는 Azure에 Azure 스택에서 전송 됩니다.

- **ID 미터링** – 리소스 사용에 대 한 고유 ID입니다.
- **수량** – 리소스 사용량입니다.
- **위치** – 현재 스택에서 Azure 리소스를 배포 하는 위치입니다.
- **리소스 URI** – 정규화 된 사용량이 보고 되는 리소스의 URI입니다.
- **구독 ID** – 로컬 (Azure 스택) 구독이 Azure 스택 사용자의 구독 ID입니다.
- **시간** – 사용 현황 데이터의 시작 시간과 종료 시간입니다. 약간의 지연 시간 사이의 Azure 스택에 사용 된 이러한 리소스는 사용 현황 데이터를 상거래에 보고 하는 경우. Azure 스택 집계 사용 현황 데이터를 24 시간 마다 고 commerce 파이프라인 Azure에 사용 현황 데이터를 보고 다른 몇 시간을 이동합니다. 따라서 자정 되기 바로 전에 발생 하는 경우에 표시 될 Azure는 다음 날입니다.

## <a name="generate-usage-data-reporting"></a>사용 데이터 보고를 생성 합니다.

1. 보고 사용 현황 데이터를 테스트 하려면 Azure 스택 몇 가지 리소스를 만듭니다. 예를 들어 만들 수 있습니다는 [저장소 계정](azure-stack-provision-storage-account.md), [Windows Server VM](azure-stack-provision-vm.md) 및 코어 사용이 보고 되는 방법을 보려면 Basic 및 Standard Sku를 사용 하 여 Linux VM입니다. 여러 유형의 리소스에 대 한 사용 현황 데이터는 다른 측정 단위에서 보고 됩니다.

2. 몇 시간 동안 실행 하 여 리소스를 유지 합니다. 사용 정보는 약 1 시간 마다 수집 됩니다. 를 수집 했으면이 데이터는 Azure로 전송 되 고 Azure 상거래 시스템에서 처리 합니다. 이 프로세스는 몇 시간 정도 걸릴 수 있습니다.

## <a name="view-usage---csp-subscriptions"></a>사용-CSP 구독 보기

Azure 스택 CSP 구독을 사용 하 여 등록 한 경우 같은 방식으로 Azure 사용량을 볼 수 있는 사용 현황 및 요금을 볼 수 있습니다. Azure 스택 사용 조정 파일을 통해 사용할 수 있는 및 청구서에 포함 됩니다. [파트너 센터](https://partnercenter.microsoft.com/partner/home)합니다. 조정 파일 매월 업데이트 됩니다. 최근 Azure 스택 사용 정보에 액세스 해야 할 경우에 파트너 센터 Api를 사용할 수 있습니다.

   ![파트너 센터](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>사용 현황 보기 – 기업 계약 구독

사용 및 청구 금액은 볼 수는 기업 계약 구독을 사용 하 여 Azure 스택을 등록 한 경우는 [EA 포털](https://ea.azure.com/)합니다. 스택 사용 azure EA 포털에서 보고서 섹션 아래의 Azure 사용량 함께 고급 다운로드에 포함 됩니다. 

## <a name="view-usage--other-subscriptions"></a>사용 현황 보기 – 다른 구독

Azure 스택 다른 구독 형식의 예를 들어 선불 구독을 사용 하 여 등록 한 경우 Azure 계정 센터에서 사용 현황 및 요금을 볼 수 있습니다. 에 로그인 하 고 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions) Azure 계정 관리자 및 Azure 스택을 등록 하는 데 사용 하는 Azure 구독을 선택 합니다. 다음 그림에 나와 있는 것 처럼 각 사용 되는 리소스에 대 한 청구 Azure 스택 사용 현황 데이터를 볼 수 있습니다.

   ![청구 흐름](media/azure-stack-usage-reporting/pricing-details.png)

Azure 스택 개발 키트에 대 한 Azure 스택 리소스는 가격이 $0.00으로 표시 되 고 있으므로 청구 되지 않습니다.

## <a name="which-azure-stack-deployments-are-charged"></a>있는 Azure 스택 배포 요금이 청구 됩니다.

리소스 사용량은 Azure 스택 개발 키트에 대 한 무료입니다. 반면 Azure 스택 multi-node 시스템에 대 한 작업 부하 Vm, 저장소 서비스 및 응용 프로그램 서비스 요금이 청구 됩니다.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>사용자가 Vm 인프라에 대 한 요금이 청구 됩니다.

번호 Vm에 일부 Azure 스택 리소스 공급자에 대 한 사용 현황 데이터를 Azure에 보고 하지만 이러한 Vm에 대 한 나이 Azure 스택 인프라를 사용 하도록 설정 하려면 배포 하는 동안 만들어진 Vm에는 요금이 청구 되지 합니다.  

사용자가 테 넌 트 구독에서 실행 되는 Vm에 대해서만 요금이 부과 됩니다. Azure 스택의 라이선스 계약 준수에 대 한 테 넌 트 구독 아래의 모든 워크 로드 배포 되어야 합니다.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Azure 스택에서 사용 하려는 하는 경우 Windows Server 라이선스 아니오, 어떻게 하나요?

기존 라이선스를 사용 하 여 사용 미터 생성을 방지 합니다. "Azure 스택 기존 소프트웨어 사용" 섹션에 설명 된 대로 Azure 스택에서 기존 Windows Server 라이선스를 사용할 수 있습니다 [Azure 스택 라이선스 가이드](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409)합니다. 고객에 설명 된 대로 Windows Server 가상 컴퓨터를 배포할 필요가 [Windows Server 라이선스에 대 한 하이브리드 혜택](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 하 여 기존 라이선스를 사용 하려면 문서입니다.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>구독 리소스를 사용 요금이 부과 됩니다.
때 제공 되는 구독 [Azure를 사용한 Azure 스택 등록](azure-stack-register.md) 요금이 부과 됩니다.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>어떤 유형의 구독 사용 데이터 보고에 대 한 지원 됩니까?

Azure 스택 multinode EA (기업 계약) 및 CSP 구독이 지원 됩니다. EA (기업 계약), 종 량 제, CSP 및 MSDN 구독 Azure 스택 개발 키트에 대 한 사용 데이터 보고를 지원 합니다.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>사용 데이터 보고 통치 클라우드에서 작업 합니까?

Azure 스택 개발 키트 사용 데이터 보고 글로벌 Azure 시스템에서 만든 구독 해야 합니다. 통치 클라우드 (Azure Government, Azure 독일 및 Azure 중국 클라우드) 중 하나에서 만들어진 구독 사용 데이터 보고를 지원 하지 하므로 azure에 등록할 수 없습니다.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>사용자 수는 Azure 결제 포털에서 Azure 스택 사용 현황 데이터를 식별 하는 방법

사용 현황 세부 정보 파일에 Azure 스택 사용 현황 데이터를 볼 수 있습니다. 사용 현황 세부 정보 파일을 가져오는 방법에 대해 알아야 참조는 [Azure 계정 센터 문서에서 사용 현황 파일 다운로드](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv)합니다. 사용 현황 세부 정보 파일에는 Vm 및 Azure 스택 저장소 식별 하는 Azure 스택 미터 포함 되어 있습니다. "Azure 스택 합니다." 라는 지역에서 Azure 스택에서 사용 되는 모든 리소스는 보고 되는

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Azure 스택에 보고를 사용 하지 않는 r e 계정 센터에서 생성 된 보고서를 일치 하는 이유

Azure 스택 Api 사용 하 여 보고 되는 사용 현황 데이터과 Azure 계정 센터에서 보고 한 사용 현황 데이터는 항상입니다. 이 지연 시간은 Azure 상거래 Azure 스택에서 사용 현황 데이터를 업로드 하는 데 필요한 시간입니다. 이 지연으로 인해 자정 되기 바로 전에 발생 하는 경우에 표시 될 Azure는 다음 날입니다. 사용 하는 경우는 [Azure 스택 사용량 Api](azure-stack-provider-resource-api.md), Azure 청구 포털에서 보고 사용에 대 한 결과 비교 하 고, 차이 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [공급자 사용량 API](azure-stack-provider-resource-api.md)  
* [테넌트 사용량 API](azure-stack-tenant-resource-usage-api.md)
* [사용 FAQ](azure-stack-usage-related-faq.md)
* [사용을 관리 및 클라우드 서비스 공급자와 요금 청구](azure-stack-add-manage-billing-as-a-csp.md)
