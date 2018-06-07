---
title: 셀프 서비스 암호 재설정 | Microsoft Docs
description: Azure Active Directory B2C 미리 보기에서 고객을 위해 셀프 서비스 암호 재설정을 설정하는 방법 보여주기
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.openlocfilehash: 5b75455ad604b594a5f85fea8299d35a7d02c848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>고객을 위해 셀프 서비스 암호 재설정 구성
셀프 서비스 암호 재설정 기능을 사용하면 로컬 계정에 등록된 고객은 자체적으로 암호를 재설정할 수 있습니다. 특히 정기적으로 응용 프로그램을 사용하는 수백 만 명의 고객 있는 경우 지원 담당자의 부담을 크게 줄여줍니다. 현재는 검증된 이메일 주소만 복구 방법으로 지원됩니다.

> [!NOTE]
> 이 문서는 로그인 정책의 컨텍스트에서 사용되는 셀프 서비스 암호 재설정에 적용됩니다. 완전히 사용자 지정 가능한 암호 재설정 정책이 앱에서 호출되어야 하는 경우 [이 문서](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)를 참조하세요.
> 
> 

기본적으로 디렉터리에는 셀프 서비스 암호 재설정이 설정되어 있지 않습니다. 설정하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com/)에 구독 관리자로 로그인합니다. 이는 동일한 직장이나 학교 계정 또는 디렉터리를 만드는 데 사용한 동일한 Microsoft 계정입니다.
2. **Azure Active Directory**를 엽니다(왼쪽 탐색 모음).
4. **셀프 서비스 암호 재설정이 사용하도록 설정됨**을 **모두**로 설정합니다. 
5. 페이지 위쪽에서 **저장**을 클릭합니다. 완료되었습니다!

테스트하려면 (로컬 계정을 ID 공급자로 가진)로그인 정책에서 "지금 실행" 기능을 사용합니다. 메일 주소 및 암호 또는 사용자 이름 및 암호를 입력하는 로컬 계정 로그인 페이지에서 **계정에 액세스할 수 없나요?** 를 클릭하여 고객 환경을 확인합니다.

> [!NOTE]
> 셀프 서비스 암호 재설정 페이지는 [회사 브랜딩 기능](../active-directory/customize-branding.md)을 사용하여 사용자 지정할 수 있습니다.
> 
> 

