---
title: 소비자 등록 중에 전자 메일 확인 사용 안 함 - Azure Active Directory B2C
description: Azure Active Directory B2C의 소비자 등록 중에 전자 메일 확인을 사용하지 않도록 설정하는 방법을 보여 주는 토픽입니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 2/06/2017
ms.author: davidmu
ms.openlocfilehash: 4f48df553d35386fb2b0448972dc01f9c6427ebd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: 소비자 등록 중에 전자 메일 확인 사용 안 함
Azure AD(Azure Active Directory) B2C를 사용하도록 설정하면 소비자는 전자 메일 주소를 제공하고 로컬 계정을 만들어 응용 프로그램에 등록할 수 있게 됩니다. Azure AD B2C는 등록 프로세스 중에 소비자에게 전자 메일 주소를 확인하도록 요구하여 전자 메일 주소의 유효성을 보장합니다. 또한 악의적인 자동화 프로세스를 통해 응용 프로그램에 대한 가짜 계정이 생성되지 못하게 합니다.

일부 응용 프로그램 개발자는 등록 프로세스 동안 전자 메일 확인을 건너뛰고 소비자에게 나중에 전자 메일 주소를 확인하도록 합니다. 이를 지원하는 경우 전자 메일 확인을 사용하지 않도록 Azure AD B2C를 구성할 수 있습니다. 이를 통해 등록 프로세스가 좀 더 원활해지고 개발자는 전자 메일 주소를 확인한 소비자와 아직 전자 메일 주소를 확인하지 않은 소비자를 구분할 수 있습니다.

기본적으로 등록 정책에는 전자 메일 확인 기능이 켜져 있습니다. 해제하려면 다음 단계를 사용합니다.

1. [다음 단계에 따라 Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. 등록 구성 방식에 따라 **등록 정책** 또는 **등록 또는 로그인 정책**을 클릭합니다.
3. 정책(예: "B2C_1_SiUp")을 클릭하여 엽니다. 블레이드 위쪽에서 **편집** 을 클릭합니다.
4. **페이지 UI 사용자 지정**을 클릭합니다.
5. **로컬 계정 등록 페이지**를 클릭합니다.
6. **등록 특성** 섹션의 **이름** 열에 있는 **전자 메일 주소**를 클릭합니다.
7. **확인 필요** 옵션을 **아니요**로 전환합니다.
8. **정책 편집** 블레이드에 도달할 때까지 아래쪽의 **확인**을 클릭합니다.
9. 블레이드 위쪽에서 **저장**을 클릭합니다. 완료되었습니다!

> [!NOTE]
> 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. 기본 기능을 사용하지 않도록 설정하는 경우에는 자체 확인 시스템을 추가하는 것이 좋습니다.
> 
> 

Microsoft는 사용자 의견 및 제안을 항상 환영합니다! 이 토픽을 완료하기가 어렵거나 이 콘텐츠를 개선할 사항이 있는 경우 페이지의 맨 아래에 의견을 보내주시면 감사하겠습니다. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)에 추가해 주세요.
