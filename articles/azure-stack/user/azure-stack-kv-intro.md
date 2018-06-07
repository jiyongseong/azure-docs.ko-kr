---
title: "Azure 스택 키 자격 증명 모음 소개 | Microsoft Docs"
description: "Azure 스택 키 자격 증명 모음 키 및 암호를 관리 하는 방법에 대해 알아봅니다"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: mabrigg
ms.openlocfilehash: a50a03e70ccf014a8a9d33e0f177febed560853f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>주요 자격 증명 모음에서 Azure 스택 소개

## <a name="prerequisites"></a>필수 조건 

* Azure 키 자격 증명 모음 서비스를 포함 하는 제품에 등록 해야 합니다.  
* [PowerShell Azure 스택을 사용 하도록 구성 된](azure-stack-powershell-configure-user.md)합니다.
 
## <a name="key-vault-basics"></a>주요 자격 증명 모음 기본 사항
Azure 스택의 주요 자격 증명 모음에는 암호화 키를 보호 하 고 암호 클라우드 응용 프로그램 및 서비스를 사용 합니다. 자격 증명 모음 키를 사용 하 여 암호화할 수 있습니다 키와 암호를 같은:
   * 인증 키 
   * Storage 계정 키
   * 데이터 암호화 키
   * .pfx 파일
   * 암호

키 자격 증명 모음은 키 관리 프로세스를 간소화하고 데이터를 액세스하고 암호화 하는 키의 제어를 유지할 수 있습니다. 개발자는 개발 및 테스트(분)을 위한 키를 만든 다음, 프로덕션 키로 원활하게 마이그레이션할 수 있습니다. 보안 관리자는 필요한 경우 권한을 키로 부여(및 해지)할 수 있습니다.

Azure 스택 구독 사용 하 여 모든 사용자가 만들고 사용할 수 있는 주요 자격 증명 모음입니다. 키 자격 증명 모음에는 개발자와 보안 관리자 혜택, 있지만 조직에 대 한 다른 Azure 스택 서비스를 관리 하는 연산자 구현을 관리할 수 있습니다. 예를 들어 Azure 스택 연산자는 Azure 스택 구독으로 로그인 할 수는 키를 저장 한 다음 이러한 운영 작업을 담당 하는 조직에 대 한 자격 증명 모음을 만듭니다.

* 만들거나 키 또는 암호를 가져옵니다.
* 취소 또는 키 또는 암호를 삭제 합니다.
* 사용자 또는 응용 프로그램을 다음 관리 하거나 해당 키와 암호를 사용할 수 있도록 키 자격 증명 모음에 액세스 권한을 부여 합니다.
* 키 사용 구성 (예를 들어, 서명 또는 암호화) 합니다.

그런 다음 연산자 (Uri)를 응용 프로그램에서 호출할 Uniform Resource Identifier와 개발자를 제공할 수 있습니다. 연산자는 보안 관리자 키 사용 로깅 정보를 제공할 수도 있습니다.

개발자는 API를 사용하여 직접 키를 관리할 수도 있습니다. 자세한 내용은 키 자격 증명 모음 개발자 가이드를 참조 하세요.

## <a name="scenarios"></a>시나리오
다음 시나리오는 설명 방법을 주요 자격 증명 모음 보안 관리자 및 개발자의 요구를 충족할 수 있습니다.

### <a name="developer-for-an-azure-stack-application"></a>Azure 스택 응용 프로그램 개발자
**문제:** 서명 및 암호화 키를 사용 하는 Azure 스택에 대 한 응용 프로그램을 작성 합니다. 이러한 키를 내 응용 프로그램에서 외부 솔루션은 지리적으로 분산 하는 응용 프로그램에 적합 합니다.

**문:** 키 자격 증명 모음에 저장 되 고 필요한 경우는 URI에서 호출 합니다.

### <a name="developer-for-software-as-a-service-saas"></a>Saas ()으로 소프트웨어에 대 한 개발자
**문제:** 내 고객의 키와 암호에 대 한 책임이 나 잠재적인 책임 않겠습니다. 수행할 가장 좋은 경우를 제공 하는 핵심 소프트웨어 기능을 수행 하 집중할 수 있도록 고객 소유 하 고 해당 키를 관리 합니다.

**문:** 고객 Azure 스택에 자체 키를 가져올 수 있습니다 및 다음으로 관리 관리 합니다. 

### <a name="chief-security-officer-cso"></a>Chief 보안 책임자 (CSO)
**문제:** 내 조직 키 수명 주기를 제어 하며 키 사용을 모니터링할 수 있는지 확인 하고자 합니다.

**문:** 키 자격 증명 모음은 Microsoft 참조 하지 않거나 키를 추출할 수 있도록 설계 되었습니다. 응용 프로그램을 고객의 키를 사용 하 여 암호화 작업을 수행 해야 하는 경우 키 자격 증명 모음에서 응용 프로그램을 대신 하 여 키를 사용 합니다. 응용 프로그램에서 고객의 키를 표시하지 않습니다. 여러 Azure 스택 서비스 및 리소스를 사용 하지만 Azure 스택의 단일 위치에서 키를 관리할 수 있습니다. 자격 증명 모음 지원 및 응용 프로그램을 사용할 수 자격 증명 모음에에서 있는 Azure 스택, 지역 것에 관계 없이 단일 인터페이스를 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 Azure 스택의 주요 자격 증명 모음 관리](azure-stack-kv-manage-portal.md)  
* [PowerShell을 사용 하 여 Azure 스택의 주요 자격 증명 모음 관리](azure-stack-kv-manage-powershell.md)

