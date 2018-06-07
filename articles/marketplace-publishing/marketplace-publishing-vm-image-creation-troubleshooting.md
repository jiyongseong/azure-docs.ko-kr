---
title: VHD를 만드는 동안 일반적인 문제 해결 방법 | Microsoft Docs
description: VHD 만드는 동안 문제를 해결하는 일반적인 질문 및 문제에 대답합니다.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: mbaldwin
ms.openlocfilehash: 65361ad5bd7c3311c428b64b8476ec8f2ea2d17b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>VHD를 만드는 동안 발생하는 일반적인 문제 해결 방법
이 문서는 가상 머신 솔루션을 게시하거나 관리하는 동안 문제가 발생하거나 관련된 일반적인 질문이 있는 Azure Marketplace 게시자 및/또는 공동 관리자를 돕기 위해 제공됩니다.

1. 호스트의 이름을 변경하려면 어떻게 해야 하나요?
   
    VM이 만들어지면 사용자는 호스트의 이름을 업데이트할 수 없습니다.
2. 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하려면 어떻게 해야 하나요?
   
   * [Windows VM에 대한 참조](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Linux VM에 대한 참조](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. 새 SSH 인증서를 생성하려면 어떻게 해야 하나요?
   
   링크를 참조하세요. [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. 열린 VPN 인증서를 구성하려면 어떻게 해야 하나요?
   
   링크를 참조하세요. [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Microsoft Azure 가상 머신 환경(infrastructure-as-a-service)에서 Microsoft 서버 소프트웨어를 실행하기 위한 지원 정책은 무엇인가요?
   
   링크를 참조하세요. [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Virtual Machines는 고유 식별자를 가지고 있나요?
   
   Azure는 모든 VM에 Azure VM 고유 ID를 인코딩합니다. 이 블로그 및 문서에서 세부 정보를 참조하세요.
7. VM에서 시작 태스크에 있는 사용자 지정 스크립트 확장을 어떻게 관리할 수 있나요?
   
   링크를 참조하세요. [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Premium Storage에 업로드된 VHD를 사용하여 Azure Portal에서 VM을 만들려면 어떻게 해야 하나요?
   
   이 기능을 지원하지 않습니다.
9. 32비트 앱은 Azure Marketplace에서 지원되나요?
   
   지원 정책에 대한 자세한 내용은 [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672) 링크를 참조하세요.
10. 내 VHD에서 이미지를 만들려고 할 때마다 PowerShell에서 "리소스인 이미지 리포지토리로 VHD를 이미 등록했습니다." 오류가 표시됩니다. 이미지를 만들지 않았거나 Azure에서 이 이름을 가진 이미지를 찾지 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?
    
    이 문제는 일반적으로 사용자가 이 VHD에서 VM을 프로비전하고 해당 VHD에 대한 잠금이 있는 경우에 발생합니다. 이 VHD에서 할당된 VM이 없는지 확인합니다. 오류가 계속되는 경우 이 링크를 사용하거나 관련된 게시 포털에서 지원 티켓을 제기합니다.(질문 11의 답변에서 세부 정보를 제공).