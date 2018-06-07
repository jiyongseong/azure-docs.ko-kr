---
title: 'Azure AD Connect: 설치 유형 선택 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect에 사용할 설치 유형을 선택하는 방법을 안내합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 76f1ce12ab149f57ec6e995d132de83105c5e0ca
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Azure AD Connect에 사용할 설치 유형 선택
Azure AD Connect에서는 새 설치에 대해 기본 및 사용자 지정의 두 가지 설치 유형을 제공합니다. 이 항목은 설치 중에 사용할 옵션을 결정하는 데 도움이 됩니다.

## <a name="express"></a>Express
기본은 가장 일반적인 옵션으로, 약 90%의 새 설치에서 사용됩니다. 이 옵션은 가장 일반적인 고객 시나리오에 적합한 구성을 제공하기 위해 고안되었습니다.

이 옵션에서는 다음과 같이 전제합니다.

- 단일 Active Directory 포리스트 온-프레미스가 있습니다.
- 설치에 사용할 수는 엔터프라이즈 관리자 계정이 있습니다.
- 온-프레미스 Active Directory에 100,000개 미만의 개체가 있습니다.

다음 기능을 사용할 수 있습니다.

- Single Sign-On을 위해 온-프레미스에서 Azure AD로 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-hash-synchronization.md).
- [사용자, 그룹, 연락처 및 Windows 10 컴퓨터](active-directory-aadconnectsync-understanding-default-configuration.md)를 동기화하는 구성
- 모든 도메인 및 모든 OU에 포함된 적합한 모든 개체의 동기화
- 항상 사용 가능한 최신 버전을 사용할 수 있도록 [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md)가 사용되도록 설정됩니다.

기본을 계속 사용할 수 있는 경우의 옵션:

- 모든 OU를 동기화하지는 않으려면 기본을 계속 사용하고 마지막 페이지에서 **동기화 프로세스 시작...**\*을 선택 취소합니다. 그런 후 설치 마법사를 다시 실행하고 [구성 옵션](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options)에서 OU를 변경한 후 예약된 동기화를 사용하도록 설정합니다.
- Azure AD Premium에서 비밀번호 쓰기 저장 등의 기능 중 하나를 사용하려고 합니다. 먼저 기본 방법을 진행하여 초기 설치를 완료합니다. 그런 후 설치 마법사를 다시 실행하고 [구성 옵션](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options)을 변경합니다.

## <a name="custom"></a>사용자 지정
사용자 지정된 경로는 기본보다 더 많은 옵션을 제공합니다. 기본 설치에 대해 이전 섹션에 설명된 구성이 조직에 맞지 않는 모든 경우에 이 옵션을 사용해야 합니다.

사용하는 경우:

- Active Directory에서 엔터프라이즈 관리자 계정에 대한 액세스 권한이 없습니다.
- 둘 이상의 포리스트가 있거나 앞으로 둘 이상의 포리스트를 동기화하려고 합니다.
- Connect 서버에서 연결할 수 있는 도메인이 포리스트에 없습니다.
- 사용자 로그인을 위해 페더레이션 또는 통과 인증을 사용하려고 합니다.
- 100,000개가 넘는 개체가 있고 전체 SQL Server를 사용해야 합니다.
- 그룹 기반 필터링과 도메인 또는 OU 기반 필터링을 사용하려고 합니다.

## <a name="upgrade-from-dirsync"></a>DirSync에서 업그레이드
현재 DirSync를 사용하는 경우 [DirSync에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)의 단계에 따라 기존 구성을 업그레이드합니다. 업그레이드 옵션에는 다음 두 가지가 있습니다.

- 동일한 서버에 Connect를 설치하기 위한 전체 업그레이드
- 기존 DirSync 서버가 여전히 작동 중인 상태에서 Connect를 새 서버에 설치하는 병렬 배포

## <a name="upgrade-from-azure-ad-sync"></a>Azure AD Sync에서 업그레이드
현재 Azure AD Sync를 사용하는 경우 한 Connect 버전에서 최신 버전으로 업그레이드할 때와 [동일한 단계](active-directory-aadconnect-upgrade-previous-version.md)를 따를 수 있습니다. 업그레이드 옵션에는 다음 두 가지가 있습니다.

- 동일한 서버에 Connect를 설치하기 위한 전체 업그레이드
- 기존 Azure AD Sync 서버가 여전히 작동 중인 상태에서 Connect를 새 서버에 설치하는 스윙 마이그레이션

## <a name="migrate-from-fim2010-or-mim2016"></a>FIM2010 또는 MIM2016에서 마이그레이션
현재 Azure AD Connect에서 Forefront Identity Manager 2010 또는 Microsoft Identity Manager 2016을 사용하는 경우 마이그레이션만 가능한 옵션입니다. [스윙 마이그레이션](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)에 설명된 단계를 따릅니다. 이러한 단계에서는 Azure AD Sync를 모두 FIM2010/MIM2016으로 바꿉니다.

## <a name="next-steps"></a>다음 단계
사용하도록 선택한 옵션에 따라 왼쪽의 목차에서 자세한 단계를 제공하는 문서를 찾아보세요.
