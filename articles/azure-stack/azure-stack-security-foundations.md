---
title: "Azure 스택 보안 제어 이해 | Microsoft Docs"
description: "Azure 스택에 적용 하 고 보안 제어 하는 방법에 대 한 자세한 내용은 서비스 관리자는"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1d92f8f2ed9e8ab504afc65bab861e1f7bb3689
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure 스택 인프라 보안 상태

*적용 대상: Azure 스택 시스템 통합*

보안 고려 사항 및 규정 준수는 하이브리드 클라우드를 사용 하는 것에 대 한 기본 드라이버 가장 합니다. Azure 스택은 이러한 시나리오에 맞게 디자인 하 고 Azure 스택 채택 하는 경우 이미 있는 컨트롤을 이해 해야 합니다.

Azure 스택은 함께 사용 하는 두 개의 보안 상태 계층이 있습니다. 첫 번째 계층은 끝까지 Azure 리소스 관리자는 하드웨어 구성 요소에서 이동 하 고 관리자 및 테 넌 트 포털을 포함 하 여 Azure 스택 인프라를 구성 합니다. 두 번째 계층은 테 넌 트 만들기, 배포 및 관리 되는 작업은 구성 되며 가상 컴퓨터 또는 응용 프로그램 서비스 웹 사이트 등이 포함 됩니다.  

## <a name="security-approach"></a>보안 방법
Azure 스택 최신 위협 으로부터 보호 하기 위해 보안 상태에 두고 설계 되었습니다 및 규정 준수 주요 표준의 요구 사항을 충족 하도록 만들어졌습니다. 결과적으로, Azure 스택 인프라의 보안 상태는 두 개의 독특하고 기반:

 - **위반을 가정 합니다.**  
에 집중 시스템 이미 위반 되었는지 가정과에서 부터는 *검색 및 위반의 영향을 제한* 대만 공격을 방지 하려고 합니다. 
 - **기본적으로 기능이 강화 되었습니다.**  
인프라에 잘 정의 된 하드웨어 및 소프트웨어에서 실행 되므로 우리 *사용 하도록 설정, 구성 및 보안 기능을 모든 유효성 검사* 기본적으로 합니다.



Azure 스택 통합된 시스템으로 배달 되므로, Azure 스택 인프라의 보안 상태는 Microsoft에서 정의 됩니다. Azure에서 마찬가지로 테 넌 트는 자신의 테 넌 트 작업 부하의 보안 상태를 정의 하는 일을 담당 합니다. 이 문서는 Azure 스택 인프라의 보안 상태에 기본 기술 자료를 제공합니다.

## <a name="data-at-rest-encryption"></a>나머지 암호화에는 데이터
모든 Azure 스택 인프라 및 테 넌 트 데이터는 Bitlocker를 사용 하 여 암호화 됩니다. 이 암호화에 대해 물리적 손실이 나 도난 Azure 스택 저장소 구성 요소를 보호합니다. 

## <a name="data-in-transit-encryption"></a>데이터 전송 암호화를
Azure 스택 인프라 구성 요소는 TLS 1.2를 사용 하 여 암호화 된 채널을 사용 하 여 통신 합니다. 암호화 인증서는 자체 인프라에 의해 관리 됩니다. 

Azure 스택 포털 또는 REST 끝점 같은 모든 외부 인프라 끝점 보안 통신을 위한 TLS 1.2를 지원 합니다. 이러한 끝점에 대 한 암호화 인증서를 제 3 자 또는 엔터프라이즈 인증 기관에서 제공 되어야 합니다. 

이러한 외부 끝점에 대 한 자체 서명 된 인증서를 사용할 수 있지만 Microsoft 강력 하 게 사용 하지 말라고 해당 합니다. 

## <a name="secret-management"></a>보안 관리
Azure 스택 인프라는 다양 한 암호와 같은 기밀 정보를 사용 하 여 작동 합니다. 대부분의는 자동으로 24 시간 마다 회전 하는 그룹 관리 서비스 계정에 있기 때문에 자주 회전 됩니다.

그룹 관리 서비스 계정 권한 있는 끝점에 대 한 스크립트와 함께 수동으로 회전 가능 하지 않은 나머지 비밀 정보입니다.

## <a name="code-integrity"></a>코드 무결성
Azure 스택에서 최신 Windows Server 2016 활용 보안 기능입니다. 그 중 하나가 Windows Defender Device Guard 등 응용 프로그램 허용 목록은 고 보장 하는 권한이 부여 된 Azure 스택 인프라 내에서 코드를 실행 하는 경우 

권한 있는 코드에서 Microsoft 또는 OEM 업체에서 서명한 있으며 Microsoft에서 정의한 정책에 지정 된 허용 된 소프트웨어 목록에 포함 됩니다. 즉, Azure 스택 인프라에서 실행 되도록 승인 된 소프트웨어만 실행할 수 있습니다. 권한이 없는 코드를 실행 하는 차단 하 고 감사 생성 됩니다.

또한 Device Guard 정책 Azure 스택 인프라에서 실행 제 3 자 에이전트 또는 소프트웨어를 방지 합니다.

## <a name="credential-guard"></a>자격 증명 보호
Azure 스택의 또 다른 Windows Server 2016 보안 기능에는 Azure 스택 인프라 자격 증명-Pass-the-hash 및 티켓 패스 전달 공격 으로부터 보호 하는 데 사용 되는 Windows Defender Credential Guard입니다.

## <a name="antimalware"></a>맬웨어 방지
Azure 스택 (Hyper-v 호스트 및 가상 컴퓨터)의 모든 구성 요소는 Windows Defender 바이러스 백신으로 보호 됩니다.

## <a name="constrained-administration-model"></a>제한 된 관리 모델
Azure 스택의 관리는 각각 특정 용도가 항목 점이 3 개를 사용 하 여 제어 됩니다. 
1. [관리자 포털](azure-stack-manage-portals.md) 매일 관리 작업에 대 한 지점을 클릭 환경을 제공 합니다.
2. Azure 리소스 관리자 PowerShell 및 Azure CLI 사용 되는 REST API를 통해 관리 포털의 모든 관리 작업을 표시 합니다. 
3. 특정 하위 수준 작업에 대 한 데이터 센터 통합 또는 시나리오를 지 원하는 예를 들어, 라는 PowerShell 끝점을 노출 하는 Azure 스택 [권한 있는 끝점](azure-stack-privileged-endpoint.md)합니다. 이 끝점 cmdlet의 허용 집합을 노출 하 고 감사 과도 하 게 됩니다.

## <a name="network-controls"></a>네트워크 컨트롤
Azure 스택 인프라는 여러 계층의 네트워크 액세스 제어 List(ACL) 함께 제공 됩니다. Acl은 인프라 구성 요소에 대 한 무단된 액세스를 방지 하 고 인프라 통신의 작동에 필요한 경로만 제한 합니다. 

네트워크 Acl 3 계층에 적용 됩니다.
1.  랙 스위치의 맨 위로 이동
2.  소프트웨어 정의 네트워크
3.  호스트 및 VM 운영 체제 방화벽 


