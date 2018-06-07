---
title: Azure에서 Ansible을 사용하기 위한 도구
description: Azure에서 Ansible용 개별 도구 설치 및 사용
ms.service: ansible
keywords: ansible, azure, 개발, 도구, vs 코드, visual studio code, 확장
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Azure에서 Ansible을 사용하기 위한 도구

다음 도구를 사용하면 Ansible 및 Azure를 더 쉽고 효율적으로 작업할 수 있습니다.

## <a name="visual-studio-code-extension-for-ansible"></a>Ansible용 Visual Studio Code 확장

[Ansible용 Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)은 Visual Studio Code에서 Ansible을 사용하기 위한 몇 가지 기능을 제공합니다.

- Ansible 문서에 입력할 때 Ansible 지시문, 모듈 및 플러그인이 자동 완성됩니다.
- Ansible 플레이북을 제작할 때 &lt;Ctrl>&lt;Space>를 클릭하면 코드 조각이 표시됩니다.
- 구문 강조를 통해 Ansible 플레이북 코드가 YAML 구문에 따라 다양한 색상과 글꼴로 표시됩니다.
- Visual Studio Code 터미널 창에서 Ansible 플레이북을 실행할 수 있습니다.
    - (Windows 전용)Docker 컨테이너에서 Aniable을 실행할 수 있습니다.
    - (Linux 및 macOS)Docker 컨테이너 또는 로컬 Ansible 설치에서 Ansible을 실행할 수 있습니다. 
- Azure Cloud Shell에서 Ansible 플레이북을 실행할 수 있습니다.