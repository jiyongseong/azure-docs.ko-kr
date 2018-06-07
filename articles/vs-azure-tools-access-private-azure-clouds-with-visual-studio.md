---
title: Visual Studio로 사설 Azure 클라우드에 액세스 | Microsoft Docs
description: Visual Studio를 사용하여 사설 클라우드 리소스에 액세스하는 방법에 대해 알아봅니다.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 0160ac6db2b92d5a30a19dd444d01a8558b3eed3
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Visual Studio로 사설 Azure 클라우드에 액세스

기본적으로 Visual Studio는 Azure 클라우드 REST 엔드포인트를 지원합니다. 이 문서에서는 사설 클라우드의 인증서를 사용하여 Visual Studio에서 사설 클라우드에 액세스하고 상호 작용하는 방법에 대해 알아봅니다.

1. 사설 클라우드에 대한 Azure Portal에서 게시 설정 파일을 다운로드하거나 게시 설정 파일에 대해 관리자에게 문의합니다. 파일 확장명은 `.publishsettings`입니다.

1. Visual Studio **서버 탐색기**에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭하고 **구독 관리 및 필터링**을 선택합니다.

    ![구독 명령 관리](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. **Microsoft Azure 구독 관리** 대화 상자에서 **인증서** 탭을 선택한 다음 **가져오기**를 선택합니다.

    ![Azure 인증서 가져오기](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. **Microsoft Azure 구독 가져오기** 대화 상자에서 **찾아보기**를 선택합니다.

    ![Microsoft Azure 구독 가져오기 대화 상자의 찾아보기 단추](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. **열기** 대화 상자에서 게시 설정 파일을 저장한 디렉터리로 이동하여 파일을 선택한 다음 **열기**를 선택합니다.

    ![게시 설정 파일 선택](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. **Microsoft Azure 구독 가져오기** 대화 상자로 돌아가면 **찾아보기**를 선택합니다.

    ![게시 설정 파일 가져오기](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    게시 설정 파일에서 Visual Studio로 인증서를 가져왔으므로 이제 사설 클라우드 리소스와 상호 작용할 수 있습니다.

