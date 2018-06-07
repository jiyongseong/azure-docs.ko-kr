---
title: "저장소 계정에 Azure 스택 | Microsoft Docs"
description: "Azure 스택 저장소 계정을 만드는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a599d809ba3da8487a6c5d115bf04922a546e6ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack의 Storage 계정
Storage 계정에는 Blob 및 Table service, 그리고 저장소 데이터 개체에 대한 고유한 네임스페이스가 있습니다. 기본적으로 계정에 대한 데이터는 저장소 계정 소유자에만 사용할 수 있습니다.

1. Azure 스택 POC 컴퓨터에 로그인 하려면 `https://adminportal.local.azurestack.external` 으로 [관리자](azure-stack-connect-azure-stack.md), 클릭 하 고 **새로** > **데이터 + 저장소**  >  **저장소 계정**합니다.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 에 **저장소 계정 만들기** 블레이드에서 저장소 계정에 대 한 이름 입력 합니다. 새 **리소스 그룹**, 기존 템플릿을 선택 하거나 다음을 클릭 **만들기** 저장소 계정을 만들 수 있습니다.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 새 저장소 계정의 보려면 클릭 **모든 리소스**, 저장소 계정에 대 한 다음 검색 하 고 해당 이름을 클릭 합니다.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>다음 단계
[Azure 리소스 관리자 템플릿 사용](user/azure-stack-arm-templates.md)

[Azure 저장소 계정에 대 한 자세한 내용은](../storage/common/storage-create-storage-account.md)

[Azure 스택 일관 된 Azure 저장소 유효성 검사 가이드 다운로드](http://aka.ms/azurestacktp1doc)
