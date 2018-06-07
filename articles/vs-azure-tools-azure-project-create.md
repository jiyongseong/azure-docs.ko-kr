---
title: Visual Studio에서 Azure 클라우드 서비스 프로젝트 만들기 | Microsoft Docs
description: Visual Studio에서 Azure 클라우드 서비스 프로젝트를 만드는 방법을 알아봅니다.
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 06213ecabf3669bf3b8cf2b8d73a4e8def359536
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Visual Studio에서 Azure 클라우드 서비스 프로젝트 만들기
Azure Tools for Visual Studio는 Azure에 대한 클라우드 서비스를 만들 수 있는 프로젝트 템플릿을 제공합니다. 프로젝트를 만든 후 Visual Studio에서 클라우드 서비스를 구성 및 디버그하고 Azure에 배포할 수 있습니다.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Visual Studio에서 Azure 클라우드 서비스 프로젝트를 만드는 단계
이 섹션에서는 하나 이상의 웹 역할을 사용하여 Visual Studio에서 Azure 클라우드 서비스 프로젝트를 만드는 과정을 안내합니다.  

1. 관리자 권한으로 Visual Studio를 시작합니다.

1. 주 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

1. Visual C# 또는 Visual Basic 프로젝트 템플릿 노드에서 **클라우드**를 선택한 다음 템플릿 목록에서 **Azure 클라우드 서비스**를 선택합니다.

    ![새 Azure 클라우드 서비스](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. 프로젝트 개발에 사용하려는 .NET Framework의 버전을 지정합니다.

1. 프로젝트에 대한 이름과 위치 및 솔루션에 대한 이름을 입력합니다. 

1. **확인**을 선택합니다.

1. **새 Microsoft Azure 클라우드 서비스** 대화 상자에서 추가하려는 역할을 선택하고 오른쪽 화살표 단추를 선택하여 솔루션에 역할을 추가합니다.

    ![새 Azure 클라우드 서비스 역할 선택](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. 추가한 역할의 이름을 바꾸려면 **새 Microsoft Azure 클라우드 서비스** 대화 상자에서 역할 위에 마우스를 놓고 상황에 맞는 메뉴에서 **이름 바꾸기**를 선택합니다. 역할을 추가한 후 솔루션 내에서(**솔루션 탐색기**에서) 역할의 이름을 바꿀 수도 있습니다.

    ![Azure 클라우드 서비스 역할 이름 바꾸기](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Visual Studio Azure 프로젝트에는 솔루션의 역할 프로젝트에 대한 연결이 있습니다. 또한 *서비스 정의 파일* 및 *서비스 구성 파일*도 포함됩니다.

- **서비스 정의 파일** - 필요한 역할, 끝점 및 가상 컴퓨터 크기를 포함하여 응용 프로그램에 대한 런타임 설정을 정의합니다. 
- **서비스 구성 파일** - 얼마나 많은 역할의 인스턴스가 실행되는지와 역할에 대해 정의된 설정의 값을 구성합니다. 

이러한 파일에 대한 자세한 내용은 [Visual Studio에서 Azure 클라우드 서비스에 대한 역할 구성](vs-azure-tools-configure-roles-for-cloud-service.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Visual Studio에서 Azure 클라우드 서비스 프로젝트의 역할 관리](./vs-azure-tools-cloud-service-project-managing-roles.md)
