---
title: Azure AD 테넌트를 얻는 방법 | Microsoft Docs
description: 응용 프로그램 등록 및 구축을 위해 Azure Active Directory 테넌트를 얻는 방법입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 051078bd3220c8d0f6b78c834cdf3628c2039e8e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Azure Active Directory 테넌트를 얻는 방법

Azure Active Directory (Azure AD)에서 [테넌트](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) 는 조직을 나타냅니다. Azure, Microsoft Intune 또는 Office 365와 같은 Microsoft 클라우드 서비스에 등록하는 등 Microsoft와의 관계를 만들 때 조직에서 받고 소유하는 Azure AD 서비스의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다. 

테넌트는 회사에 사용자 및 암호, 사용자 프로필 데이터, 사용 권한 등에 대한 사용자의 정보를 저장합니다. 또한 그룹, 응용 프로그램, 조직 및 보안에 관련된 기타 정보도 포함합니다.

Azure AD 사용자가 응용 프로그램에 로그인하도록 허용하려면 자신의 테넌트에 응용 프로그램을 등록해야 합니다. Azure AD 테넌트를 만들고 여기에 응용 프로그램을 게시하는 경우 **비용이 전혀 들지 않습니다**(테넌트의 프리미엄 기능에 대해서는 비용을 지불할 수도 있음). 실제로 대부분의 개발자는 실험, 개발, 준비 및 테스트 목적으로 여러 개의 테넌트와 응용 프로그램을 만듭니다.

## <a name="use-an-existing-azure-ad-tenant"></a>기존 Azure AD 테넌트 사용

대부분의 개발자는 이미 Azure AD 테넌트(예: Office 365 또는 Azure 구독)와 연결된 서비스 또는 구독을 통해 테넌트를 확보하고 있습니다. 테넌트가 아직 있는지 확인하려면, 응용 프로그램을 관리하는 데 사용하려는 계정으로 [Azure Portal](https://portal.azure.com)에 로그인하고 오른쪽 위 모서리에 표시되는 계정 정보를 확인합니다. 테넌트가 있으면 자동으로 해당 테넌트에 로그인되며, 계정 이름 바로 아래에 테넌트 이름이 표시됩니다. Azure Portal의 오른쪽 위에 있는 사용자의 계정 이름을 가리키면 사용자의 이름, 이메일, 디렉터리 및 테넌트 ID(GUID), 그리고 사용자의 도메인이 나타납니다. 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다.

> [!TIP]
> 테넌트 ID를 찾아야 하는 경우 이 정보를 찾는 여러 방법이 있습니다. 사용자의 계정 이름을 기리켜 테넌트 ID를 가져오거나 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID**를 선택할 수 있습니다.

계정과 연결된 기존 테넌트가 없으면, 계정 이름 아래에 GUID가 표시되며 [새 테넌트를 만들](#create-a-new-azure-ad-tenant) 때까지 앱 등록과 같은 작업을 수행할 수 없습니다.

## <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

Azure AD 테넌트가 없거나 새 테넌트를 만들려는 경우 [Azure Portal](https://portal.azure.com)에서 [디렉터리 만들기 환경](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)을 사용하면 됩니다. 이 프로세스는 약 1분 정도 걸리며, 결국에는 새로 만든 테넌트로 이동하라는 메시지가 표시됩니다.
