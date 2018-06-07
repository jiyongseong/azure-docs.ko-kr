---
title: Azure 스택에서 API 버전 프로필을 관리 | Microsoft Docs
description: Azure 스택에서 API 버전 프로필에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: adbe88a44ac38868a68a6845c328ef4cf7fba60c
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604440"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure 스택에서 API 버전 프로필을 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

프로필 API는 Azure 리소스 공급자 및 Azure REST 끝점에 대 한 API 버전을 지정합니다. 프로필 API를 사용 하 여 여러 언어로 사용자 지정 클라이언트를 만들 수 있습니다. 각 클라이언트에 연결 하는 올바른 리소스 공급자 API 버전 Azure 스택 API 프로필을 사용 합니다.

각 리소스 공급자 API의 버전은 Azure 스택 호환 정확 하 게 정렬할 필요 없이 Azure 리소스 공급자와 함께 작동 하도록 앱을 만들 수 있습니다. 응용 프로그램, 프로필을 방금 맞춤 SDK 올바른 API 버전으로 되돌립니다.

이 항목 수 있습니다.

 - Azure 스택에 대 한 API 프로필을 이해 합니다.
 - API 프로필을 사용 하 여 솔루션을 개발 하는 방법을 알아봅니다.
 - 코드 관련 지침을 찾을 위치를 참조 하십시오.

## <a name="summary-of-api-profiles"></a>프로필 API 요약

- API 프로필은 Azure 리소스 공급자의 집합 및 해당 API 버전을 나타내는 데 사용 됩니다.
- 여러 Azure 클라우드 간에 서식 파일을 만들 수 API 프로필을 만들었습니다. 프로필은 호환 되 고 안정적 인터페이스에 대 한 요구 사항에 충족 하도록 설계 되었습니다.
- 프로필에는 년에 4 번 해제 됩니다.
- 세 개의 프로필 명명 규칙이 사용 됩니다.
    - **latest**  
        전역 Azure에서 릴리스된 최신 API 버전을 포함 합니다.
    - **yyyy-mm-dd-hybrid**  
    매기고 흐름에 해제,이 릴리스에서 중점적 일관성 및 안정성이 여러 클라우드 간에 합니다. 이 프로필에는 최적의 Azure 스택 호환성 대상 으로합니다.
    - **mm dd 프로필 yyyy** 최적의 안정성과, 그러면 최신 기능 사이 배치 합니다.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API 프로필 및 호환성 Azure 스택

최신 Azure API 프로필 Azure 스택와 호환 되지 않습니다. Azure 스택 솔루션에 대해 사용할 프로필을 식별 하는 다음과 같은 명명 규칙을 사용할 수 있습니다.

**최신**  
이 프로필에는 작동 하지 않는 Azure 스택이 글로벌 Azure에는 가장 최신 API 버전에 있습니다. **최신** 에 가장 큰 수의 주요 변경 내용입니다. 프로필은 안정성과 호환성을 다른 클라우드의 따로 배치 됩니다. 최신 API 버전을 사용 하려는 경우 **최신** 프로필을 사용 해야 합니다.

**Mm dd 하이브리드 Yyyy**  
이 프로필은 매년 3 월 및 9 월에 해제 됩니다. 이 프로필에는 최적의 안정성과 호환성을 다양 한 클라우드가 있습니다. **Mm dd 하이브리드 Yyyy** 글로벌 Azure 및 Azure 스택을 대상으로 하도록 설계 되었습니다. 이 프로필에 나열 된 Azure API 버전 Azure 스택에 표시 되는 것과 동일 하 게 됩니다. 하이브리드 클라우드 솔루션에 대 한 코드를 개발 하려면이 프로필을 사용할 수 있습니다.

**yyyy-mm-dd-profile**  
이 프로필은 글로벌 Azure에 대 한 6 월이 고 12 월에 해제 됩니다. 이 프로필은 Azure 스택;에 대해 작동 하지 않습니다. 일반적으로 많은 주요 변경 됩니다. 최적의 안정성과의 차이점, 그러면 최신 기능 사이 배치 되지만 **최신** 이 프로필은 및 **최신** 항상 상관 없이 최신 API 버전 이루어져 API 릴리스 되었습니다. 예를 들어 내일 새로운 API 버전 계산 API에 대 한 만들어지면 해당 API 버전에 나타납니다는 **최신**, 아니라는 **mm dd 프로필 yyyy** 이 프로필이 이미 존재 하기 때문에 있습니다.  **mm dd 프로필 yyyy** 년 6 월 또는 년 12 월 하기 전에 릴리스된 가장 최신 버전에 설명 합니다.

## <a name="azure-resource-manager-api-profiles"></a>Azure 리소스 관리자 API 프로필

Azure 스택 글로벌 Azure의 환경과 API 버전의 최신 버전을 사용 하지 않습니다. 솔루션을 만들 때 Azure 스택 호환 되는 각 Azure 리소스 공급자에 대 한 API 버전을 확인 해야 합니다.

