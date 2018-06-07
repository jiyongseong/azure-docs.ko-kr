---
title: 'Azure AD Connect: 하이브리드 Azure AD 조인 구성 후 작업 | Microsoft Docs'
description: 이 문서에서는 하이브리드 Azure AD 조인 완료 후 필요한 구성 후 작업을 자세히 설명합니다.
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: samueld
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: anandyadavmsft
ms.openlocfilehash: 02f1cbd1f2b8f7b0bec2f8016a300ede1d6f0439
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354548"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인 구성 후 작업

Azure AD Connect를 실행하여 하이브리드 Azure AD 조인에 대한 조직을 구성한 후 설정을 완료하기 위해 해야 할 몇 가지 추가 단계가 있습니다.  사용하는 장치에 적용되는 단계만 수행하면 됩니다.

## <a name="1-configure-controlled-rollout-optional"></a>1. 제어된 롤아웃 구성(선택 사항)
Windows 10 및 Windows Server 2016을 실행하는 모든 도메인 조인 장치는 모든 구성 단계가 완료되면 자동으로 Azure AD에 등록됩니다. 이 자동 등록보다 제어된 롤아웃을 선호하는 경우 그룹 정책을 사용하여 자동 롤아웃을 선택적으로 사용하거나 사용하지 않을 수 있습니다.  이 그룹 정책은 다른 구성 단계를 시작하기 전에 설정해야 합니다. Azure AD
* Active Directory에서 그룹 정책 개체를 만듭니다.
* 이름을 지정합니다(예: 하이브리드 Azure AD 조인).
* 편집하고 컴퓨터 구성 > 정책 > 관리 템플릿 > Windows 구성 요소 > 장치 등록으로 이동합니다.

>[!NOTE]
>2012R2의 경우 정책 설정이 **컴퓨터 구성 > 정책 > 관리 템플릿 > Windows 구성 요소 > 작업 공간 연결 > 클라이언트 컴퓨터에 자동으로 작업 공간 연결**에 있습니다.

* [도메인에 가입된 컴퓨터를 장치로 등록] 설정을 해제합니다.
* 적용하고 [확인]을 클릭합니다.
* GPO를 원하는 위치(조직 구성 단위, 보안 그룹 또는 모든 장치의 도메인)에 연결합니다.

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. 장치 등록 엔드포인트로 네트워크 구성
Azure AD에 컴퓨터를 등록하려면 조직 네트워크 내에 있는 컴퓨터에서 다음 URL에 액세스할 수 있어야 합니다.

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Windows 10 장치에 대해 WPAD를 구현합니다.
조직이 아웃바운드 프록시를 통해 인터넷에 액세스하는 경우 Windows 10 컴퓨터에서 Azure AD에 등록할 수 있게 WPAD(웹 프록시 자동 검색)를 구현합니다.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Azure AD Connect에서 구성하지 않은 포리스트에 SCP를 구성합니다. 

SCP(서비스 연결점)는 장치에서 자동 등록에 사용하는 Azure AD 테넌트 정보를 포함하고 있습니다.  Azure AD Connect에서 다운로드한 PowerShell 스크립트 ConfigureSCP.ps1을 실행합니다.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Azure AD Connect에서 구성하지 않은 모든 페더레이션 서비스를 구성합니다.

조직에서 페더레이션 서비스를 사용하여 Azure AD에 로그인하는 경우 Azure AD 신뢰 당사자 트러스트의 클레임 규칙이 장치 인증을 허용해야 합니다. AD FS로 페더레이션을 사용하는 경우 [AD FS 도움말](https://aka.ms/aadrptclaimrules)로 이동하여 클레임 규칙을 생성합니다. Microsoft 이외의 페더레이션 솔루션을 사용하는 경우 해당 공급자에게 지침을 문의합니다.  

>[!NOTE]
>Windows 하위 수준 장치를 갖고 있는 경우 Azure AD 트러스트에 대한 요청을 받을 때 서비스에서 authenticationmethod 및 wiaormultiauthn 클레임 발급을 지원해야 합니다. AD FS에서, 인증 메서드를 통과하는 발급 변환 규칙을 추가해야 합니다.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Windows 하위 수준 장치에 Azure AD Seamless SSO 사용

조직에서 암호 해시 동기화 또는 통과 인증을 사용하여 Azure AD에 로그인하는 경우 해당 로그인 방법에 Azure AD Seamless SSO를 사용하여 Windows 하위 수준 장치를 인증합니다(https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso). 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Windows 하위 수준 장치에 대한 Azure AD 정책 설정

Windows 하위 수준 장치를 등록하려면 사용자가 장치를 등록할 수 있도록 Azure AD 정책에서 허용해야 합니다. 

* Azure Portal에서 계정에 로그인합니다.
* Azure Active Directory > 장치 > 장치 설정으로 이동합니다.
* "사용자가 장치를 Azure AD에 등록할 수 있습니다"를 [모두]로 설정합니다.
* 저장을 클릭합니다.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Windows 하위 수준 장치에 Azure AD 엔드포인트 추가

장치를 인증할 때 인증서 프롬프트가 나타나지 않도록 Windows 하위 수준 장치의 로컬 인트라넷 영역에 Azure AD 장치 인증 엔드포인트를 추가합니다(https://device.login.microsoftonline.com). 

[Seamless SSO](https://aka.ms/hybrid/sso)를 사용하는 경우 해당 영역에서 "스크립트를 통해 상태 표시줄 업데이트 허용"을 설정하고 https://autologon.microsoftazuread-sso.com 엔드포인트를 추가합니다. 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Windows 하위 수준 장치에 Microsoft Workplace Join을 설치합니다.

이 설치 관리자는 장치 시스템에 사용자 컨텍스트에서 실행되도록 예약된 작업을 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 통합 Windows 인증을 사용하여 인증한 후 사용자 자격 증명을 사용하여 장치를 Azure AD에 자동으로 가입합니다. 다운로드 센터는 https://www.microsoft.com/en-us/download/details.aspx?id=53554에 있습니다. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. 장치 등록을 허용하도록 그룹 정책을 구성합니다.

* 아직 그룹 정책 개체를 만들지 않은 경우 Active Directory에서 새로 만듭니다.
* 이름을 지정합니다(예: 하이브리드 Azure AD 조인).
* 편집하고 컴퓨터 구성 > 정책 > 관리 템플릿 > Windows 구성 요소 > 장치 등록으로 이동합니다.
* [도메인에 가입된 컴퓨터를 장치로 등록]을 설정합니다.
* 적용하고 [확인]을 클릭합니다.
* GPO를 원하는 위치(조직 구성 단위, 보안 그룹 또는 모든 장치의 도메인)에 연결합니다.

>[!NOTE]
>2012R2의 경우 정책 설정이 **컴퓨터 구성 > 정책 > 관리 템플릿 > Windows 구성 요소 > 작업 공간 연결 > 클라이언트 컴퓨터에 자동으로 작업 공간 연결**에 있습니다.

## <a name="next-steps"></a>다음 단계
[장치 쓰기 저장 구성](./active-directory-aadconnect-feature-device-writeback.md)
