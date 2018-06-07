---
title: "새 Azure 스택 테 넌 트 계정을 Azure Active Directory에 추가 | Microsoft Docs"
description: "Microsoft Azure 스택 개발 키트를 배포한 후 테 넌 트 포털을 탐색할 수 있도록 하나 이상의 테 넌 트 사용자 계정을 만들려면 해야 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Azure Active Directory에서 새 Azure Stack 테넌트 계정 추가
후 [Azure 스택 개발 키트 배포](azure-stack-run-powershell-script.md), 테 넌 트 포털을 탐색 하 고 제안 및 계획을 테스트할 수 있도록 테 넌 트 사용자 계정이 필요 합니다. 테 넌 트 계정을 만들 수 있습니다 [Azure 포털을 사용 하 여](#create-an-azure-stack-tenant-account-using-the-azure-portal) 또는 [PowerShell을 사용 하 여](#create-an-azure-stack-tenant-account-using-powershell)합니다.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Stack 테넌트 계정 만들기
Azure 포털을 사용하려면 Azure 구독이 있어야 합니다.

1. [Azure](https://portal.azure.com)에 로그인합니다.
2. Microsoft Azure 왼쪽 탐색 모음에서 **Active Directory**를 클릭합니다.
3. 디렉터리 목록에서 Azure Stack에 사용할 디렉터리를 클릭하거나 새 디렉터리를 만듭니다.
4. 이 디렉터리 페이지에서 **사용자**를 클릭합니다.
5. **사용자 추가**를 클릭합니다.
6. 에 **사용자 추가** 마법사는 **유형의 사용자** 목록에서 선택 **조직 내에서 새 사용자**합니다.
7. **사용자 이름** 상자에 사용자의 이름을 입력합니다.
8. **@** 상자에서 적절한 항목을 선택합니다.
9. 다음 화살표를 클릭합니다.
10. 에 **사용자 프로필** 마법사의 페이지 입력 **이름**, **성**, 및 **표시 이름**합니다.
11. 에 **역할** 목록에서 선택 **사용자**합니다.
12. 다음 화살표를 클릭합니다.
13. 에 **임시 암호 가져오기** 페이지 **만들기**합니다.
14. **새 암호**를 복사합니다.
15. 새 계정으로 Microsoft Azure에 로그인합니다. 메시지가 표시되면 암호를 변경합니다.
16. 에 로그인 `https://portal.local.azurestack.external` 테 넌 트 포털을 보려면 새 계정을 사용 합니다.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>PowerShell을 사용하여 Azure Stack 테넌트 계정 만들기
Azure 구독이 없는, 테 넌 트 사용자 계정을 추가 하려면 Azure 포털을 사용할 수 없습니다. 이 경우에 Azure Active Directory에 대 한 Windows PowerShell 모듈을 대신 사용할 수 있습니다.

> [!NOTE]
> Microsoft 계정 (Live ID)를 사용 하 여 Azure 스택 개발 키트를 배포 하는, 테 넌 트 계정을 만들려면 AAD PowerShell을 사용할 수 없습니다. 
> 
> 

1. 설치는 [Microsoft Online Services 로그인 도우미 RTW IT 전문가 위한](https://www.microsoft.com/en-us/download/details.aspx?id=41950)합니다.
2. 설치는 [Azure Active Directory에 대 한 Windows PowerShell 모듈 (64 비트 버전)](http://go.microsoft.com/fwlink/p/?linkid=236297) 엽니다.
3. 다음 cmdlet을 실행합니다.

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 새 계정으로 Microsoft Azure에 로그인 합니다. 메시지가 표시되면 암호를 변경합니다.
2. 에 로그인 `https://portal.local.azurestack.external` 테 넌 트 포털을 보려면 새 계정을 사용 합니다.

