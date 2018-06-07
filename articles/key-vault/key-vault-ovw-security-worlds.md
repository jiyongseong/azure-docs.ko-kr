---
ms.assetid: 
title: "Azure Key Vault 보안 권역 | Microsoft Docs"
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: aeab36153d3a4e004d12206bab92cea9b30400ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault 보안 권역 및 지리적 경계

Azure Key Vault는 다중 테넌트 서비스이며, 각 Azure 위치의 HSM(하드웨어 보안 모듈) 풀을 사용합니다. 

동일한 지역의 Azure 위치에 있는 모든 HSM은 동일한 암호화 경계(Thales 보안 권역)를 공유합니다. 예를 들어 미국 동부 및 미국 서부는 미국 지리적 위치에 속하기 때문에 동일한 보안 권역을 공유합니다. 일본의 모든 Azure 위치는 동일한 보안 권역을 공유하고 오스트레일리아, 인도 등의 모든 Azure 위치도 마찬가지입니다. 

## <a name="backup-and-restore-behavior"></a>백업 및 복원 동작

다음 조건이 둘 다 true이기만 하면 특정 Azure 위치의 주요 자격 증명 모음에서 생성한 키 백업을 다른 Azure 위치의 주요 자격 증명 모음으로 복원할 수 있습니다.

- 두 Azure 위치는 동일한 지리적 위치에 속합니다.
- 두 주요 자격 증명 모음이 동일한 Azure 구독에 속합니다.

예를 들어 인도 서부에 있는 주요 자격 증명 모음의 키에 대해 지정된 구독이 생성한 백업은 동일한 구독 및 지리적 위치(인도 서부, 인도 중부 또는 인도 남부)에 있는 다른 주요 자격 증명 모음으로만 복원할 수 있습니다.

## <a name="regions-and-products"></a>지역 및 제품

- [Azure 지역](https://azure.microsoft.com/regions/)
- [지역별 Microsoft 제품](https://azure.microsoft.com/regions/services/)

지역은 표에서 주요 제목으로 표시된 보안 권역에 매핑됩니다.

예를 들어 지역별 제품 문서에서 **아메리카** 탭에는 미국 동부, 미국 중부, 미국 서부가 모두 아메리카 지역에 매핑되어 있습니다. 

>[!NOTE]
>예외로, 미국 국방부 동부 및 미국 국방부 중부에는 자체 보안 권역이 있습니다. 

마찬가지로, **유럽** 탭에는 북유럽 및 서유럽이 둘 다 유럽 지역에 매핑되어 있습니다. **아시아 태평양** 탭에서도 마찬가지입니다.



