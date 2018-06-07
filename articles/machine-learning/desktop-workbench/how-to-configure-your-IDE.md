---
title: IDE와 함께 실행되도록 Azure Machine Learning Workbench를 구성하려면 어떻게 하나요?  | Microsoft Docs
description: IDE와 함께 실행되도록 Azure Machine Learning Workbench를 구성하는 방법에 대한 가이드.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 8a4bb8d825f89b08e9b7514d67ae2d2512cceec9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>IDE와 함께 실행되도록 Azure Machine Learning Workbench를 구성하는 방법 

인기 있는 Python IDE(통합 개발 환경)와 함께 실행되도록 Azure Machine Learning Workbench를 구성할 수 있습니다. 이렇게 하면 데이터 준비, 코드 작성, 실행 추적 및 연산화 간에 데이터 과학 개발 환경을 원활하게 이동할 수 있습니다. 현재 지원되는 IDE는 다음과 같습니다.
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>워크벤치 구성
1. 앱 왼쪽 상단 모서리에서 **파일** 메뉴 클릭 
2. 플라이아웃에서 **프로젝트 IDE 구성** 옵션 선택 
3. **이름** 필드에 `VS Code` 또는 `PyCharm` 입력(이름은 임의)
4. IDE 실행 파일의 위치(실행 파일 이름 및 확장명이 모두 포함된)를 **실행 경로**에 입력

### <a name="default-install-path-for-visual-studio-code"></a>Visual Studio Code의 기본 설치 경로  

* Windows 32비트 - `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64비트 - `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS - .app 경로(예: `/Applications/Visual Studio Code.app`)를 선택하면 앱에서 나머지 경로를 자동으로 추가합니다. 실행 파일의 전체 경로는 기본적으로 `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`입니다. VS Code에서 `Shell Command: Install 'code' command in PATH` 명령을 실행한 경우 `/usr/local/bin/code`에서 VS Code 스크립트를 참조할 수도 있습니다.

### <a name="default-install-path-for-pycharm"></a>PyCharm의 기본 설치 경로 

* Windows 32비트 - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64비트 - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS - .app 경로(예: “/Applications/PyCharm CE.app”)를 선택하면 앱에서 나머지 경로를 자동으로 추가합니다. 실행 파일의 전체 경로는 기본적으로 `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`입니다. bin 폴더 `/usr/local/bin/charm`에서도 PyCharm을 찾을 수 있습니다.

## <a name="open-project-in-ide"></a>IDE에서 프로젝트 열기 
구성이 완료되면 Azure Machine Learning Workbench에서 **파일** 메뉴를 열고 **프로젝트 열기(<IDE_Name>)**를 클릭하여 Azure Machine Learning 프로젝트를 열 수 있습니다. 이 작업은 구성된 IDE에서 현재 활성 프로젝트를 엽니다. _참고: 프로젝트에 없는 경우 **프로젝트 열기(<IDE_Name>)**가 비활성화됩니다._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Visual Studio Code에서 통합 터미널 구성

### <a name="windows"></a>Windows 
PowerShell 대신 cmd가 되도록 기본 셸이 재정의되었습니다. **프로젝트 (<IDE_Name>) 열기**를 클릭하면 다음과 같은 프롬프트가 표시됩니다. 

_셸: `C:\windows\System32\cmd.exe`(작업 영역 설정으로 정의됨)가 터미널에서 실행되도록 허용하시겠습니까?_

Azure ML Workbench 명령줄 인터페이스와 매끄럽게 작동하도록 셸을 구성하려면 `yes`를 선택합니다.

### <a name="mac"></a>Mac
Mac에서 Visual Studio Code의 통합 터미널을 사용하여 `az` 명령을 실행하려면 `terminal.integrated.env.osx` 키 아래에서 프로젝트의 `.vscode/settings.json` 파일에 있는 `PATH`와 동일한 값이 되도록 `PATH`를 수동으로 설정해야 합니다. 터미널에서 다음 명령을 실행하면 됩니다. `PATH=<PATH in .vscode/settings>`
