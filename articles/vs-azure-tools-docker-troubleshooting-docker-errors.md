---
title: "Visual Studio를 사용하여 Windows에서 Docker 클라이언트 오류 문제 해결 | Microsoft Docs"
description: "Visual Studio 2017을 사용하여 웹앱을 만들고 Docker에 배포하는 경우 발생하는 문제를 해결합니다."
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Docker를 사용한 Visual Studio 2017 개발 문제 해결

Visual Studio Tools for Docker로 작업할 때 응용 프로그램 빌드 또는 디버깅 중에 문제가 발생할 수도 있습니다. 다음은 몇 가지 일반적인 문제 해결 단계입니다.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>볼륨 공유 사용 안 함. Windows용 Docker CE 설정(Linux 컨테이너에만 해당)에 볼륨 공유 사용

이 문제를 해결하려면:

1. 알림 영역에서 **Windows용 Docker**를 마우스 오른쪽 단추로 클릭한 다음 **설정**을 선택합니다.
1. **공유 드라이브**를 선택하고 프로젝트가 있는 드라이브와 함께 시스템 드라이브를 공유합니다.

> [!NOTE]
> 파일이 공유됨으로 나타나면, 볼륨 공유를 다시 사용하도록 설정하기 위해 대화 상자의 맨 아래에 있는 "자격 증명 재설정..." 링크를 여전히 클릭해야 합니다.

![공유 드라이브](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>디버깅을 시작할 수 없음

한 가지 이유로 사용자 프로필 폴더에 부실 디버깅 구성 요소가 있는 것과 관련 있을 수 있습니다. 다음 디버그 세션에 최신 디버깅 구성 요소가 다운로드되도록 하려면 다음 명령을 실행하여 이러한 폴더를 제거합니다.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>응용 프로그램을 디버깅할 때 네트워킹에 국한되는 오류

호스트 컴퓨터에서 네트워크 관련 구성 요소를 새로 고침할 [정리 컨테이너 호스트 네트워킹](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking)에서 다운로드할 수 있는 스크립트를 실행 시도합니다.


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub 리포지토리

다른 문제가 발생하면, [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) 문제를 참조하세요.