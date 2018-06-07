---
title: Azure 스택 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정 | Microsoft Docs
description: 스택에서 Azure 구독에 액세스 하려면 서비스 공급자를 사용 하도록 설정 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Azure 스택 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정

*적용 대상: Azure 스택 시스템 통합*

Azure 스택은 클라우드 서비스 공급자 (CSP) 사용 중인 Azure 스택 및 Azure의 리소스에 액세스 하기 위해 자신의 구독을 관리할 수 있습니다. 사용 하 여 공급자 하도록 할 수도 있습니다. 이 문서에서는 다음 방법을 안내합니다.

 * 구독 한 서비스 공급자 액세스 권한이 부여 됩니다.
 * 서비스 공급자는 서비스를 관리할 수 있는지 확인 합니다.

> [!Note]
>  CSP에는 사용자 계정 관리 되지 않습니다. 다음 단계를 건너뜁니다을 CSP 하기 위해 Azure 스택 구독을 관리할 수 없습니다.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>클라우드 서비스 공급자에 구독 관리

으로 CSP 추가 **사용자** 구독에 있습니다.

1. 테 넌 트 디렉터리에 사용자 역할이 있는 게스트 사용자로 CSP를 추가 합니다.  사용자를 추가 하는 방법에 대 한 단계를 참조 하세요. [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP는 사용자에 대 한 로컬 스택 Azure 구독을 만듭니다.
3. Azure 스택을 사용 하 여 시작할 준비가 되었습니다.
4. CSP은 리소스를 관리할 수도 있습니다를 확인 하기 위해 구독에 리소스를 만들어야 합니다. 예를 들어, 수행할 수 있습니다 [스택 Azure 포털과 Windows 가상 컴퓨터를 만들](azure-stack-quick-windows-portal.md)합니다.

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>RBAC 권한을 사용 하 여 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정

으로 CSP 추가 **소유자** 구독에 있습니다.

1. 테 넌 트 디렉터리에 게스트 사용자로 CSP를 추가 합니다.  사용자를 추가 하는 방법에 대 한 단계를 참조 하세요. [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP 게스트 사용자에 게 소유자 역할을 추가 합니다. CSP 사용자 구독에 추가 하는 방법에 대 한 단계를 참조 하십시오. [Azure 구독 리소스에 대 한 액세스를 관리 하려면 Use Role-Based 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP는 사용자에 대 한 로컬 스택 Azure 구독을 만듭니다.
4. Azure 스택을 사용 하 여 시작할 준비가 되었습니다.
5. CSP은 리소스를 관리할 수 있는지 확인 하기 위해 구독에 리소스를 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

스택에서 Azure 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 참조 [사용 및 청구 Azure 스택의](../azure-stack-billing-and-chargeback.md)합니다.
