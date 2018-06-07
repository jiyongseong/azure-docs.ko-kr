---
title: 역할을 사용하여 Azure 청구에 대한 액세스 관리 | Microsoft Docs
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: vikdesai
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: 7329b06171bd538cc6e9aa8172380a2d4dd47dae
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>역할 기반 액세스 제어를 사용하여 Azure의 요금 청구 정보에 대한 액세스 관리

구독에는 사용자 역할, 즉 계정 관리자, 서비스 관리자, 공동 관리자, 소유자, 참가자, 읽기 권한자 및 청구 읽기 권한자 중 하나를 할당하여 팀의 구성원에게 Azure 청구 정보에 대한 액세스 권한을 부여할 수 있습니다. 이러한 사용자는 [Azure Portal](https://portal.azure.com/)에서 청구 정보에 액세스할 수 있게 되며 [청구 API](billing-usage-rate-card-overview.md)를 사용하여 청구서 및 사용량 정보를 프로그래밍 방식으로 가져올 수 있습니다(옵트인한 경우). 역할을 부여할 수 있는 사람, 역할로 수행할 수 있는 작업에 대한 자세한 내용은 [Azure RBAC의 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="opt-in"></a> 추가 사용자가 청구서에 액세스할 수 있도록 허용

계정 관리자는 [Azure Portal](https://portal.azure.com/)을 사용하여 옵트인(opt in)하고 API를 통해 다른 사용자에 대한 청구서 액세스를 허용해야 합니다.

1. 계정 관리자는 Azure Portal의 [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.

1. **청구서**를 선택하고 **청구서 액세스**를 선택합니다.

    ![청구서에 대한 액세스를 위임하는 방법을 보여 주는 스크린샷](./media/billing-manage-access/AA-optin.png)

1. 액세스를 **설정**한 후 변경 사항을 저장하면 구독 범위 역할의 사용자가 청구서를 다운로드할 수 있습니다.

    ![청구서 액세스에 대한 위임을 설정/해제하는 방법을 보여 주는 스크린샷](./media/billing-manage-access/AA-optinAllow.png)

옵트인(opt in)하면 구독의 서비스 관리자, 공동 관리자, 소유자, 참가자, 읽기 권한자 및 청구 읽기 권한자가 Azure Portal에서 PDF 청구서를 다운로드할 수 있습니다. 그러나 2016년 12월보다 오래된 청구서는 현재 계정 관리자만 사용할 수 있습니다.

또한 계정 관리자는 전자 메일을 통해 청구서가 전송되도록 구성할 수도 있습니다. 자세한 내용은 [전자 메일로 청구서 받기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요.

## <a name="adding-users-to-the-billing-reader-role"></a>청구 읽기 권한자 역할에 사용자 추가

청구 읽기 권한자 역할은 Azure Portal에서 구독 청구 정보에 대해 읽기 전용으로 액세스할 수 있으며 VM 및 저장소 계정 등의 서비스에는 액세스할 수 없습니다. 구독 청구 정보에만 액세스하면 되며 Azure 서비스 관리 기능은 필요하지 않은 사람에게는 청구 읽기 권한자 역할을 할당합니다. 이 역할은 Azure 구독에 대해 재무 및 비용 관리만 수행하는 조직의 사용자에게 적합합니다.

1. Azure Portal의 [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.

1. **Access Control(IAM)** 를 선택하고 **추가**를 클릭합니다.

    ![구독 블레이드에서 IAM을 보여 주는 스크린샷](./media/billing-manage-access/select-iam.PNG)

1. **역할 선택** 페이지에서 **청구 읽기 권한자**를 선택합니다.

    ![팝업 보기에서 청구 읽기 권한자를 보여 주는 스크린샷](./media/billing-manage-access/select-roles.PNG)

1. 초대하려는 사용자의 전자 메일을 입력하고 **확인**을 클릭하여 초대를 보냅니다.

    ![전자 메일을 입력하여 누군가를 초대하는 모습을 보여 주는 스크린샷](./media/billing-manage-access/add-user.PNG)

1. 초대 전자 메일의 지침에 따라 청구 읽기 권한자로 로그인합니다.

    ![청구 읽기 권한자가 Azure Portal에서 볼 수 있는 내용을 보여 주는 스크린샷](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> 청구 읽기 권한자 기능은 미리 보기의 기능이며 엔터프라이즈(EA) 구독 또는 비 글로벌 클라우드는 아직 지원하지 않습니다.

## <a name="adding-users-to-other-roles"></a>다른 역할에 사용자 추가

소유자 또는 참가자 등의 다른 역할의 사용자는 청구 정보에 액세스할 수만 있고 Azure 서비스에는 액세스할 수 없습니다. 이러한 역할을 관리하려면 [구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경](billing-add-change-azure-subscription-administrator.md)을 참조하세요.

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>누가 [계정 센터](https://account.windowsazure.com)에 액세스할 수 있나요?

계정 관리자만 Azure 센터에 로그인할 수 있습니다. 계정 관리자는 구독의 법적 소유자입니다. 다른 사람에게 [구독 소유권이 양도](billing-subscription-transfer.md)되지 않은 한, 기본적으로 Azure 구독에 등록했거나 구입한 사람이 계정 관리자입니다. 계정 관리자는 구독을 만들고, 구독을 취소하고, 구독에 대한 청구 주소를 변경하고, 구독에 대한 액세스 정책을 관리할 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
