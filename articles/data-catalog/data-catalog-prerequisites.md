---
title: "Azure 데이터 카탈로그의 필수 구성 요소 | Microsoft Docs"
description: "Azure Data Catalog 시작에 필요한 필수 구성 요소에 대해 알아봅니다."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: a7effaaaeb23661b9be96dcddc2c140ab8c8b92b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-prerequisites"></a>Azure 데이터 카탈로그의 필수 구성 요소

Azure Data Catalog를 설정하기 전에 몇 가지 사항을 주의해야 합니다. 걱정하지 마세요. 이 프로세스는 오래 걸리지 않습니다.

## <a name="azure-subscription"></a>Azure 구독
데이터 카탈로그를 설정하려면 Azure 구독의 소유자 또는 공동 소유자여야 합니다.

Azure 구독에서는 데이터 카탈로그와 같은 클라우드 서비스 리소스에 대한 액세스를 구성할 수 있습니다. 구독은 리소스 사용을 보고하고, 요금을 청구하고, 지불하는 방식을 제어할 수도 있습니다. 각 구독은 별도 청구 및 지불 설정을 가질 수 있으므로 부서, 프로젝트, 지사 등에 따라 다른 구독 및 계획이 있을 수 있습니다. 모든 클라우드 서비스는 구독에 속하고 데이터 카탈로그를 설정하기 전에 구독을 보유해야 합니다. 자세한 내용은 [계정, 구독 및 관리 역할 관리](../active-directory/active-directory-assign-admin-roles-azure-portal.md)를 참조하세요.

## <a name="azure-active-directory"></a>Azure Active Directory
데이터 카탈로그를 설정하려면 Azure AD(Azure Active Directory) 사용자 계정으로 로그인해야 합니다.

Azure AD는 클라우드 및 온-프레미스 모두에서 비즈니스가 ID와 액세스를 쉽게 관리하는 방법을 제공합니다. 사용자는 클라우드 및 온-프레미스 웹 응용 프로그램에 SSO(Single Sign-on)를 위해 단일 회사 또는 학교 계정을 사용할 수 있습니다. 데이터 카탈로그는 로그인 인증에 Azure AD를 사용합니다. 자세히 알아보려면 [Azure Active Directory란?](../active-directory/active-directory-whatis.md)을 참조하세요.

> [!NOTE]
> [Azure Portal](http://portal.azure.com/)을 사용하여 사용자는 개인 Microsoft 계정 또는 Azure Active Directory 작업 또는 학교 계정으로 로그인할 수 있습니다. Azure Portal을 사용하거나 [데이터 카탈로그 포털](http://www.azuredatacatalog.com)을 사용하여 데이터 카탈로그를 설정하려면 개인 계정이 아닌, Azure Active Directory 계정을 사용하여 로그인해야 합니다.
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory 정책 구성
데이터 카탈로그 포털에 로그인할 수 있는 상황이 발생할 수 있지만, 데이터 원본 등록 도구에 로그인을 시도할 때 로그인하지 않도록 하는 오류 메시지가 발생합니다. 이 문제 동작은 사용자가 회사 네트워크에 있는 경우 또는 회사 네트워크 외부에서 연결하는 경우에만 발생할 수 있습니다.

데이터 원본 등록 도구가 폼 인증을 사용하여 Active Directory에 대한 사용자 자격 증명의 유효성 검사를 수행합니다. 로그인이 성공하려면 Azure Active Directory 관리자가 전역 인증 정책에서 폼 인증을 사용할 수 있도록 해야 합니다.

전역 인증 정책에서 다음 스크린샷에 나와 있는 것처럼 인트라넷 및 엑스트라넷 연결을 위해 인증 방법을 개별적으로 사용하도록 설정할 수 있습니다. 연결되어 있는 네트워크에 대해 폼 인증을 사용할 수 없는 경우 로그인 오류가 발생할 수 있습니다.

 ![Active Directory 전역 인증 정책](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>다음 단계
자세한 내용은 [인증 정책 구성](https://technet.microsoft.com/library/dn486781.aspx)을 참조하세요.
