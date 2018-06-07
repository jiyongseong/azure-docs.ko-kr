---
title: "사용자 지정 개발 응용 프로그램에 대해 필요한 특정 API를 찾는 방법 | Microsoft Docs"
description: "사용자 지정 개발 Azure AD 응용 프로그램에서 특정 API에 액세스하는 데 필요한 권한을 구성하는 방법"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e3d15db2e00c2cd5b52dd6c486f61ee88d922d79
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>사용자 지정 개발 응용 프로그램에 대해 필요한 특정 API를 찾는 방법

API에 액세스하려면 액세스 범위 및 역할을 구성해야 합니다. 클라이언트 응용 프로그램에 리소스 응용 프로그램 웹 API를 표시하려는 경우 API에 대한 액세스 범위 및 역할을 구성해야 합니다. 클라이언트 응용 프로그램에서 웹 API에 액세스하도록 하려면 앱 등록에서 API에 액세스하기 위한 권한을 구성해야 합니다.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>웹 API를 공개하는 리소스 응용 프로그램 구성

웹 API를 표시할 때, 권한을 앱 등록에 추가하면 API가 **API 선택** 목록에 표시됩니다. 액세스 범위를 추가하려면 [리소스 응용 프로그램에 액세스 범위 추가](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application)에 설명된 단계를 따르세요.

## <a name="configuring-a-client-application-to-access-web-apis"></a>웹 API에 액세스하는 클라이언트 응용 프로그램 구성

앱 등록에 권한을 추가할 때 표시된 웹 API에 **API 액세스 권한을 추가**할 수 있습니다. 웹 API에 액세스하려면 [웹 API에 액세스할 수 있는 자격 증명 또는 권한 추가](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis)에 설명된 단계를 따르세요.

## <a name="next-steps"></a>다음 단계

-   [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Azure Active Directory 응용 프로그램 매니페스트 이해](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


