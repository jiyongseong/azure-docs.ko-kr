---
title: "Azure 스택 (서비스 관리자 및 테 넌 트)에 사용자별 리소스에 대 한 사용 권한 관리 | Microsoft Docs"
description: "서비스 관리자 또는 테 넌 트 RBAC 사용 권한을 관리 하는 방법에 설명 합니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: fenila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-role-based-access-control"></a>역할 기반 Access Control 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure Stack의 사용자는 구독, 리소스 그룹 또는 서비스의 각 인스턴스에 대해 읽기 권한자, 소유자 또는 참가자일 수 있습니다. 예를 들어 사용자 A 구독 하나에 판독기 사용 권한이 없지만 7 가상 컴퓨터에 소유자 권한을 갖게 될 수 있습니다.

 - 읽기 권한자: 사용자는 모든 항목을 볼 수 있지만 변경할 수 없습니다.
 - 참여자: 사용자는 리소스에 대한 액세스를 제외한 모든 것을 관리할 수 있습니다.
 - 소유자: 사용자는 리소스에 대한 액세스를 포함한 모든 것을 관리할 수 있습니다.

## <a name="set-access-permissions-for-a-user"></a>사용자에 대한 액세스 권한을 설정합니다

1. 관리하려는 리소스에 대한 소유자 권한이 있는 계정으로 로그인합니다.
2. 리소스에 대 한 블레이드에서 클릭는 **액세스** 아이콘 ![](media/azure-stack-manage-permissions/image1.png)합니다.
3. 에 **사용자** 블레이드에서 클릭 **역할**합니다.
4. 에 **역할** 블레이드에서 클릭 **추가** 사용자에 대 한 사용 권한을 추가 하 합니다.

## <a name="set-access-permissions-for-a-universal-group"></a>유니버설 그룹에 대 한 액세스 권한을 설정합니다 

> [!Note]  
Active Directory에만 적용할 수 있는 페더레이션 서비스 (AD FS).

1. 관리하려는 리소스에 대한 소유자 권한이 있는 계정으로 로그인합니다.
2. 리소스에 대 한 블레이드에서 클릭는 **액세스** 아이콘 ![](media/azure-stack-manage-permissions/image1.png)합니다.
3. 에 **사용자** 블레이드에서 클릭 **역할**합니다.
4. 에 **역할** 블레이드에서 클릭 **추가** 유니버설 그룹 Active Directory 그룹에 대 한 사용 권한을 추가 하 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack 테넌트 추가](azure-stack-add-new-user-aad.md)

