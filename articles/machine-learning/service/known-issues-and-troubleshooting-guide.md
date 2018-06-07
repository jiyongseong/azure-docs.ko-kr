---
title: 알려진 문제 및 문제 해결 가이드 | Microsoft Docs
description: 알려진 문제의 목록 및 문제를 해결하는 데 도움이 되는 가이드
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: db910096f776f2f8710c0ac03f0378491018fc09
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 알려진 문제 및 문제 해결 가이드 
이 문서는 Azure Machine Learning Workbench 응용 프로그램 사용의 일부로 발생하는 오류 또는 실패를 찾고 수정하는 데 도움을 줍니다. 

## <a name="find-the-workbench-build-number"></a>Workbench 빌드 번호 찾기
지원 팀과 논의할 때는 Workbench 앱의 빌드 번호가 있어야 합니다. **도움말** 메뉴를 클릭하여 Windows에서 빌드 번호를 확인하고 **Azure ML Workbench 정보**를 선택할 수 있습니다. MacOS 등에서 **Azure ML Workbench** 메뉴를 클릭하고 **Azure ML Workbench**를 선택할 수 있습니다.

## <a name="machine-learning-msdn-forum"></a>Machine Learning MSDN 포럼
질문을 게시할 수 있는 MSDN 포럼이 있습니다. 제품 팀에서 포럼을 적극적으로 모니터링합니다. 포럼 URL은 [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum)입니다. 

## <a name="gather-diagnostics-information"></a>진단 정보 수집
도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 로그 파일 위치는 다음과 같습니다.

### <a name="installer-log"></a>설치 관리자 로그
설치 중 문제가 발생하는 경우 설치 관리자 로그 파일은 다음 위치에 추가됩니다.

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
이러한 디렉터리의 내용을 zip으로 압축하고 진단을 위해 Microsoft로 보낼 수 있습니다.

### <a name="workbench-desktop-app-log"></a>워크벤치 데스크톱 앱 로그
로그인하는 데 문제가 있거나 Workbench 데스크톱이 충돌하면 다음에서 로그 파일을 찾을 수 있습니다.
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
이러한 디렉터리의 내용을 zip으로 압축하고 진단을 위해 Microsoft로 보낼 수 있습니다.

### <a name="experiment-execution-log"></a>실험 실행 로그
데스크톱 앱을 제출하는 동안 특정 스크립트에 오류가 발생하면 `az ml experiment submit` 명령을 사용하여 CLI를 통해 다시 제출해보세요. 이렇게 하면 JSON 형식으로 전체 오류 메시지가 표시되며, **작업 ID** 값이 포함되어 있다는 장점이 있습니다. **작업 ID**를 포함하는 JSON 파일을 보내주시면 진단하는 데 도움을 드릴 수 있습니다. 

