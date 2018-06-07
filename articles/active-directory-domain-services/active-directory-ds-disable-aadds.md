---
title: "Azure Active Directory Domain Services 비활성화 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure Active Directory Domain Services 비활성화"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: f61f6df85e47bec32e147990d956a4409429a60c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Active Directory Domain Services 비활성화
이 문서에서는 Azure Portal을 사용하여 Azure AD 디렉터리에 대한 Azure AD(Active Directory) Domain Services를 사용하지 않도록 설정하는 방법을 설명합니다.

> [!WARNING]
> **삭제는 영구적이며 되돌릴 수 없습니다.**
> 주의하여 진행하세요. 관리되는 도메인을 삭제하는 경우:
  * 관리되는 도메인의 도메인 컨트롤러가 가상 네트워크에서 프로비전 해제되고 제거됩니다.
  * 관리되는 도메인의 데이터가 영구적으로 삭제됩니다. 여기에는 관리되는 도메인에서 만든 사용자 지정 OU, GPO, 사용자 지정 DNS 레코드, 서비스 주체, GMSA 등이 포함됩니다.
  * 관리되는 도메인에 가입된 컴퓨터는 도메인과의 트러스트 관계를 해제하고 도메인 가입을 해제해야 합니다.
  * 기업 AD 자격 증명을 사용하여 이러한 컴퓨터에 로그인 할 수 없습니다. 대신 컴퓨터에 대한 로컬 관리자 자격 증명을 사용하십시오.
관리되는 도메인을 삭제해도 Azure AD 디렉터리가 삭제되거나 디렉터리에 부정적인 영향을 주지는 않습니다.
>

Azure AD Domain Services 관리되는 도메인을 삭제하려면 다음 단계를 수행하십시오.
1. Azure Portal의 [Azure AD Domain Services 확장](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)으로 이동합니다.
2. 관리되는 도메인의 이름을 클릭합니다.

    ![삭제할 도메인 선택](./media/getting-started/domain-services-delete-select-domain.png)

3. **개요** 페이지에서 **삭제** 단추를 클릭합니다.

    ![도메인 삭제](./media/getting-started/domain-services-delete-domain.png)

4. 삭제를 확인하려면 관리되는 도메인의 DNS 도메인 이름을 입력합니다. 완료되면 **삭제** 단추를 클릭합니다.

    ![도메인 삭제 확인](./media/getting-started/domain-services-delete-domain-confirm.png)

관리되는 도메인인 약 15~20분 내에 삭제됩니다.

[의견 공유](active-directory-ds-contact-us.md) 를 고려하여 나중에 Azure AD Domain Services를 선택하는 데 도움이 되는 기능을 이해할 수 있습니다. 이 피드백은 서비스를 배포 요구 사항 및 사용 사례에 적합하도록 발전시키는 데 도움이 됩니다.
