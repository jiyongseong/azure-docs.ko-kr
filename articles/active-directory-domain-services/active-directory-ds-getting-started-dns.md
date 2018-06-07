---
title: 'Azure Active Directory Domain Services: Azure 가상 네트워크에 대한 DNS 설정 업데이트 | Microsoft Docs'
description: Azure Active Directory Domain Services 시작
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 972b995d3768e765b95c136b3cfbee91ab0a88ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 활성화

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>작업 4: Azure 가상 네트워크에 대한 DNS 설정 업데이트
이전 구성 작업에서 디렉터리에 Azure Active Directory Domain Services를 사용하도록 성공적으로 설정했습니다. 다음 작업은 가상 네트워크 내의 컴퓨터가 이러한 서비스에 연결되고 해당 서비스를 사용할 수 있도록 합니다. 이 문서에서는 가상 네트워크에서 Azure Active Directory Domain Services를 사용할 수 있는 두 개의 IP 주소를 가리키도록 가상 네트워크에 대한 DNS 서버 설정을 업데이트합니다.

Azure Active Directory Domain Services를 사용하도록 설정한 가상 네트워크에 대한 DNS 서버 설정을 업데이트하려면 다음 단계를 완료합니다.

1. **개요** 탭에는 관리되는 도메인을 완전히 프로비전한 후 수행할 일련의 **필수 구성 단계**가 나열되어 있습니다. 첫 번째 구성 단계는 **가상 네트워크를 위한 DNS 서버 설정 업데이트**입니다.

    ![Domain Services - 완전히 프로비전한 후 개요 탭](./media/getting-started/domain-services-provisioned-overview.png)

2. 도메인이 완전히 프로비전되면 두 개의 IP 주소가 이 타일에 표시됩니다. 이들 IP 주소 각각은 관리되는 도메인에 대한 도메인 컨트롤러를 나타냅니다.

3. 첫 번째 IP 주소를 클립보드에 복사하려면 옆에 있는 복사 단추를 클릭합니다. 그런 다음 **DNS 구성 서버** 단추를 클릭합니다.

4. 첫 번째 IP 주소를 **DNS 서버** 블레이드에 있는 **DNS 서버 추가** 텍스트 상자에 붙여넣습니다. 왼쪽으로 스크롤 하여 두 번째 IP 주소를 복사하여 **DNS 서버 추가** 텍스트 상자에 붙여넣습니다.

    ![Domain Services - DNS 업데이트](./media/getting-started/domain-services-update-dns.png)

5. 가상 네트워크에 대한 DNS 서버를 업데이트를 완료하면 **저장**을 클릭합니다.

> [!NOTE]
> 다시 시작하고 나면 네트워크 상의 가상 머신만 새 DNS 설정을 얻습니다. 업데이트된 DNS 설정을 즉시 얻어야 하는 경우 포털, PowerShell 또는 CLI로 다시 시작을 트리거합니다.
>
>

## <a name="next-step"></a>다음 단계
[작업 5: Azure Active Directory Domain Services에 암호 해시 동기화 사용](active-directory-ds-getting-started-password-sync.md)
