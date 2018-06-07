---
title: "Azure AD Connect 동기화: 기술 개념 | Microsoft Docs"
description: "Azure AD Connect 동기화의 기술 개념에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi;andkjell
ms.openlocfilehash: 2cb5143729fa92d12b21701e62416bced31c1fc7
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect Sync: 기술 개념
이 문서에서는 [아키텍처 이해](active-directory-aadconnectsync-technical-concepts.md)항목을 요약합니다.

Azure AD Connect 동기화는 견고한 메타 디렉터리 동기화 플랫폼상에 빌드됩니다.
다음 섹션에서는 메타 디렉터리 동기화에 대한 개념을 소개합니다.
MIIS, ILM 및 FIM을 바탕으로 구성된 Azure Active Directory 동기화 서비스는 데이터 원본에 연결, 데이터 원본 사이에 데이터 동기화 및 ID의 프로비전 및 프로비전 해제를 위한 차세대 플랫폼을 제공합니다.

![기술 개념](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

다음 섹션에서는 FIM 동기화 서비스의 다음과 같은 측면에 대한 자세한 정보를 제공합니다.

* 커넥터
* 특성 이름
* 커넥터 공간
* 메타 버스
* 프로비전

## <a name="connector"></a>커넥터
연결된 디렉터리와 통신하는 데 사용되는 코드 모듈을 커넥터(이전의 MA(관리 에이전트))라고 합니다.

이러한 작업은 Azure AD Connect 동기화를 실행하는 컴퓨터에 설치됩니다. 커넥터는 특수 에이전트의 배포에 의존하지 않고 원격 시스템 프로토콜을 사용하여 대화할 수 있는 에이전트 없는 기능을 제공합니다. 따라서 특히 중요한 응용 프로그램 및 시스템을 다룰 때 위험성과 배포 시간이 감소합니다.

위 그림에서 커넥터는 커넥터 공간와 동의어 이지만 외부 시스템과의 모든 통신을 포함합니다.

커넥터는 모든 가져오기 및 시스템에 내보내기 기능을 책임지며, 개발자가 선언적 프로비전을 사용하여 데이터 변환을 사용자 지정하는 경우 기본적으로 각 시스템에 연결하는 방법을 이해할 필요가 없게 해줍니다.

가져오기 및 내보내기가 예약된 경우에만 발생하므로 변경 내용이 연결된 데이터 원본에 자동으로 전파되지 않아서 변경이 추가로 차단됩니다. 또한 개발자는 거의 모든 데이터 원본에 연결하기 위해 자체 커넥터를 만들 수 있습니다.

## <a name="attribute-flow"></a>특성 이름
메타 버스는 인접 커넥터 공간에서 조인된 모든 ID의 통합된 보기입니다. 위 그림에서는 특성 흐름이 인바운드 및 아웃바운드 흐름에 대해 화살촉이 있는 선으로 표시됩니다. 특성 흐름은 한 시스템에서 다른 시스템으로 데이터 복사 또는 변환 프로세스이며 모든 특성 흐름(인바운드 또는 아웃바운드)입니다.

특성 흐름은 동기화(전체 또는 델타) 작업이 예정되어 있을 때 커넥터 공간과 메타 버스 사이에서 양방향으로 발생합니다.

특성 흐름은 이러한 동기화가 실행되는 경우에만 발생합니다. 특성 흐름은 동기화 규칙에 정의되어 있습니다. 이러한 흐름은 인바운드(위 그림의 ISR) 또는 아웃바운드(위 그림의 OSR)일 수 있습니다.

## <a name="connected-system"></a>연결된 시스템
연결된 시스템(연결된 디렉터리)은 Azure AD Connect Sync가 연결된 원격 시스템을 뜻하며 ID 데이터를 읽고 씁니다.

## <a name="connector-space"></a>커넥터 공간
연결된 각 데이터 원본은 커넥터 공간 내 개체 및 특성의 필터링된 하위 집합으로 표시됩니다.
따라서 개체를 동기화할 때 원격 시스템에 연결할 필요 없이 동기화 서비스가 로컬에서 작동되며, 상호 작용이 가져오기 및 내보내기로만 제한됩니다.

데이터 원본 및 커넥터가 변경 내용 목록(델타 가져오기)을 제공할 수 있는 능력이 있는 경우 운영 효율성이 크게 증가합니다. 마지막 폴링 주기 이후의 변경 내용만 교환되기 때문입니다. 커넥터 공간은 커넥터 일정 가져오기와 내보내기를 요구하여 연결된 데이터 원본이 변경 내용을 자동으로 전파하지 못하도록 방지합니다. 추가된 이 보험은 테스트, 미리보기 또는 다음 업데이트를 확인하는 동안 마음의 평화를 줍니다.

## <a name="metaverse"></a>메타 버스
메타 버스는 인접 커넥터 공간에서 조인된 모든 ID의 통합된 보기입니다.

ID가 서로 연결되어 있고 가져오기 흐름 매핑을 통해 기관이 다양한 특성에 할당되므로 중앙 메타 버스 개체가 여러 시스템으로부터 정보를 집계하기 시작합니다. 이 개체 특성 흐름으로부터 매핑이 아웃바운드 시스템에 정보를 전달합니다.

권한이 있는 시스템이 메타 버스에 정보를 프로젝션하면 개체가 생성됩니다. 모든 연결이 제거되는 즉시 메타 버스 개체가 삭제됩니다.

메타버스의 개체는 직접 편집할 수 없습니다. 개체의 모든 데이터는 특성 흐름을 통해 제공되어야 합니다. 메타버스는 각 커넥터 공간을 가진 영구 커넥터를 관리합니다. 이러한 커넥터는 각 동기화 실행에 대해 다시 평가할 필요가 없습니다. 즉 Azure AD Connect Sync는 매번 일치하는 원격 개체를 찾을 필요가 없습니다. 이렇게 하면 일반적으로 개체를 연결해야 하는 특성이 변경되지 않도록 하기 위해 비용이 많이 드는 에이전트가 필요하지 않습니다.

관리해야 하는 기존 개체가 포함되어 있을 수 있는 새 데이터 원본을 검색할 때 Azure AD Connect 동기화는 조인 규칙이라는 프로세스를 사용하여 링크를 설정하는 데 사용할 잠재적인 후보를 평가합니다.
링크가 설정되면 이 평가가 다시 발생하지 않으며 일반 특성 흐름이 연결된 원격 데이터 원본과 메타 버스 간에 발생할 수 있습니다.

## <a name="provisioning"></a>프로비전
권한이 있는 원본이 새 개체를 메타 버스에 프로젝션하면 새 커넥터 공간 개체를 다른 커넥터에 생성하여 다운스트림에 연결된 데이터 소스를 나타낼 수 있습니다.

그러면 고유하게 링크가 설정되고 특성 흐름이 양방향으로 진행될 수 있습니다.

규칙이 새 커넥터 공간 개체를 만들어야 한다고 결정할 때마다 프로버전이라고 부릅니다. 그러나 이 작업은 커넥터 공간 내에서만 발생하기 때문에 내보내기가 실행될 때까지 연결된 데이터 원본으로 전달되지 않습니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
