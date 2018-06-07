---
title: "Azure Logic Apps에서 인증서를 사용하여 B2B 메시지 보호 | Microsoft Docs"
description: "인증서를 추가하여 엔터프라이즈 통합 팩을 사용하여 B2B 메시지를 보호합니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 708bdcddede71186c48ae7d4034cc9df0bd61391
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
---
# <a name="secure-b2b-messages-with-certificates"></a>인증서를 사용하여 B2B 메시지 보호

경우에 따라 B2B 통신을 기밀로 유지해야 합니다. 통합 앱(특히, 논리 앱)에서 B2B 통신을 보호할 수 있도록 통합 계정에 인증서를 추가할 수 있습니다. 인증서는 전자 통신에서 참가자의 ID를 확인하는 디지털 문서입니다.
인증서를 통해 다음과 같은 방법으로 통신을 보호할 수 있습니다.

* 메시지 콘텐츠 암호화
* 디지털로 메시지에 서명  

엔터프라이즈 통합 앱에서 다음과 같은 인증서를 사용할 수 있습니다.

* 공용 인증서는 CA(인증 기관)에서 구입해야 합니다
* 개인 인증서는 직접 발행할 수 있습니다. 이러한 인증서를 자체 서명된 인증서라고도 합니다.

## <a name="upload-a-public-certificate"></a>공용 인증서 업로드

B2B 기능이 포함된 논리 앱에서 *공용 인증서*를 사용하려면 먼저 통합 계정에 인증서를 업로드해야 합니다. 사용자가 만드는 [규약](logic-apps-enterprise-integration-agreements.md)에서 해당 속성을 정의한 후에 인증서를 사용하여 B2B 메시지를 안전하게 보호할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합"을 입력한 다음, **통합 계정**을 선택합니다.

   ![통합 계정 찾기](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. **통합 계정**에서 인증서를 추가하려는 통합 계정을 선택합니다.

   ![인증서를 추가할 통합 계정 선택](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. **인증서** 타일을 선택합니다.  

   !["인증서" 선택](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. **인증서** 아래에서 **추가**를 선택합니다.

   !["추가" 선택](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. **인증서 추가** 아래에서 인증서에 대한 세부 정보를 입력합니다.
   
   1. 인증서 **이름**을 입력합니다. 인증서 형식에서 **공용**을 선택합니다.

   2. **인증서** 상자의 오른쪽에서 폴더 아이콘을 선택합니다. 
   업로드하려는 인증서 파일을 찾고 선택합니다. 
   작업을 완료하면 **확인**을 선택합니다.

      ![공용 인증서 업로드](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure에서는 선택 사항을 확인한 후에 인증서를 업로드합니다.

   ![새 인증서 표시](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>개인 인증서 업로드

B2B 기능이 포함된 논리 앱에서 *개인 인증서*를 사용하려면 먼저 통합 계정에 인증서를 업로드해야 합니다. 먼저 [Azure Key Vault](../key-vault/key-vault-get-started.md)에 추가할 개인 키가 있어야 합니다. 

사용자가 만드는 [규약](logic-apps-enterprise-integration-agreements.md)에서 해당 속성을 정의한 후에 인증서를 사용하여 B2B 메시지를 안전하게 보호할 수 있습니다.

> [!NOTE]
> 개인 인증서의 경우 메시지에 서명하고 암호화하기 위해 [AS2 규약](logic-apps-enterprise-integration-as2.md) 보내기 및 받기 설정에서 표시하도록 해당 공용 인증서를 추가해야 합니다.

1. [개인 키를 Azure Key Vault에 추가](../key-vault/key-vault-get-started.md#add)하고 **키 이름**을 입력합니다.
   
2. Azure Logic Apps에 Azure Key Vault에서 작업을 수행할 수 있는 권한을 부여합니다. Logic Apps 서비스 주체에게 액세스 권한을 부여하려면 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) PowerShell 명령을 사용합니다. 예:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. [Azure Portal](https://portal.azure.com)에 로그인합니다.

4. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합"을 입력한 다음, **통합 계정**을 선택합니다.

   ![통합 계정 찾기](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. **통합 계정**에서 인증서를 추가하려는 통합 계정을 선택합니다.

6. **인증서** 타일을 선택합니다.  

   ![인증서 타일 선택](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. **인증서** 아래에서 **추가**를 선택합니다.   

   ![추가 단추 선택](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. **인증서 추가** 아래에서 인증서에 대한 세부 정보를 입력합니다.
   
   1. 인증서 **이름**을 입력합니다. 인증서 형식에서 **개인**을 선택합니다.

   2. **인증서** 상자의 오른쪽에서 폴더 아이콘을 선택합니다. 
   업로드하려는 인증서 파일을 찾고 선택합니다. 
   또한 **리소스 그룹**, **Key Vault** 및 **키 이름**을 선택합니다. 
   작업을 완료하면 **확인**을 선택합니다.

      ![인증서 추가](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure에서는 선택 사항을 확인한 후에 인증서를 업로드합니다.

   ![새 인증서 표시](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>다음 단계

* [B2B 규약 만들기](logic-apps-enterprise-integration-agreements.md)
