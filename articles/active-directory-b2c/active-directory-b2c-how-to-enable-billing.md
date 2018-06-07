---
title: Azure AD B2C에 Azure 구독을 연결하는 방법 | Microsoft Docs
description: Azure AD B2C 테넌트에 대한 요금을 Azure 구독에 청구하는 단계별 가이드입니다.
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 12/05/2017
ms.author: davidmu
ms.openlocfilehash: bb9324b01bb810ba15994612bac2ff20dc83ab82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Azure 구독을 Azure AD B2C 테넌트에 연결

> [!IMPORTANT]
> Azure AD B2C의 사용 요금 청구 및 가격 책정에 대한 최신 정보는 [Azure AD B2C 가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/) 페이지에 있습니다.

Azure AD B2C에 대한 사용 요금은 Azure 구독에 청구됩니다. Azure AD B2C 테넌트가 만들어지면 테넌트 관리자는 Azure AD B2C 테넌트를 Azure 구독에 명시적으로 연결해야 합니다. 이 문서는 방법을 안내합니다.

> [!NOTE]
> Azure AD B2C 테넌트에 연결된 구독은 Azure AD B2C 사용 요금을 청구하는 데만 사용할 수 있습니다. *Azure AD B2C 테넌트 내에서* 다른 Azure 서비스 또는 Office 365 라이선스를 추가하는 데는 구독을 사용할 수 없습니다.

 구독 연결은 대상 Azure 구독 내에 Azure AD B2C “리소스”를 만들어서 이루어집니다. 많은 Azure AD B2C “리소스”는 다른 Azure 리소스(예: VM, 데이터 저장소, LogicApps)와 함께, 단일 Azure 구독 내에서 만들어질 수 있습니다. 구독이 연결된 Azure AD 테넌트로 이동하여 구독 내의 모든 리소스를 볼 수 있습니다.

계속하려면 유효한 Azure 구독이 필요합니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

먼저 구독을 연결하려는 [Azure AD B2C 테넌트를 만들어야](active-directory-b2c-get-started.md) 합니다. 이미 Azure AD B2C 테넌트를 만든 경우에는 이 단계를 건너뜁니다.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Azure 구독을 나타내는 Azure AD 테넌트에서 Azure Portal 열기

Azure 구독을 나타내는 Azure AD 테넌트로 이동합니다. [Azure Portal](https://portal.azure.com)을 열고 사용할 Azure 구독을 나타내는 Azure AD 테넌트로 전환합니다.

![Azure AD 테넌트로 전환](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Azure Marketplace에서 Azure AD B2C 찾기

**리소스 만들기** 단추를 클릭합니다. **마켓플레이스 검색** 필드에 `B2C`를 입력합니다.

![강조 표시된 추가 단추와 마켓플레이스 검색 필드의 Azure AD B2C 텍스트](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

결과 목록에서 **Azure AD B2C**를 선택합니다.

![결과 목록에서 선택된 Azure AD B2C](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Azure AD B2C에 대한 세부 정보가 표시됩니다. 새로운 Azure Active Directory B2C 테넌트 구성을 시작하려면 **만들기** 단추를 클릭합니다.

리소스 만들기 화면에서 **내 Azure 구독에 기존 Azure AD B2C 테넌트 연결**을 선택합니다.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Azure 구독 내에서 Azure AD B2C 리소스 만들기

리소스 만들기 대화 상자의 드롭다운에서 Azure AD B2C 테넌트를 선택합니다. 사용자가 전역 관리자로 있는 모든 테넌트와 아직 구독에 연결되지 않은 테넌트가 표시됩니다.

Azure AD B2C 리소스 이름은 Azure AD B2C 테넌트의 도메인 이름과 일치하도록 미리 선택됩니다.

구독에 대해 사용자가 관리자인 활성 Azure 구독을 선택합니다.

리소스 그룹 및 리소스 그룹 위치를 선택합니다. 여기에서 선택한 항목은 Azure AD B2C 테넌트 위치, 성능 또는 청구 상태에 영향을 주지 않습니다.

![B2C 리소스 만들기](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C 테넌트 리소스 관리

Azure 구독 내에서 Azure AD B2C 리소스를 만든 후 "B2C 테넌트" 유형의 새 리소스가 다른 Azure 리소스와 함께 추가된 것을 볼 수 있습니다.

이 리소스를 사용하여 다음을 수행할 수 있습니다.

- 구독으로 이동하여 청구 정보를 검토합니다.
- Azure AD B2C 테넌트로 이동합니다.
- 지원 요청을 제출합니다.
- Azure AD B2C 테넌트 리소스를 다른 Azure 구독 또는 다른 리소스 그룹으로 이동합니다.

![B2C 리소스 설정](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>알려진 문제

### <a name="csp-subscriptions"></a>CSP 구독

현재, Azure AD B2C 테넌트는 CSP 구독에 연결될 수 **없습니다**.

### <a name="self-imposed-restrictions"></a>자체적으로 적용된 제한 사항

사용자가 Azure 리소스 만들기에 대해 지역별 제한을 구축했을 수 있습니다. 이 제한으로 인해 Azure AD B2C 리소스가 만들어지지 않을 수 있습니다. 유연하게 작업하려면 이러한 제한을 완화하세요.

## <a name="next-steps"></a>다음 단계

각 Azure AD B2C 테넌트에 대해 이러한 단계가 완료되면 Azure 직접 또는 기업 계약 세부 정보에 따라 비용이 Azure 구독에 청구됩니다.

Azure 구독을 선택한 상태에서 사용 현황 및 청구 정보를 검토할 수 있습니다. [사용 현황 보고 API](active-directory-b2c-reference-usage-reporting-api.md)를 사용하여 자세한 일일 사용 현황 보고서 검토할 수도 있습니다.