대신 모든 리소스 공급자와 Azure 스택에서 지 원하는 특정 버전, 조사 하는 보다 API 프로필을 사용할 수 있습니다. 프로필에는 리소스 공급자 및 API 버전 집합을 지정합니다. 프로필에 지정 된 대상 api 버전에는 SDK 또는 SDK를 사용 하 여 빌드한 도구 되돌아갑니다. API 프로필을 통해 전체 템플릿에 적용 되는 프로필 버전을 지정할 수 있습니다 하 고 Azure 리소스 관리자 런타임 시 리소스의 올바른 버전을 선택 합니다.

API 프로필은 PowerShell, Azure CLI, SDK 및 Microsoft Visual Studio에 제공 된 코드와 같은 Azure 리소스 관리자를 사용 하는 도구와 함께 작동 합니다. 도구 및 Sdk 프로필을 사용 하 여 모듈 및 응용 프로그램을 빌드할 때 포함 하는 라이브러리의 버전을 읽을 수 있습니다.

예를 들어, 사용 된 저장소를 만드는 PowerShell을 사용 하 여 계정을 **Microsoft.Storage** 지 원하는 api 버전 2016-03-30을 api 버전 2015-12-01 Microsoft.Compute 리소스 공급자를 사용 하 여 VM 리소스 공급자 를 저장소에 대 한 2016-03-30을 지 원하는 PowerShell 모듈을 조회 해야 하 고 있는 모듈 계산에 대 한 2015-02-01을 지원 하 고 설치 합니다. 대신, 프로필을 사용할 수 있습니다. Cmdlet을 사용 하 여 * * 설치 프로필 * profilename * * *, 및 PowerShell 모듈의 올바른 버전을 로드 합니다.

마찬가지로, Python 기반 응용 프로그램을 빌드하는 Python SDK를 사용 하면 프로필을 지정할 수 있습니다. SDK는 스크립트에 지정 된 리소스 공급자에 대 한 올바른 모듈을 로드 합니다.

개발자는 솔루션 작성에 집중할 수 있습니다. api 버전, 리소스 공급자를 연구 하는 대신 및 클라우드를 함께 작동, 프로필을 사용 하 고 해당 프로필을 지 원하는 모든 클라우드 간에 코드 작동 하는지 알아야 합니다.

## <a name="api-profile-code-samples"></a>API 프로필 코드 샘플

프로필을 사용 하 여 Azure 스택이 있는 기본 언어와 솔루션을 통합할 수 있도록 코드 샘플을 찾을 수 있습니다. 현재 다음 언어에 대 한 지침 및 샘플을 찾을 수 있습니다.

- **PowerShell**  
사용할 수는 **AzureRM.Bootstrapper** 모듈 API 버전 프로필로 작업 하는 데 필요한 PowerShell cmdlet을 가져올 PowerShell 갤러리를 통해 사용할 수 있습니다. 자세한 내용은 참조 [PowerShell에 대 한 API를 사용 버전 프로필](azure-stack-version-profiles-powershell.md)합니다.
- **Azure CLI 2.0**  
Azure 스택 특정 API 버전 프로필을 사용 하도록 환경 구성을 업데이트할 수 있습니다. 자세한 내용은 참조 [Azure CLI 2.0에 대 한 API를 사용 버전 프로필](azure-stack-version-profiles-azurecli2.md)합니다.
- **GO**  
이동 SDK 프로필에는 서로 다른 서비스에서 다른 버전으로 여러 리소스 종류의 조합입니다. 프로필은 프로필에서 사용할 수 있는 / 경로에서 버전에는 **YYYY-월-일** 형식입니다. 자세한 내용은 참조 [GO에 대 한 API를 사용 버전 프로필](azure-stack-version-profiles-go.md)합니다.
- **Ruby**  
Azure 스택 리소스 관리자에 대 한 Ruby SDK 빌드 및 인프라를 관리 하는 데 유용한 도구를 제공 합니다. SDK에 대 한 리소스 공급자에는 계산, 가상 네트워크 및 저장소 Ruby 언어와 함께 포함 됩니다. 정보를 참조 하십시오. [Ruby 사용 하 여 API를 사용 버전 프로필](azure-stack-version-profiles-ruby.md)
- **Python**  
- Python SDK의 API 버전 프로필 Azure 스택 및 전역 Azure와 같은 다른 클라우드 플랫폼을 대상으로 지원 합니다. 하이브리드 클라우드에 대 한 솔루션을 만드는 프로필 API를 사용할 수 있습니다. 정보를 참조 하십시오. [python 버전 프로필 API를 사용](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)
* [프로필에서 지 원하는 리소스 공급자 API 버전에 대 한 세부 정보를 검토](azure-stack-profiles-azure-resource-manager-versions.md)합니다.