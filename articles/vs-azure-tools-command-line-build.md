---
title: Azure에 대한 명령줄 빌드 | Microsoft Docs
description: Azure에 대한 명령줄 빌드
services: visual-studio-online
author: ghogen
manager: douge
assetId: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: 7d0138abb07aea46ad8d0069c87964b393347dcf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="building-azure-projects-from-the-command-line"></a>명령줄에서 Azure 프로젝트 빌드
Microsoft Build Engine(MSBuild)을 사용하여 Visual Studio가 설치되지 않은 빌드 랩 환경에 제품을 빌드할 수 있습니다. MSBuild는 Microsoft에서 확장 가능하고 완전히 지원되는 프로젝트 파일에 대한 XML 형식을 사용합니다. MSBuild 파일 형식으로 하나 이상의 플랫폼 및 구성에 대해 어떤 항목을 빌드해야 하는지 설명할 수 있습니다

명령줄에서 MSBuild를 실행할 수도 있으며 이 항목에서는 그 방법을 설명합니다. 명령줄에서 속성을 설정하여 프로젝트의 특정 구성을 빌드할 수 있습니다. 마찬가지로, MSBuild가 빌드할 대상을 정의할 수 있습니다. 명령줄 매개 변수 및 MSBuild에 대한 자세한 내용은 [MSBuild 명령줄 참조](https://msdn.microsoft.com/library/ms164311.aspx)를 참조하세요.

## <a name="msbuild-parameters"></a>MSBuild 매개 변수
패키지를 만드는 가장 간단한 방법은 `/t:Publish` 옵션으로 MSBuild를 실행하는 것 입니다. 기본적으로 이 명령은 프로젝트의 루트 폴더를 기준으로 디렉터리를 만듭니다(예: `<ProjectDirectory>\bin\Configuration\app.publish\`). Azure 프로젝트를 빌드하면 패키지 파일 자체와 함께 제공되는 구성 파일의 두 파일이 생성됩니다.

* 패키지 파일(`project.cspkg`)
* 구성 파일(`ServiceConfiguration.TargetProfile.cscfg`)

기본적으로 각 Azure 프로젝트에는 로컬(디버깅) 빌드용 서비스 구성 파일 하나와 클라우드(스테이징 또는 프로덕션) 빌드용 서비스 구성 파일 하나가 포함되지만 필요에 따라 서비스 구성 파일을 추가하거나 제거할 수 있습니다. Visual Studio 내에서 패키지를 빌드하면 패키지와 함께 포함할 서비스 구성 파일을 묻는 메시지가 표시됩니다. MSBuild를 사용하여 패키지를 빌드하면 로컬 서비스 구성 파일이 기본적으로 포함됩니다. 다른 서비스 구성 파일을 포함하려면 MSBuild 명령(`MSBuild /t:Publish /p:TargetProfile=ProfileName`)의 `TargetProfile`속성을 설정합니다.

저장된 패키지 및 구성 파일에 대해 다른 디렉터리를 사용하려는 경우 후행 백슬래시 구분 기호를 포함하는 `/p:PublishDir=Directory\`옵션을 사용하여 경로를 설정합니다.

## <a name="next-steps"></a>다음 단계
패키지를 빌드한 후에 Azure에 배포할 수 있습니다.