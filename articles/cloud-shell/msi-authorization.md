---
title: Azure Cloud Shell에서 MIS 사용 | Microsoft Docs
description: Azure Cloud Shell에서 MSI를 통해 코드 인증
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="use-msi-in-azure-cloud-shell"></a>Azure Cloud Shell에서 MSI 사용

Azure Cloud Shell은 MSI(Managed Service Identities)를 통한 권한 부여를 지원합니다. Azure 서비스와 안전하게 통신하기 위해 이를 활용하여 액세스 토큰을 검색합니다.

## <a name="about-managed-service-identity-msi"></a>MSI(Managed Service Identity) 정보
클라우드 응용 프로그램을 빌드할 때 일반적으로 발생하는 문제 중 하나는 클라우드 서비스에 인증하기 위해 코드에 포함해야 하는 자격 증명을 안전하게 관리하는 방법입니다. Cloud Shell에서는 스크립트에 필요할 수 있는 자격 증명을 위해 Key Vault에서 검색을 인증해야 할 수 있습니다.

MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

## <a name="acquire-access-token-in-cloud-shell"></a>Cloud Shell에서 액세스 토큰 확보

다음 명령을 실행하여 MSI 액세스 토큰을 환경 변수 `access_token`으로 설정합니다.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>토큰 만료 처리

로컬 MSI 하위 시스템은 토큰을 캐시합니다. 따라서 원하는 대로 자주 호출할 수 있으며 다음과 같은 경우에 Azure AD에 대한 실시간 호출이 발생합니다.
- 캐시에서 토큰 부재로 인해 캐시 누락이 발생하는 경우
- 토큰이 만료된 경우

코드에서 토큰을 캐시하는 경우 토큰이 만료되었음을 리소스가 가리키는 시나리오를 처리하도록 준비해야 합니다.

토큰 오류를 처리하려면 [MSI 액세스 토큰 컬링에 대한 MSI 페이지](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[MSI에 대해 자세히 알아보기](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[MSI VM에서 액세스 토큰 획득](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
