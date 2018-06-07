---
title: Azure AD B2C에서 사용자 데이터 관리 | Microsoft Docs
description: Azure AD B2C에서 사용자 데이터를 삭제하거나 내보내는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.openlocfilehash: 414221c3e4942801b5792835d520ec936c8c4bbb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932531"
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Azure AD B2C에서 사용자 데이터 관리

 이 문서에서는 [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog)에 제공되는 작업을 사용하여 Azure AD(Active Directory) B2C에서 사용자 데이터를 관리할 수 있는 방법에 대한 정보를 제공합니다. 사용자 데이터 관리에는 데이터를 삭제하거나 감사 로그에서 데이터를 내보내는 기능이 포함됩니다.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>사용자 데이터 삭제

사용자 데이터는 Azure AD B2C 디렉터리와 감사 로그에 저장됩니다. 모든 사용자 감사 데이터는 Azure AD B2C에서 30일간 데이터 보존을 위해 유지됩니다. 이 30일 내에 사용자 데이터를 삭제하려면 [사용자 삭제](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) 작업을 사용하면 됩니다. 데이터가 상주할 수도 있는 Azure AD B2C 테넌트마다 삭제 작업이 필요합니다. 

Azure AD B2C의 모든 사용자에게는 개체 ID가 할당됩니다. 개체 ID는 Azure AD B2C에서 사용자 데이터를 삭제하는 데 사용할 수 있는 명확한 식별자를 제공합니다.  아키텍처에 따라 개체 ID는 재무, 마케팅 및 고객 관계 관리 데이터베이스와 같은 기타 서비스 전반에서 유용한 상관 관계 식별자가 될 수 있습니다.  

사용자에 대한 개체에 ID를 확보하는 가장 정확한 방법은 Azure AD B2C 인증 과정에서 확보하는 것입니다.  다른 방법을 사용하여 사용자로부터 유효한 데이터 요청을 수신하는 경우 사용자를 찾고 연결된 개체 ID를 기록하는 데 고객 서비스 지원 에이전트 검색 등의 오프라인 프로세스가 필요할 수 있습니다. 

다음 예제에는 가능한 데이터 삭제 흐름을 보여줍니다.

1. 사용자가 로그인하여 **내 데이터 삭제**를 선택합니다.
2. 응용 프로그램이 응용 프로그램의 관리 섹션 내에 데이터를 삭제하는 옵션을 제공합니다.
3. 응용 프로그램이 Azure AD B2C에 인증을 강제로 수행합니다. Azure AD B2C가 사용자의 개체 ID가 포함된 토큰을 응용 프로그램에 제공합니다. 
4. 응용 프로그램이 토큰을 수신하고 개체 ID가 Azure AD Graph API에 대한 호출을 통해 사용자 데이터를 삭제하는 데 사용됩니다. Azure AD Graph API가 사용자 데이터를 삭제하고 200 OK 상태 코드를 반환합니다.
5. 응용 프로그램이 개체 ID나 기타 식별자를 사용하여 필요에 따라 다른 조직 시스템에서 사용자 데이터 삭제를 조정합니다.
6. 응용 프로그램이 데이터 삭제를 확인하고 사용자에게 다음 단계를 제공합니다.

## <a name="export-customer-data"></a>고객 데이터 내보내기

Azure AD B2C에서 고객 데이터를 내보내는 프로세스는 삭제 프로세스와 비슷합니다.

Azure AD B2C 사용자 데이터는 다음 항목으로 제한됩니다.

- **Azure Active Directory에 저장된 데이터** - 데이터는 개체 ID나 이메일 또는 사용자 이름과 같은 로그인 이름을 사용한 Azure AD B2C 인증 사용자 과정에서 검색될 수 있습니다.  
- **사용자별 감사 이벤트 보고서** - 데이터는 개체 ID를 사용하여 인덱싱됩니다.

다음과 같은 데이터 내보내기 흐름 예제에서 응용 프로그램이 수행하는 것으로 언급된 단계는 백엔드 프로세스 또는 디렉터리에 관리자 역할이 있는 사용자가 수행할 수도 있습니다.

1. 사용자가 응용 프로그램에 로그인합니다. Azure AD B2C는 필요한 경우 다중 요소 인증을 사용하여 인증을 적용합니다.
2. 응용 프로그램은 사용자 자격 증명을 사용하여 사용자 특성을 검색하는 Azure AD Graph API 작업을 호출합니다. Azure AD Graph API는 속성 데이터를 JSON 형식으로 제공합니다. 스키마에 따라서 ID 토큰 콘텐츠는 사용자에 관한 모든 개인 데이터를 포함하도록 설정될 수 있습니다.
3. 응용 프로그램이 최종 사용자 감사 활동을 검색합니다. Azure AD Graph API가 이벤트 데이터를 응용 프로그램에 제공합니다.
4. 응용 프로그램이 사용자가 사용할 수 있도록 데이터를 집계합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 액세스 관리](manage-user-access.md)에서 사용자가 응용 프로그램에 액세스하는 방법을 관리하는 방법에 대해 알아봅니다.




