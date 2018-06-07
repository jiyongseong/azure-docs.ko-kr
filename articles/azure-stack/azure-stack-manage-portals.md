---
title: "관리자 포털을 사용 하 여 Azure 스택의 | Microsoft Docs"
description: "Azure 스택 연산자 관리자 포털을 사용 하는 방법에 설명 합니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 34d9d207225327758d535a11c870019f3db31cb0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>관리자 포털을 사용 하 여 Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

포털은 두 개가 Azure 스택의; 관리 포털 및 사용자 포털 (라고도 *테 넌 트* 포털). Azure 스택 연산자로 Azure 스택의 작업과 일상적인 관리에 대 한 관리자 포털을 사용할 수 있습니다. 

## <a name="access-the-administrator-portal"></a>관리자 포털에 액세스

먼저 수 있는 인지 확인 해야 개발 키트 환경에 대 한 [개발 키트 호스트에 연결할](azure-stack-connect-azure-stack.md) 가상 사설망 (VPN) 또는 원격 데스크톱 연결을 통해.

관리자 포털에 액세스 하려면 찾아보기를 포털 URL과 Azure 스택 연산자의 자격 증명을 사용 하 여 로그인 합니다. 통합된 된 시스템에 대 한 URL 다릅니다 포털 영역 이름 및 스택 Azure 배포의 외부 정규화 된 도메인 이름 (FQDN)에 따라.

| Environment | 관리자 포털 URL |   
| -- | -- | 
| 개발 키트| https://adminportal.local.azurestack.external  |
| 통합 시스템 | https://adminportal 합니다. &lt; *지역*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![관리자 포털](media/azure-stack-manage-portals/image1.png)

관리자 포털에서와 같은 작업을 수행할 수 있습니다.

* (시스템 상태, 업데이트, 용량 등을 포함 합니다.) 인프라 관리
* 마켓플레이스 채우기
* 계획 및 제안 만들기
* 사용자에 대 한 구독 만들기

에 **빠른 시작 자습서** 타일에서 가장 일반적인 작업에 대 한 온라인 설명서를 링크 합니다.
 
해야 하는 기능 관리자 포털에서 가상 컴퓨터, 가상 네트워크 및 저장소 계정과 같은 리소스를 만들려는 연산자에 대 한 경우에 [사용자 포털에 로그인](user/azure-stack-use-portal.md) 만들기 및 리소스를 테스트 합니다. (의 **가상 컴퓨터를 만들** 빠른 시작 자습서 타일에는 링크에는 관리자 포털에서 가상 컴퓨터를 만들 수 있지만 Azure 스택 초기 배포 후의 유효성 검사에이 절차는.)

## <a name="subscription-behavior"></a>구독 동작
 
관리자 포털에서 사용할 수 있는 구독 하나만 있습니다. 이 구독은 *공급자 구독 기본*합니다. 관리자 포털에서 사용 하기 위해 다른 구독을 추가할 수 없습니다.

Azure 스택 연산자로 관리자 포털에서 사용자가 (자신 포함)에 대 한 구독을 추가할 수 있습니다. 사용자 (자신 포함) 액세스 하 고 사용자 포털에서 이러한 구독을 사용할 수 있습니다. 사용자 포털 관리자 포털의 관리 또는 작동 기능에 대 한 액세스를 제공 하지 않습니다.

관리자 및 사용자 포털은 Azure 리소스 관리자의 개별 인스턴스에 의해 지원 됩니다. 리소스 관리자 분리로 인해 구독에는 포털 교차 하지 않는 합니다. 예를 들어 사용자 포털에 로그인 하면 Azure 스택 연산자로 사용 하는 경우 기본 공급자 구독을 액세스할 수 없습니다. 따라서 관리 기능에 액세스할이 필요는 없습니다. 공용 제공에서 자신에 대 한 구독을 만들 수 있지만 테 넌 트 사용자로 간주 됩니다.

  >[!NOTE]
  개발 키트 환경에서 Azure 스택 연산자와 동일한 테 넌 트 디렉터리에 사용자가 속할 경우 하지 차단 된 관리자 포털에 로그인 합니다. 그러나 이러한는 관리 기능 중 하나를 액세스할 수 없습니다. 또한 관리자 포털에서 구독 또는 액세스 제공 하는 사용자 포털에서 사용할 수는 추가할 수는 없습니다.

## <a name="administrator-portal-tips"></a>관리자 포털 팁

### <a name="customize-the-dashboard"></a>대시보드 사용자 지정

대시보드 기본 타일 집합이 포함 되어 있습니다. 클릭할 수 있는 **대시보드 편집** 기본 대시보드를 수정 하거나 클릭 **새 대시보드** 사용자 지정 대시보드를 추가 합니다. 대시보드에 타일을 쉽게 추가할 수 있습니다. 클릭 수는 예를 들어 **새로**를 마우스 오른쪽 단추로 클릭 **제공 + 계획**, 클릭 하 고 **대시보드에 고정**합니다.

### <a name="quick-access-to-online-documentation"></a>온라인 설명서에 대 한 빠른 액세스

Azure 스택 연산자 설명서에 액세스 하려면 관리 포털의 오른쪽 위 모서리에 도움말 및 지원 (물음표) 아이콘을 클릭 한 다음 클릭 **도움말 + 지원**합니다.

### <a name="quick-access-to-help-and-support"></a>도움말 및 지원에 대 한 빠른 액세스

관리자 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 아이콘 (물음표)를 클릭 한 다음 클릭 하는 경우 **새로운 지원 요청**,이 다음 중 하나:

- 통합된 시스템을 사용 하는 경우 열 수 있는 직접 지원 티켓와 Microsoft 고객 지원 서비스 (CSS) 사이트에 열립니다. "Where 지원을 받는" 섹션을 참조 [Azure 스택 관리 기본 사항](azure-stack-manage-basics.md) 이해 (oem) 면 하드웨어 공급 업체 지원 또는 Microsoft 기술 지원 서비스를 통해 이동 해야 하 합니다.
- 개발 키트를 사용 하 여이 작업 직접 Azure 스택 포럼 사이트를 엽니다. 이러한 포럼 정기적으로 모니터링 됩니다. 개발 키트 평가 환경 이기 때문에 Microsoft CSS 통해 제공 되는 공식 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 스택의 영역 관리](azure-stack-region-management.md)
