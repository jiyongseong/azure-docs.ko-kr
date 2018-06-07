---
title: 응용 프로그램 프록시 에이전트 커넥터를 설치할 때 문제 발생 | Microsoft 문서
description: 응용 프로그램 프록시 에이전트 커넥터를 설치할 때 발생하는 문제를 해결하는 방법
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 70d2f159c5485009e36aa80dfa8b73f9b3cf4ec1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>응용 프로그램 프록시 에이전트 커넥터를 설치할 때 문제 발생

Microsoft AAD 응용 프로그램 프록시 커넥터는 아웃바운드 연결을 사용하여 클라우드 사용 가능 끝점에서 내부 도메인으로의 연결을 설정하는 내부 도메인 구성 요소입니다.

## <a name="general-problem-areas-with-connector-installation"></a>커넥터 설치에 대한 일반적인 문제 영역

커넥터 설치가 실패한 경우 근본 원인은 대개 다음 영역 중 하나입니다.

1.  **연결** – 성공적인 설치를 완료하려면 새 커넥터를 등록하고 향후 트러스트 속성을 설정해야 합니다. 이 작업은 AAD 응용 프로그램 프록시 클라우드 서비스에 연결하여 수행됩니다.

2.  **트러스트 설정** – 새 커넥터는 자체 서명된 인증서를 만들고 클라우드 서비스에 등록합니다.

3.  **관리자 인증** – 설치 중 사용자가 커넥터 설치를 완료하려면 관리자 자격 증명을 제공해야 합니다.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>클라우드 응용 프로그램 프록시 서비스 및 Microsoft 로그인 페이지에 대한 연결 확인

**목표:** 커넥터 컴퓨터가 Microsoft 로그인 페이지뿐만 아니라 AAD 응용 프로그램 프록시 등록 끝점에도 연결할 수 있는지 확인합니다.

1.  브라우저를 열고 <https://aadap-portcheck.connectorporttest.msappproxy.net> 웹 페이지로 이동하여 포트 80 및 443으로 미국 중부 및 미국 동부 데이터센터에 대한 연결이 작동하는지 확인합니다.

2.  이러한 포트 중 하나라도 성공하지 못하면(녹색 체크 표시가 없으면) 방화벽 또는 백 엔드 프록시에서 \*.msappproxy.net이 포트 80 및 443으로 올바르게 정의되어 있는지 확인합니다.

3.  브라우저(별도 탭)를 열고 <https://login.microsoftonline.com>을 방문하여 해당 페이지에 로그인할 수 있는지 확인합니다.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>컴퓨터 및 백 엔드 구성 요소가 응용 프로그램 프록시 트러스트 인증서를 지원하는지 확인

**목표:** 커넥터 컴퓨터, 백 엔드 프록시 및 방화벽이 향후 트러스트를 위해 커넥터가 만든 인증서를 지원할 수 있는지 확인합니다.

>[!NOTE]
>커넥터는 TLS1.2에서 지원하는 SHA512 인증서를 만들려고 합니다. 컴퓨터 또는 백 엔드 방화벽 및 프록시가 TLS1.2를 지원하지 않으면 설치가 실패합니다.
>
>

**이 문제를 해결하려면:**

1.  컴퓨터가 TLS1.2를 지원하는지 확인합니다. 2012 R2 이후의 모든 Windows 버전은 TLS1.2를 지원합니다. 커넥터 컴퓨터가 2012 R2 또는 이전 버전인 경우 컴퓨터에 <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2> 기술 자료 문서가 설치되어 있는지 확인합니다.

2.  네트워크 관리자에게 문의하여 백 엔드 프록시와 방화벽이 나가는 트래픽에 대해 SHA512를 차단하지 않는지 확인합니다.

## <a name="verify-admin-is-used-to-install-the-connector"></a>관리자가 커넥터를 설치했는지 확인합니다.

**목표:** 커넥터를 설치하려고 하는 사용자가 올바른 자격 증명을 가진 관리자인지 확인합니다. 현재 설치가 성공하려면 사용자가 전역 관리자여야 합니다.

**자격 증명이 올바른지 확인하려면:**

<https://login.microsoftonline.com>에 연결하고 동일한 자격 증명을 사용합니다. 로그인이 성공하는지 확인합니다. **Azure Active Directory** -&gt; **사용자 및 그룹** -&gt; **모든 사용자**로 이동하여 사용자 역할을 확인할 수 있습니다. 

사용자 계정을 선택한 다음 결과 메뉴에서 "디렉터리 역할"을 선택합니다. 선택한 역할이 "전역 관리자"인지 확인합니다. 이 단계에서 페이지에 액세스할 수 없으면 전역 관리자가 아닙니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 응용 프로그램 프록시 커넥터 이해](manage-apps/application-proxy-connectors.md)
