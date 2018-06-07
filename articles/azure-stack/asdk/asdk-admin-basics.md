---
title: Azure 스택 개발 키트 기본 사항 | Microsoft Docs
description: Azure 스택 개발 키트 (ASDK)에 대 한 기본 관리 작업을 수행 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 05dd42b049c75b9ea592ffe341f44e3b02b9757f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="asdk-administration-basics"></a>ASDK 관리 기본 사항 
처음 사용 Azure 스택 개발 키트 (ASDK) 관리 하는 경우 알아야 할 몇 가지 있습니다. 이 설명서는 평가 환경에서 Azure 스택 운영자 사용자 역할의 개요를 제공 하 고 테스트 사용자를 확인 하는 방법을 신속 하 게 생산성을 유지 될 수 있습니다.

먼저 검토 해야는 [Azure 스택 개발 키트 란?](asdk-what-is.md) 문서는 ASDK 및 제한 사항의 용도 이해 해야 합니다. 사용 해야 개발 키트 "샌드박스" 개발 하 고 비-프로덕션 환경에서 앱을 테스트 Azure 스택을 평가할 수 있습니다. 

Azure와 같은 Azure 스택 신속 하 게 innovates 하므로 ASDK는의 새로운 빌드를 정기적으로 해제 합니다. 그러나 Azure 스택 통합 시스템 배포 수는 ASDK를 업그레이드할 수 없습니다. 따라서 최신 빌드를 이동 하려는 경우 수행 해야 완전히 [는 ASDK 재배포](asdk-redeploy.md)합니다. 업데이트 패키지를 적용할 수 없습니다. 이 프로세스는 시간이 소요 되지만 이점은 사용 가능 하 게 되는 즉시 최신 기능을 사용해 시도 수입니다. 

## <a name="what-account-should-i-use"></a>사용할 계정은?
Azure 스택을 관리할 때 알고 있어야 하는 몇 가지 계정 고려 사항이 있습니다. 특히 배포에서는 Windows Server Active Directory Federation Services (AD FS)를 사용 하 여 Azure Active Directory (Azure AD) 대신 id 공급자로 합니다. 다음 계정 고려 사항 통합 Azure 스택 시스템과 ASDK 배포 모두에 적용 됩니다.

|계좌|Azure AD|AD FS|
|-----|-----|-----|
|로컬 관리자 (. \Administrator)|ASDK 호스트 관리자|ASDK 호스트 관리자|
|AzureStack\AzureStackAdmin|ASDK 호스트 관리자<br><br>Azure 스택 관리 포털에 로그인 할 수 있습니다.<br><br>보기 및 관리 서비스 패브릭 링에 대 한 액세스|ASDK 호스트 관리자<br><br>Azure 스택 관리 포털에 액세스할 수 없습니다<br><br>보기 및 관리 서비스 패브릭 링에 대 한 액세스<br><br>더 이상 기본 공급자 구독 (DP)의 소유자|
|AzureStack\CloudAdmin|에 액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.|에 액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.<br><br>ASDK 호스트에 로그인 할 수 없습니다.<br><br>기본 공급자 구독 (DP)의 소유자|
|Azure AD 전역 관리자|설치 중에 사용<br><br>기본 공급자 구독 (DP)의 소유자|해당 없음|
|

## <a name="what-tools-do-i-use-to-manage"></a>관리 도구에 대해 사용 합니까?
사용할 수 있습니다는 [Azure 스택 관리자 포털](https://adminportal.local.azurestack.external) 또는 PowerShell을 Azure 스택을 관리 합니다. 기본 개념을 파악 하는 가장 쉬운 방법은 포털을 통해 됩니다. PowerShell을 사용 하려는 경우 설치 해야 할 [Azure 스택에 대 한 PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) 및 [GitHub에서 Azure 스택 도구 다운로드](asdk-post-deploy.md#download-the-azure-stack-tools)합니다.

Azure 스택 하는 기본 배포, 관리 및 조직 메커니즘으로 Azure 리소스 관리자를 사용합니다. Azure 스택을 관리 하 고 사용자가 지원할 수 있도록 할 Azure 리소스 관리자에 대해 알아야 합니다. 참조 하 여 자세히 알아볼 수 있습니다는 [Azure 리소스 관리자 백서 시작](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)합니다.

## <a name="your-typical-responsibilities"></a>일반적인 사용자의 책임
사용자가 서비스를 사용 하려고 합니다. 자신의 관점에서 이러한 서비스를 사용할 수 있도록 주 역할은 합니다. 제공 되는 서비스에 알아볼 수 있습니다는 ASDK을 사용 하 고 서비스 이렇게 하는 방법에 따라 사용 가능한 [계획과 제안, 할당량 만들기](asdk-offer-services.md)합니다. 가상 컴퓨터 이미지와 같은 marketplace에 항목을 추가 해야 합니다. 가장 쉬운 방법은 [마켓플레이스 항목을 다운로드](asdk-marketplace-item.md) Azure 스택에 Azure에서.

> [!NOTE]
> 계획, 혜택 및 서비스를 테스트 하려는 경우 사용 해야는 [사용자 포털](https://portal.local.azurestack.external); 하지는 [관리자 포털](https://adminportal.local.azurestack.external)합니다.

서비스를 제공할 뿐만 아니라 모든 일반의 역할은 Azure 스택 연산자 실행 되도록 유지는 ASDK를 수행 해야 합니다. 이러한 의무는 다음 등이 포함:
- Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) 배포에 대 한 사용자 계정을 추가 합니다.
- 역할 기반 액세스 제어 (RBAC) 역할 (정당한 관리자 제한 되지 않습니다) 할당
- 인프라 상태 모니터
- 네트워크 및 저장소 리소스 관리
- 실패 한 개발 키트 호스트 컴퓨터 하드웨어를 교체 

## <a name="where-to-get-support"></a>지원을 받을 수 있는 위치
개발 키트에 대 한 유일한 지원 해결에 대 한 지원 관련 질문을가 [Microsoft Azure 스택 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)합니다. 관리 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 아이콘 (물음표)를 클릭 한 다음 클릭 하는 경우 **새로운 지원 요청**, 직접 포럼 사이트 열립니다. 이러한 포럼 정기적으로 모니터링 됩니다. 

> [!IMPORTANT]
> ASDK 평가 환경 이기 때문에 지원은 없습니다 공식를 통해 Microsoft 지원 CSS (고객 서비스)를 제공 합니다.

## <a name="next-steps"></a>다음 단계
[ASDK 배포](asdk-deploy.md)

