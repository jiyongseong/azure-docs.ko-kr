---
title: Azure 스택 앱 서비스 업데이트 2의 릴리스 정보 | Microsoft Docs
description: Azure 스택 앱 서비스에 대 한 두 개의 알려진된 문제 및 업데이트를 다운로드 위치에 업데이트에 포함 된 내용에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Azure 스택 업데이트 2 릴리스 정보에 대 한 앱 서비스

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이러한 릴리스 정보에 개선 사항 및 Azure 앱 서비스의 수정 Azure 스택 업데이트 2와 관련 된 알려진된 문제에 설명 합니다. 알려진된 문제를 직접 배포, 업데이트 프로세스 및 문제 (설치 후) 빌드와 관련 된 문제 나뉩니다.

> [!IMPORTANT]
> Azure 스택 통합 시스템 1804 업데이트를 적용 하거나 Azure 앱 서비스 1.2를 배포 하기 전에 최신 Azure 스택 개발 키트를 배포 합니다.
>
>

## <a name="build-reference"></a>빌드 참조

Azure 스택 업데이트 2 빌드 번호에 앱 서비스는 **72.0.13698.10**

### <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> Azure 스택 앱 서비스를 Azure의 새 배포 이제 요구는 [3 주체 와일드 카드 인증서](azure-stack-app-service-before-you-get-started.md#get-certificates) 처리 하는 방식에는 SSO에 대 한 Kudu 이제 Azure 앱 서비스에서의 향상으로 인해 합니다. 새 제목은  **\*. sso.appservice.\< 지역\>.\< domainname\>.\< 확장\>**
>
>

참조는 [시작 하기 전에 설명서](azure-stack-app-service-before-you-get-started.md) 배포가 시작 되기 전에 합니다.

### <a name="new-features-and-fixes"></a>새 기능 및 수정

Azure 스택 업데이트 2에서 azure 앱 서비스는 다음과 같은 개선 사항이 및 수정 합니다.

- 업데이트를 **응용 프로그램 서비스 테 넌 트, 관리자, 함수 포털 및 Kudu 도구**합니다. Azure 스택 포털 SDK 버전와 일치 합니다.

- 안정성 및 오류 일반적인 문제를 쉽게 진단 사용 메시지를 개선 하기 위해 핵심 서비스를 업데이트 합니다.

- **다음 응용 프로그램 프레임 워크 및 도구에 대 한 업데이트**:
  - 추가 된.Net Framework 4.7.1
  - 추가 **Node.JS** 버전:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - 추가 **NPM** 버전:
    - 5.6.0
  - .NET 업데이트 핵심 구성 요소 공용 클라우드에서 Azure 앱 서비스와 일치 하도록 합니다.
  - 업데이트 된 Kudu

- 배포의 자동 전환을 슬롯 기능이 활성화- [자동 교환 구성](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- 테스트에서 프로덕션 기능이 활성화- [프로덕션에서 테스트 소개](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- 사용-azure 함수 프록시 [Azure 함수 프록시 사용](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- 앱 서비스 관리 확장 UX 지원 장바구니 분석 이라는
  - 비밀 회전
  - 인증서 회전
  - 시스템 자격 증명 회전
  - 연결 문자열 회전

### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)

- 작업자는 응용 프로그램 서비스는 기존 가상 네트워크에 배포 하 고 파일 서버 에서만 사용 가능 개인 네트워크에 파일 서버에 연결할 수 없습니다.

기존 가상 네트워크와 파일 서버에 연결 하는 데 내부 IP 주소에 배포 하려는 경우 추가 해야는 아웃 바운드 보안 규칙 작업자 서브넷과 파일 서버 간에 SMB 트래픽을 사용 하도록 설정 합니다. 이 작업을 수행 하려면 관리 포털에서 WorkersNsg으로 이동한 다음 속성으로 아웃 바운드 보안 규칙 추가:
 * 원본: 모든
 * 원본 포트 범위: *
 * 대상: IP 주소
 * 대상 IP 주소 범위: Ip 범위를 파일 서버에 대 한
 * 대상 포트 범위: 445
 * 프로토콜: TCP
 * 동작: 허용
 * 우선 순위: 700
 * 이름: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 Azure 운영 클라우드 관리자에 대 한 알려진된 문제

설명서에서 참조는 [Azure 스택 1804 릴리스 정보](azure-stack-update-1804.md)

## <a name="next-steps"></a>다음 단계

- Azure 앱 서비스의 개요를 참조 하십시오. [Azure 앱 서비스에 대 한 Azure 스택 개요](azure-stack-app-service-overview.md)합니다.
- Azure 스택 앱 서비스 배포를 준비 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택 앱 서비스를 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)합니다.
