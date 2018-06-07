---
title: 'Azure AD Connect: 장치 옵션 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect에서 사용할 수 있는 장치 옵션에 대해 자세히 설명합니다.
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354528"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: 장치 옵션

다음 설명서에서는 Azure AD Connect에서 사용할 수 있는 다양한 장치 옵션에 대한 정보를 제공합니다. Azure AD Connect를 사용하여 다음 두 작업을 구성할 수 있습니다. 
* **하이브리드 Azure AD 조인**: 사용자의 환경에 온-프레미스 AD 공간이 있고 Azure Active Directory에서 제공하는 기능의 이점도 활용하려는 경우 하이브리드 Azure AD 조인 장치를 구현할 수 있습니다. 이는 온-프레미스 Azure Active Directory 및 Azure Active Directory에 모두 가입되어 있는 장치입니다.
* **장치 쓰기 저장**: 장치 쓰기 저장은 AD FS(2012 R2 이상) 보호 장치에 대한 장치에 따라 조건부 액세스를 사용하도록 설정하는 데 사용됩니다.

## <a name="configure-device-options-in-azure-ad-connect"></a>Azure AD Connect에서 장치 옵션 구성

1.  Azure AD Connect를 실행합니다. **추가 작업** 페이지에서 **장치 옵션 구성**을 선택합니다.
    ![장치 옵션 구성](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    [다음]을 클릭하면 수행 가능한 작업에 대해 자세히 설명하는 **개요** 페이지가 표시됩니다.
    ![개요](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > 새로운 장치 구성 옵션은 버전 1.1.819.0 이상에서만 사용할 수 있습니다.

2.  Azure AD의 자격 증명을 제공한 후에는 [장치 옵션] 페이지에서 수행할 작업을 선택할 수 있습니다.
    ![장치 작업](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>다음 단계

* [하이브리드 Azure AD 조인 구성](../device-management-hybrid-azuread-joined-devices-setup.md)
* [장치 쓰기 저장 구성/해제](./active-directory-aadconnect-feature-device-writeback.md)

