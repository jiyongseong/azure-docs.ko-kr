---
title: "Visual Studio를 설치 하 고 Azure 스택에 연결 | Microsoft Docs"
description: "Visual Studio를 설치 하 고 Azure 스택에 연결 하는 데 필요한 단계에 알아봅니다"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: e73aba26a2409c710d962b9df45f6601b0e05340
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Visual Studio를 설치 하 고 Azure 스택에 연결

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Visual Studio를 사용 하 여 작성 하 고 Azure 리소스 관리자 배포에 [템플릿](user/azure-stack-arm-templates.md) Azure 스택의 합니다. 사용할 수 있습니다이 문서에 설명 된 단계를 Visual Studio 설치에서 [Azure 스택 개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)를 통해 연결 된 경우는 Windows 기반 외부 클라이언트와 주고 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)합니다. 다음이 단계는 Visual Studio 2015 Community Edition의 새 설치를 수행 합니다. 에 대해 자세히 알아보세요 [공존](https://msdn.microsoft.com/library/ms246609.aspx) 다른 Visual Studio 버전 간에 합니다.

## <a name="install-visual-studio"></a>Visual Studio 설치
1. 다운로드 및 실행 된 [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)합니다.             
2. 검색할 **2.9.6-Microsoft Azure SDK와 Visual Studio Community 2015**, 클릭 **추가**, 및 **설치**합니다.

    ![WebPI 스크린샷 설치 단계](./media/azure-stack-install-visual-studio/image1.png) 

3. 제거는 **Microsoft Azure PowerShell** Azure SDK의 일부로 설치 된 합니다.

    ![Azure PowerShell에 대 한 추가/제거 프로그램 인터페이스의 스크린 샷](./media/azure-stack-install-visual-studio/image2.png) 

4. [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)

5. 설치가 완료 되는 운영 체제를 다시 시작 합니다.

## <a name="connect-to-azure-stack"></a>Azure Stack에 연결

1. Visual Studio를 시작합니다.

2. **보기** 메뉴 선택 **클라우드 탐색기**합니다.

3. 새 창에서 선택 **계정 추가** 및 Azure Active Directory 자격 증명을 사용 하 여 로그인 합니다.  
    ![클라우드 탐색기의 스크린 샷에서 한 번에 로그인 하 고 Azure 스택에 연결](./media/azure-stack-install-visual-studio/image6.png)

로그인 한 다음을 할 수 있습니다 [템플릿을 배포할](user/azure-stack-deploy-template-visual-studio.md) 하거나 사용자 고유의 템플릿을 만들 수 있는 사용 가능한 리소스 유형과 리소스 그룹을 찾습니다.  

## <a name="next-steps"></a>다음 단계

 - [Azure Stack용 템플릿 개발](user/azure-stack-develop-templates.md)
