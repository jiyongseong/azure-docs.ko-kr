---
title: "Azure AD Connect 동기화: 기본 구성 변경 | Microsoft Docs"
description: "Azure AD Connect 동기화의 기본 구성을 변경의 모범 사례를 제공합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 384794849eb0bf2cb2f4dd056b0c95ab84d77b6e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect 동기화: 기본 구성 변경에 대한 모범 사례
이 항목에서는 Azure AD Connect 동기화에 지원되거나 지원되지 않는 변경사항을 설명합니다.

Azure AD Connect에 의해 만들어진 구성은 온-프레미스 Active Directory와 Azure AD를 동기화하는 대부분의 환경에 대해 “있는 그대로” 작동합니다. 그러나 일부 경우에는, 특정 필요 또는 요구 사항을 충족시키기 위해 구성에 일부 변경 내용을 적용할 필요가 있습니다.

## <a name="changes-to-the-service-account"></a>서비스 계정의 변경 내용
Azure AD Connect 동기화는 설치 마법사에서 만든 서비스 계정에서 실행 중입니다. 이 서비스 계정은 동기화에 의해 사용되는 데이터베이스에 대한 암호화 키를 보유합니다. 127자의 긴 암호로 만들어지며, 만료되지 않도록 설정됩니다.

* 서비스 계정의 암호는 변경 또는 초기화가 **지원되지 않습니다** . 이렇게 하면 암호화 키는 파기되고 서비스는 데이터베이스에 액세스할 수 없으며, 서비스를 시작할 수 없습니다.

## <a name="changes-to-the-scheduler"></a>스케줄러에 대한 변경 사항
빌드 1.1(2016년 2월)의 릴리스부터 [스케줄러](active-directory-aadconnectsync-feature-scheduler.md) 가 기본값 30분이 아닌 다른 동기화 주기를 사용하도록 구성할 수 있습니다.

## <a name="changes-to-synchronization-rules"></a>동기화 규칙 변경
설치 마법사는 가장 일반적인 시나리오에 대한 작업으로 간주되는 구성을 제공합니다. 구성을 변경해야 할 경우에도 여전히 지원되는 구성이 포함된 이 규칙을 따라야 합니다.

* 기본 직접 특성 흐름이 조직에 적합하지 않은 경우 [특성 흐름을 변경](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) 할 수 있습니다.
* [특성을 전달되지 않도록](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) 하고 Azure AD에서 기존 특성 값을 제거하려면 이 시나리오에 대한 규칙을 만들어야 합니다.
* [원치 않는 동기화 규칙을 사용하지 않습니다](#disable-an-unwanted-sync-rule) . 삭제된 규칙을 업그레이드 중 다시 만듭니다.
* [기본 규칙을 변경하려면](#change-an-out-of-box-rule)원래 규칙의 복사본을 만든 다음 기본 규칙을 사용하지 않도록 설정해야 합니다. 동기화 규칙 편집기에서 도움이 되는 메시지가 표시됩니다.
* 동기화 규칙 편집기를 사용하여 사용자 지정 동기화 규칙을 내보냅니다. 이 편집기는 재해 복구 시나리오에서 쉽게 다시 만드는 데 사용할 수 있는 PowerShell 스크립트를 제공합니다.

> [!WARNING]
> 기본 동기화 규칙에는 지문이 포함되어 있습니다. 이들 규칙을 변경하면 지문이 더 이상 일치하지 않습니다. 나중에 Azure AD Connect의 새 릴리스를 적용할 때 문제가 될 수 있습니다. 이 문서에 유일하게 변경하는 방법이 설명되어 있습니다.

### <a name="disable-an-unwanted-sync-rule"></a>원치 않는 동기화 규칙을 사용하지 않습니다
기본 동기화 규칙을 삭제하지 마십시오. 다음 업그레이드 도중에 다시 만들어집니다.

경우에 따라 설치 마법사가 사용 중인 토폴로지에 대해 작동하지 않는 구성을 생성합니다. 예를 들어, 계정 리소스 포리스트 토폴로지를 가지고 있지만 계정 포리스트의 스키마를 Exchange 스키마로 확장한 경우 Exchange에 대한 규칙은 계정 포리스트와 리소스 포리스트에 대해 생성됩니다. 이 경우 Exchange에 대한 동기화 규칙을 사용하지 않아야 합니다.

![비활성화된 동기화 규칙](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

위의 그림에서 설치 마법사가 계정 포리스트의 이전 버전 Exchange 2003 스키마를 발견했습니다. 이 스키마 확장은 리소스 포리스트가 Fabrikam의 환경에 도입되기 전에 추가되었습니다. 동기화된 이전 버전의 Exchange 구현에 대한 특성이 없는지 확인하려면 동기화 규칙을 보여지는 것처럼 사용하지 않도록 설정해야 합니다.

### <a name="change-an-out-of-box-rule"></a>기본 규칙을 변경하려면
조인 규칙을 변경해야 할 때만 기본 규칙을 변경할 수 있습니다. 특성 흐름을 변경해야 할 경우 기본 규칙보다 우선 순위가 높은 동기화 규칙을 만들어야 합니다. 실질적으로 복제해야 하는 유일한 규칙은 **In from AD - User Join** 규칙입니다. 다른 모든 규칙을 더 높은 우선 순위 규칙으로 덮어쓸 수 있습니다.

기본 규칙을 변경해야 할 경우 기본 규칙의 복사본을 만든 다음 원래 규칙을 사용하지 않도록 설정해야 합니다. 그런 다음 복제된 규칙을 변경합니다. 동기화 규칙 편집기가 이들 단계를 수행하도록 도와줍니다. 기본 규칙을 열면 이 대화 상자가 나타납니다.  
![경고 받은 기본 규칙](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

**예**를 선택하여 규칙의 복사본을 만듭니다. 그런 다음 복제된 규칙이 열립니다.  
![복제된 규칙](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

이 복제된 규칙에서 범위, 조인 및 변환에 필요한 사항을 변경합니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