특정 스크립트를 성공적으로 제출했으나 실행하지 못할 경우 해당 특정 실행을 식별하기 위한 **실행 ID**가 출력됩니다. 다음 명령을 사용하여 관련 로그 파일을 패키지로 만들 수 있습니다.

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` 명령은 프로젝트 루트 폴더에 `diagnostics.zip` 파일을 생성합니다. 이 ZIP 패키지에는 패키지가 실행된 당시 상태의 전체 프로젝트 폴더와 로깅 정보가 포함됩니다. 진단 파일을 보내기 전에 포함하고 싶지 않은 중요한 정보를 제거해야 합니다.

## <a name="send-us-a-frown-or-a-smile"></a>찡그린(또는 웃는) 얼굴 보내기

Azure ML Workbench에서 작업할 경우, 응용 프로그램 셸의 왼쪽 하단 모서리에 있는 웃는 얼굴 아이콘을 클릭하여 찡그린(또는 웃는) 얼굴을 보낼 수도 있습니다. 필요에 따라 이메일 주소(사용자에게 연결 가능하도록) 및/또는 현재 상태의 스크린샷을 포함하도록 선택할 수 있습니다. 

## <a name="known-service-limits"></a>알려진 서비스 제한
- 최대 허용 프로젝트 폴더 크기: 25MB
    >[!NOTE]
    >이 제한은 `.git`, `docs` 및 `outputs` 폴더에는 적용되지 않습니다. 이러한 폴더 이름은 대/소문자를 구분합니다. 큰 파일을 사용하는 경우 [변경 내용 유지 및 큰 파일 처리](../desktop-workbench/how-to-read-write-files.md)를 참조하세요.

- 최대 허용 실험 실행 시간: 7일

- 실행 후 `outputs` 폴더에서 추적된 파일의 최대 크기: 512MB
  - 즉, 스크립트에서 출력 폴더에 512MB보다 큰 파일을 생성하는 경우 수집되지 않습니다. 큰 파일을 사용하는 경우 [변경 내용 유지 및 큰 파일 처리](../desktop-workbench/how-to-read-write-files.md)를 참조하세요.

- SSH를 통해 원격 컴퓨터 또는 Spark 클러스터에 연결할 때 SSH 키는 지원되지 않습니다. 현재는 사용자 이름/암호 모드만 지원됩니다.

- HDInsight 클러스터를 계산 대상으로 사용할 때 Azure Blob을 기본 저장소로 사용해야 합니다. Azure Data Lake Storage를 사용하도록 지원되지 않습니다.

- 텍스트 클러스터링 변환은 Mac에서 지원되지 않습니다.

- RevoScalePy 라이브러리는 (Docker 컨테이너의) Windows 또는 Linux에서만 지원됩니다. macOS에서는 지원되지 않습니다.

- Jupyter 노트북을 Workbench 앱에서 여는 경우 최대 크기가 5MB로 제한됩니다. 큰 노트북은 CLI에서 'az ml notebook start' 명령을 사용하여 열고 셀 출력을 정리하여 파일 크기를 줄일 수 있습니다.

## <a name="cant-update-workbench"></a>Workbench를 업데이트할 수 없음
새 업데이트를 사용할 수 있는 경우 Workbench 앱 홈페이지에 새 업데이트에 대한 정보를 제공하는 메시지가 표시됩니다. 앱의 왼쪽 아래 구석에 있는 종 모양 아이콘에 업데이트 배지가 표시됩니다. 이 배지를 클릭하고 설치 마법사에 따라 업데이트를 설치합니다. 

![이미지 업데이트](./media/known-issues-and-troubleshooting-guide/update.png)

이러한 알림이 표시되지 않으면 앱을 다시 시작할 수 있습니다. 다시 시작한 후에도 여전히 업데이트 알림이 표시되지 않으면 몇 가지 원인이 있을 수 있습니다.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>작업 표시줄에 고정된 바로 가기에서 Workbench를 시작함
업데이트를 이미 설치했을 수 있습니다. 하지만 고정된 바로 가기는 여전히 디스크의 이전 비트를 가리키고 있습니다. `%localappdata%/AmlWorkbench` 폴더로 이동하여 이를 확인하고 최신 버전이 설치되어 있는지 검토한 후, 고정된 바로 가기 속성이 가리키는 위치를 확인할 수 있습니다. 확인된 후에는 이전 바로 가기를 제거하고 시작 메뉴에서 Workbench를 시작한 후, 필요에 따라 작업 표시줄에 새 고정 바로 가기를 만들면 됩니다.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Windows DSVM의 "Azure ML Workbench 설치" 링크를 사용하여 Workbench를 설치했음
이 경우에는 쉽게 해결할 수 있는 방법이 없습니다. 다음 단계에 따라 설치된 비트를 제거하고, 최신 설치 관리자를 다운로드하여 Workbench를 새로 설치해야 합니다. 
   - 폴더 `C:\Users\<Username>\AppData\Local\amlworkbench` 제거
   - 스크립트 `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1` 제거
   - 위의 스크립트를 시작하는 바탕 화면 바로 가기 제거
   - https://aka.ms/azureml-wb-msi 설치 프로그램을 다운로드하고 다시 설치합니다.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>로그인한 후에 "실험 계정 검사" 화면에서 중단됨
로그인한 후에 Workbench 앱은 회전 휠을 포함한 "실험 계정 검사"를 보여주는 메시지를 표시한 빈 화면에서 중단되었을 수 있습니다. 이 문제를 해결하려면, 다음 단계를 사용하세요.
1. 앱 종료
2. 다음 파일을 삭제합니다.
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. 앱을 다시 시작합니다.

## <a name="cant-delete-experimentation-account"></a>실험 계정을 삭제할 수 없음
CLI를 사용하여 실험 계정을 삭제할 수 있지만 먼저 하위 작업 영역과 해당 하위 작업 영역 내의 하위 프로젝트를 먼저 삭제해야 합니다. 그렇지 않으면 "중첩 리소스를 삭제하기 전에 리소스를 삭제할 수 없습니다."라는 오류가 표시됩니다.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Workbench 앱 내에서 프로젝트 및 작업 영역을 삭제할 수도 있습니다.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>프로젝트가 OneDrive에 있는 경우 파일을 열 수 없음
Windows 10 Fall Creators Update가 있으며 프로젝트를 OneDrive에 매핑된 로컬 폴더에 만든 경우 Workbench에서 파일을 열 수 없습니다. 이 문제는 Fall Creators Update에 의해 도입된 버그 때문에 node.js 코드가 OneDrive 폴더에서 실패하기 때문입니다. 이 버그는 Windows 업데이트를 통해 곧 해결될 예정이지만 그때까지는 OneDrive 폴더에 프로젝트를 만들지 마세요.

## <a name="file-name-too-long-on-windows"></a>Windows에서 파일 이름이 너무 깁니다.
Windows에서 Workbench를 사용하는 경우 기본 최대 260자 파일 이름 길이 제한이 발생할 수 있습니다. 그러면 "시스템이 지정된 경로를 찾을 수 없습니다."라는 오류가 표시될 수 있습니다. 레지스트리 키 설정을 수정하여 긴 파일 경로 이름을 늘릴 수 있습니다. _MAX_PATH_ 레지스트리 키를 설정하는 방법에 대한 자세한 내용은 [이 문서](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath)를 검토하세요.

## <a name="interrupt-cli-execution-output"></a>CLI 실행 출력 중단
`az ml experiment submit` 또는 `az ml notebook start`를 사용하여 실험을 실행하기 시작한 상태에서 출력을 중단하려면: 
- Windows의 경우 키보드의 Ctrl-Break 키 조합을 사용합니다.
- macOS의 경우, Ctrl-C를 사용합니다.

이렇게 하면 CLI 창의 출력 스트림만 중단됩니다. 실행 중인 작업은 실제로 중지되지 않습니다. 진행 중인 작업을 취소하려면 `az ml experiment cancel -r <run_id> -t <target name>` 명령을 사용합니다.

키보드에 Break 키가 없는 Windows 컴퓨터의 경우 가능한 대안에는 Fn-B, Ctrl-Fn-B 또는 Fn+Esc가 있습니다. 구체적인 키 조합에 대해서는 하드웨어 공급업체의 설명서를 참조하세요.

## <a name="docker-error-read-connection-refused"></a>Docker 오류 “읽기: 연결이 거부되었습니다”
로컬 Docker 컨테이너에 대해 실행할 때 경우에 따라 다음과 같은 오류가 표시될 수 있습니다. 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Docker DNS 서버를 `automatic`에서 `8.8.8.8`의 고정된 값으로 변경하여 수정할 수 있습니다.

## <a name="remove-vm-execution-error-no-tty-present"></a>VM 실행 오류 “no tty present(tty 없음)” 제거
원격 Linux 컴퓨터에서 Docker 컨테이너에 대해 실행할 때 다음 오류 메시지가 발생할 수 있습니다.
```
sudo: no tty present and no askpass program specified.
``` 
Azure Portal을 사용하여 Ubuntu Linux VM의 루트 암호를 변경하는 경우 이 메시지가 발생할 수 있습니다. 

Azure Machine Learning Workbench에는 원격 호스트에서 실행하기 위해 암호 없는 sudoers 액세스가 필요합니다. 이 작업을 수행하는 가장 간단한 방법은 _visudo_를 사용하여 다음 파일을 편집하는 것입니다(파일이 없는 경우 만들 수 있음).

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>다른 명령이 아니라, _visudo_를 사용하여 파일을 편집하는 것이 중요합니다. _visudo_는 자동으로 모든 sudo 구성 파일의 구문을 검사하고 구문적으로 올바른 sudoers 파일을 생성하지 않으면 sudo에서 사용자를 잠글 수 있습니다.

파일 끝에 다음 줄을 삽입합니다.

```
username ALL=(ALL) NOPASSWD:ALL
```

여기서 _username_은 원격 호스트에 로그인하는 데 사용할 Azure Machine Learning Workbench 이름입니다.

이 줄은 #includedir "/etc/sudoers.d" 뒤에 있어야 하며 그렇지 않으면 다른 규칙에 의해 재정의될 수 있습니다.

sudo 구성이 보다 복잡한 경우 다음 위치에 제공되는 Ubuntu에 대한 sudo 설명서를 참조할 수 있습니다. https://help.ubuntu.com/community/Sudoers

Azure에서 Ubuntu 기반 Linux VM을 실행 대상으로 사용하지 않는 경우에도 위의 오류가 발생할 수 있습니다. Ubuntu 기반 Linux VM만 원격 실행을 지원합니다. 

## <a name="vm-disk-is-full"></a>VM 디스크가 꽉 참
기본적으로 Azure에서 새 Linux VM을 만들면 운영 체제용으로 30GB 디스크를 얻을 수 있습니다. 기본적으로 Docker 엔진은 동일한 디스크를 사용하여 이미지를 아래로 끌고 컨테이너를 실행합니다. 이로 인해 OS 디스크가 채워질 수 있으며 이 경우 “VM 디스크가 꽉 참” 오류가 표시됩니다.

빠른 수정 방법은 더 이상 사용하지 않는 모든 Docker 이미지를 제거하는 것입니다. 다음 Docker 명령으로 이를 수행합니다. (물론 bash 셸에서 Docker 명령을 실행하려면 VM에 SSH를 수행해야 합니다.)

```
$ docker system prune -a
```

데이터 디스크를 추가하고 이미지 저장에 데이터 디스크를 사용하도록 Docker 엔진을 구성할 수도 있습니다. [데이터 디스크를 추가하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)은 다음과 같습니다. 그런 다음 [Docker에서 이미지를 저장하는 위치를 변경](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)합니다.

또는 OS 디스크를 확장할 수 있으며 Docker 엔진 구성을 손대지 않아도 됩니다. [OS 디스크를 확장하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks)은 다음과 같습니다.

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Windows에서 C 드라이브 공유
Windows의 로컬 Docker 컨테이너에서 실행하는 경우 `aml_config` 아래 `docker.compute` 파일에서 `sharedVolumes`를 `true`로 설정하면 실행 성능을 향상시킬 수 있습니다. 그러나 이를 위해서는 _Docker for Windows Tool_에서 C 드라이브를 공유해야 합니다. C 드라이브를 공유할 수 없는 경우 다음 팁을 시도해 보세요.

* 파일 탐색기를 사용하여 C 드라이브에서 공유 확인
* 네트워크 어댑터 설정을 열고 vEthernet에 대해 "Microsoft 네트워크용 파일 및 프린터 공유" 제거/다시 설치
* Docker 설정을 열고 Docker 설정 내에서 C 드라이브 공유
* Windows 암호로 변경은 공유에 영향을 줍니다. 파일 탐색기를 열고, C 드라이브를 다시 공유하고, 새 암호를 입력합니다.
* Docker로 C 드라이브를 공유하려고 할 때 방화벽 문제가 발생할 수 있습니다. 이 [Stack Overflow 게시물](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)이 도움이 될 수 있습니다.
* 도메인 자격 증명을 사용하여 C 드라이브를 공유하는 경우 도메인 컨트롤러에 연결할 수 없는 네트워크에서 공유가 작동을 중지할 수 있습니다(예: 홈 네트워크, 공용 wifi 등). 자세한 내용은 [이 게시물](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)을 참조하세요.

또한 `docker.compute` 파일에서 `sharedVolumne`을 `false`로 설정하여 적은 성능 비용으로 공유 문제를 피할 수도 있습니다.

## <a name="wipe-clean-workbench-installation"></a>Workbench 새로 설치 제거
대개 이 작업은 수행할 필요가 없습니다. 하지만 설치를 완전히 초기화해야 하는 경우, 다음 단계를 따르십시오.

- Windows에서:
  - 먼저 _제어판_에서 _프로그램 추가/제거_ 애플릿을 사용하여 _Azure Machine Learning Workbench_ 응용 프로그램 항목을 제거해야 합니다.  
  - 그런 다음, 다음 스크립트 중 하나를 다운로드하여 실행합니다.
    - [Windows 명령줄 스크립트](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Windows PowerShell 스크립트](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (스크립트를 실행하기 전에 상승된 권한(관리자)으로 PowerShell 창에서 `Set-ExecutionPolicy Unrestricted`를 실행해야 할 수도 있습니다.)
- macOS에서:
  - [macOS bash 셸 스크립트](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh)를 다운로드하여 실행합니다.

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Python 환경이 설치된 Azure ML과 다른 Python 위치를 사용하는 Azure ML
Azure Machine Learning Workbench의 최근 변경으로 인해, 사용자는 로컬 실행이 Azure ML Workbench로 설치된 Python 환경을 더 이상 가리키지 않을 수 있음을 알 수 있습니다. 이것은 사용자가 자신의 컴퓨터에 다른 Python 환경을 설치하고 “Python” 경로가 해당 환경을 가리키도록 설정된 경우 발생할 수 있습니다. Python 환경이 설치된 Azure ML Workbench를 사용하려면 다음 단계를 수행합니다.
- 프로젝트 루트 아래의 aml_config 폴더에 있는 local.compute 파일로 이동합니다.
- Python 환경이 설치된 Azure ML Workbench의 실제 경로를 가리키도록 “pythonLocation” 변수를 변경합니다. 두 가지 방법으로 이 경로를 얻을 수 있습니다.
    - Azure ML Python 위치는 %localappdata%\AmlWorkbench\python\python.exe에서 확인할 수 있습니다.
    - Azure ML Workbench에서 cmd를 열고, 명령 프롬프트에 python을 입력하며 sys.exe를 가져오고, sys.executable을 실행하며, 거기에서 경로를 가져올 수 있습니다. 



## <a name="some-useful-docker-commands"></a>몇 가지 유용한 Docker 명령

몇 가지 유용한 Docker 명령은 다음과 같습니다.

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
