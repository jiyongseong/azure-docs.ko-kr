---
title: Azure Active Directory의 관리 단위 관리 미리 보기
description: Azure Active Directory에서 보다 세부적인 권한 위임을 위해 관리 단위 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.component: users-groups-roles
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 4fa93e4bf64e01b44b0826b2182b125004c49609
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Azure AD의 관리 단위 관리 - 공용 미리 보기
이 문서에서는 사용자의 하위 집합에 대해 관리 권한을 위임하고 사용자의 하위 집합에 정책을 적용하는 데 사용할 수 있는 리소스의 새로운 Azure Active Directory 컨테이너인 관리 단위에 대해 설명합니다. Azure Active Directory에서 관리 단위를 통해 중앙 관리자는 지역 관리자에게 권한을 위임하거나 세부적인 수준에서 정책을 설정할 수 있습니다.

이는 각각 독립된 많은 자치 학교(경영대학, 공과대학 등)로 구성된 종합 대학교와 같이 독립된 부서가 있는 조직에서 유용합니다. 그러한 부서는 자체 IT 관리자를 통해 액세스를 제어하고, 사용자를 관리하고, 특히 부서에 대한 정책을 설정합니다. 중앙 관리자는 특정 부서의 사용자에게 부서 관리자 권한을 부여할 수 있습니다. 이 예를 통해 보다 구체적으로 설명하자면, 중앙 관리자는 특정 학교(경영대학)에 대해 관리 단위를 만들고 경영대학 사용자만 채울 수 있습니다. 그런 다음 중앙 관리자는 경영대학 IT 직원을 범위 역할에 추가할 수 있습니다. 즉, IT 직원의 경영대학 관리 권한을 경영대학 관리 단위에만 부여할 수 있습니다.

> [!IMPORTANT]
> 관리 단위를 사용하려면 관리 단위로 범위가 지정된 관리자에게 Azure Active Directory Premium 라이선스가, 관리 단위 내 모든 사용자에게는 Azure Active Directory Basic 라이선스가 있어야 합니다. 자세한 내용은 [Azure AD Premium 시작을 참조하세요](active-directory-get-started-premium.md).
>


중앙 관리자의 관점에서 관리 단위는 리소스로 생성하고 채울 수 있는 디렉터리 개체입니다. **이 미리 보기 릴리스에서는 사용자만 이 리소스가 될 수 있습니다.** 관리 단위가 생성되고 채워지면, 관리 단위에 포함된 리소스에게만 권한을 부여하도록 제한하는 범위로 사용할 수 있습니다.

## <a name="managing-administrative-units"></a>관리 단위 관리
이 미리 보기 릴리스에서 Windows PowerShell cmdlet용 Azure Active Directory 모듈을 사용하는 관리 단위를 만들고 관리할 수 있습니다. 이 방법에 대한 자세한 내용은 [관리 단위 작업](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)을 참조하세요.

소프트웨어 요구 사항 및 Azure AD 모듈 설치에 대한 자세한 내용과 구문, 매개 변수 설명 및 예제 등 관리 단위를 관리하기 위한 Azure AD 모듈 cmdlet에 대한 내용은 [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 버전](active-directory-whatis.md)
