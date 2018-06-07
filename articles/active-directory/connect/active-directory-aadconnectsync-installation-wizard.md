---
title: Azure AD Connect 설치 마법사 다시 실행 | Microsoft Docs
description: 두 번째로 실행하는 설치 마법사 작동 방법을 설명합니다.
keywords: Azure AD Connect 설치 마법사를 사용하면 두 번째로 실행하는 유지 관리 설정을 구성합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 56cc38275a23eb4529558b876db619768a885a25
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150518"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect 동기화: 두 번째로 설치 마법사 실행
처음으로 Azure AD Connect 설치 마법사를 실행하는 경우 설치를 구성하는 방법을 안내합니다. 설치 마법사를 다시 실행하는 경우 유지 관리에 대한 옵션을 제공합니다.

**Azure AD Connect**라는 시작 메뉴에서 설치 마법사를 찾을 수 있습니다

![시작 메뉴](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

설치 마법사를 시작하는 경우 이러한 옵션이 있는 페이지가 표시됩니다.

![추가 작업의 목록이 있는 페이지](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Azure AD Connect와 함께 ADFS를 설치한 경우 더 많은 옵션이 있습니다. ADFS에 있는 추가 옵션은 [ADFS 관리](active-directory-aadconnect-federation-management.md#manage-ad-fs)에 문서화됩니다.

작업 중 하나를 선택하고 **다음**을 클릭하여 계속합니다.

> [!IMPORTANT]
> 설치 마법사가 열린 동안 동기화 엔진의 모든 작업이 일시 중단됩니다. 구성 변경을 완료하는 즉시 설치 마법사를 닫았는지 확인합니다.
>
>

## <a name="view-current-configuration"></a>현재 구성 보기
이 옵션은 현재 구성된 옵션의 빠른 보기를 제공합니다.

![모든 옵션 및 해당 상태의 목록이 있는 페이지](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

**이전**을 클릭하여 다시 돌아갑니다. **종료**를 선택하는 경우 설치 마법사를 닫습니다.

## <a name="customize-synchronization-options"></a>동기화 옵션 사용자 지정
이 옵션을 사용하여 동기화 구성을 변경합니다. 사용자 지정 구성 설치 경로에서 옵션의 하위 집합을 확인할 수 있습니다. Express 설치를 처음 사용하더라도 이 옵션이 표시됩니다.

* [더 많은 디렉터리 추가](active-directory-aadconnect-get-started-custom.md#connect-your-directories). 디렉터리를 제거하려면 [커넥터 삭제](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete)를 참조하세요.
* [도메인 및 OU 필터링 변경](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)
* 그룹 필터링을 제거합니다.
* [선택적 기능 변경](active-directory-aadconnect-get-started-custom.md#optional-features)

초기 설치에서 다른 옵션을 변경할 수 없고 사용할 수 없습니다. 이러한 옵션은 다음과 같습니다.

* userPrincipalName 및 sourceAnchor에 사용할 특성을 변경합니다.
* 다른 포리스트의 개체에 대한 조인 방법을 변경합니다.
* 그룹 기반 필터링을 사용합니다.

## <a name="refresh-directory-schema"></a>디렉터리 스키마 새로 고침
온-프레미스 AD DS 포리스트 중 하나에서 스키마를 변경하는 경우 이 옵션을 사용합니다. 예를 들어 Exchange를 설치했거나 장치 개체를 사용하여 Windows Server 2012 스키마로 업그레이드했습니다. 이 경우에 Azure AD Connect가 AD DS에서 스키마를 다시 읽고 캐시를 업데이트하도록 지시해야 합니다. 이 작업은 또한 동기화 규칙을 다시 생성합니다. 예를 들어 Exchange 스키마를 추가하는 경우 Exchange에 대한 동기화 규칙을 구성에 추가합니다.

이 옵션을 선택하면 구성에 있는 모든 디렉터리가 나열됩니다. 기본 설정을 유지하고 모든 포리스트를 새로 고치거나 선택을 취소할 수 있습니다.

![환경에서 모든 디렉터리의 목록이 있는 페이지](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>준비 모드 구성
이 옵션을 사용하면 서버에서 준비 모드를 설정하거나 해제할 수 있습니다. 준비 모드 및 사용 방법에 대한 자세한 내용은 [작업](active-directory-aadconnectsync-operations.md#staging-mode)에서 찾을 수 있습니다.

옵션은 현재 준비를 설정하는지 또는 해제하는지를 표시합니다.  
![준비 모드의 현재 상태도 표시하는 옵션](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

상태를 변경하려면 이 옵션을 선택하고 확인란을 선택하거나 선택을 취소합니다.  
![준비 모드의 현재 상태도 표시하는 옵션](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>사용자 로그인 변경
이 옵션을 사용하면 암호 해시 동기화, 통과 인증 또는 페더레이션 간 사용자 로그인 방법을 변경할 수 있습니다. **구성하지 않음**으로 변경할 수 없습니다.

이 옵션에 대한 자세한 내용은 [사용자 로그인](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)에서 Azure AD Connect 동기화에서 사용되는 구성 모델에 대해 자세히 알아봅니다.

**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
