---
title: Azure의 데이터 과학 Virtual Machine에 대한 언어 | Microsoft Docs
description: Azure의 데이터 과학 Virtual Machine에 대한 언어
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 411729155f5135c7e45588b69995274c9cac1315
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine에서 지원되는 언어 

DSVM(데이터 과학 Virtual Machine)는 AI 응용 프로그램 빌드를 위한 몇 가지 미리 작성된 언어 및 개발 도구와 함께 제공됩니다. 다음은 일부 중요 항목입니다. 

## <a name="python-windows-server-2016-edition"></a>Python(Windows Server 2016 버전)

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 2.7 및 3.6 |
| 지원되는 DSVM 버전      | Windows Server 2016     |
| DSVM에 구성/설치 방법  | 두 가지 전역 `conda` 환경이 만들어집니다. <br /> * `/anaconda/`에 있는 `root` 환경은 Python 3.6입니다. <br/> * `/anaconda/envs/python2`에 있는 `python2` 환경은 Python 2.7입니다.       |
| 샘플에 대한 링크      | Python에 대한 샘플 Jupyter 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, Julia      |

> [!NOTE]
> 2018년 3월 이전에 만들어진 Windows Server 2016에는 Python 3.5 및 Python 2.7이 포함되어 있습니다. 또한 Python 2.7은 conda **root** 환경이고, **py35**는 Python 3.5 환경입니다. 

### <a name="how-to-use--run-it"></a>사용/실행 방법    

* 명령 프롬프트에서 실행

명령 프롬프트를 열고 실행하려는 Python 버전에 따라 다음을 수행합니다. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* IDE에서 사용

Visual Studio Community Edition에 설치된 PTVS(Visual Studio용 Python Tools)를 사용합니다. 기본적으로 PTVS에서 자동으로 설정되는 유일한 환경은 Python 3.6입니다. 

> [!NOTE]
> Python 2.7의 PTVS를 가리키려면 PTVS에서 사용자 지정 환경을 만들어야 합니다. Visual Studio Community Edition에서 이러한 환경 경로를 설정하려면 **도구** -> **Python 도구** -> **Python 환경**으로 이동한 다음 **+ 사용자 지정**을 클릭합니다. 그런 후 해당 위치를 `c:\anaconda\envs\python2`로 설정하고 _자동 검색_을 클릭합니다. 

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 커널 유형으로 Python 3.6의 경우는 _Python [Conda Root]_를, Python 2.7 환경의 경우는 _Python [Conda env:python2]_를 선택할 수 있습니다. 

* Python 패키지 설치

DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 관리자 권한으로 `activate` 명령을 사용하여 루트 또는 python2 환경을 활성화합니다. 그런 후 `conda` 또는 `pip`와 같은 패키지 관리자를 사용하여 패키지를 설치하거나 업데이트할 수 있습니다. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python(Linux 및 Windows Server 2012 버전)

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 2.7 및 3.5 |
| 지원되는 DSVM 버전      | Linux, Windows Server 2012    |
| DSVM에 구성/설치 방법  | 두 가지 전역 `conda` 환경이 만들어집니다. <br /> * `/anaconda/`에 있는 `root` 환경은 Python 2.7입니다. <br/> * `/anaconda/envs/py35`에 있는 `py35` 환경은 Python 3.5입니다.       |
| 샘플에 대한 링크      | Python에 대한 샘플 Jupyter 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>사용/실행 방법    

**Linux**
* 터미널에서 실행

터미널을 열고 실행하려는 Python 버전에 따라 다음을 수행합니다. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* IDE에서 사용

Visual Studio Community Edition에 설치된 PyCharm을 사용합니다. 

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 커널 유형으로 Python 2.7의 경우는 _Python [Conda Root]_를, Python 3.5 환경의 경우는 _Python [Conda env:py35]_를 선택할 수 있습니다. 

* Python 패키지 설치

DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 관리자 권한으로 또는 sudo 권한이 있는 사용자로 `source activate` 명령을 사용하여 루트 또는 py35 환경을 활성화합니다. 그런 후 `conda` 또는 `pip`와 같은 패키지 관리자를 사용하여 패키지를 설치하거나 업데이트할 수 있습니다. 

**Windows 2012**
* 명령 프롬프트에서 실행

명령 프롬프트를 열고 실행하려는 Python 버전에 따라 다음을 수행합니다. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* IDE에서 사용

Visual Studio Community Edition에 설치된 PTVS(Visual Studio용 Python Tools)를 사용합니다. 유일한 환경은 Python 2.7의 PTVS에서 자동으로 설치됩니다. 
> [!NOTE]
> Python 3.5의 PTVS를 가리키려면 PTVS에서 사용자 지정 환경을 만들어야 합니다. Visual Studio Community Edition에서 이러한 환경 경로를 설정하려면 **도구** -> **Python 도구** -> **Python 환경**으로 이동한 다음 **+ 사용자 지정**을 클릭합니다. 그런 후 해당 위치를 `c:\anaconda\envs\py35`로 설정하고 _자동 검색_을 클릭합니다. 

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 커널 유형으로 Python 2.7의 경우는 _Python [Conda Root]_를, Python 3.5 환경의 경우는 _Python [Conda env:py35]_를 선택할 수 있습니다. 

* Python 패키지 설치

DSVM의 기본 Python 환경은 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 관리자 권한으로 `activate` 명령을 사용하여 루트 또는 py35 환경을 활성화합니다. 그런 후 `conda` 또는 `pip`와 같은 패키지 관리자를 사용하여 패키지를 설치하거나 업데이트할 수 있습니다. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | Microsoft R Open 3.x(CRAN-R과 100% 호환 가능)<br /> Microsoft R Server 9.x Developer Edition(R 플랫폼용으로 준비된 확장 가능한 Enterprise 버전)|
| 지원되는 DSVM 버전      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| 샘플 링크      | R에 대한 샘플 Jupyter 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>사용/실행 방법    

**Windows**:

* 명령 프롬프트에서 실행

명령 프롬프트를 열고 `R`을 입력하면 됩니다.

* IDE에서 사용

Visual Studio Community Edition 또는 RStudio에 설치된 RTVS(Visual Studio용 RTools)를 사용합니다. 시작 메뉴 또는 바탕 화면 아이콘으로 사용할 수 있습니다. 

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 Jupyter R 커널(IRKernel)을 사용하려면 커널 유형으로 _R_을 선택할 수 있습니다. 

* R 패키지 설치

R은 모든 사용자가 읽을 수 있는 전역 환경의 DSVM에 설치됩니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 R을 실행합니다. 그런 후 R 패키지 관리자 `install.packages()`를 실행하여 패키지를 설치하거나 업데이트할 수 있습니다. 

**Linux**:

* 터미널에서 실행

터미널을 열고 `R`을 실행하기만 하면 됩니다.  

* IDE에서 사용

Linux DSVM에 설치된 RStudio를 사용합니다.  

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 Jupyter R 커널(IRKernel)을 사용하려면 커널 유형으로 _R_을 선택할 수 있습니다. 

* R 패키지 설치

R은 모든 사용자가 읽을 수 있는 전역 환경의 DSVM에 설치됩니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 R을 실행합니다. 그런 후 R 패키지 관리자 `install.packages()`를 실행하여 패키지를 설치하거나 업데이트할 수 있습니다. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| 지원되는 언어 버전 | 0.6 |
| 지원되는 DSVM 버전      | Linux, Windows     |
| DSVM에 구성/설치 방법  | Windows: `C:\JuliaPro-VERSION`에 설치됩니다.<br /> Linux: `/opt/JuliaPro-VERSION`에 설치됩니다.    |
| 샘플에 대한 링크      | Julia에 대한 샘플 Jupyter 노트북이 포함되어 있습니다.     |
| DSVM의 관련 도구      | Python, R      |
### <a name="how-to-use--run-it"></a>사용/실행 방법    

**Windows**:

* 명령 프롬프트에서 실행

명령 프롬프트를 열고 `julia`를 실행하면 됩니다. 
* IDE에서 사용

DSVM에 설치되고 바탕 화면 바로 가기로 사용할 수 있는 Julia IDE `Juno`를 사용합니다.

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 커널 유형으로 `Julia VERSION`을 선택할 수 있습니다. 

* Julia 패키지 설치

기본 Julia 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 Julia를 실행합니다. 그런 후 `Pkg.add()`와 같은 Julia 패키지 관리자 명령을 실행하여 패키지를 설치하거나 업데이트할 수 있습니다. 


**Linux**:
* 터미널에서 실행

터미널을 열고 `julia`를 실행하기만 하면 됩니다. 
* IDE에서 사용

DSVM에 설치되고 응용 프로그램 메뉴 바로 가기로 사용할 수 있는 Julia IDE `Juno`를 사용합니다.

* Jupyter에서 사용

Jupyter를 열고 `New` 단추를 클릭하여 새 노트북을 만듭니다. 이때 커널 유형으로 `Julia VERSION`을 선택할 수 있습니다. 

* Julia 패키지 설치

기본 Julia 위치는 모든 사용자가 읽을 수 있는 전역 환경입니다. 하지만 관리자만 전역 패키지를 쓰고 설치할 수 있습니다. 전역 환경에 패키지를 설치하려면 위의 방법 중 하나를 사용하여 Julia를 실행합니다. 그런 후 `Pkg.add()`와 같은 Julia 패키지 관리자 명령을 실행하여 패키지를 설치하거나 업데이트할 수 있습니다. 

## <a name="other-languages"></a>기타 언어

**C#**: Windows에서 사용할 수 있고 Visual Studio Community Edition을 통해 또는 `csc` 명령만 실행할 수 있는 `Developer Command Prompt for Visual Studio`에서 액세스할 수 있습니다. 

**Java**: OpenJDK는 DSVM의 Linux 및 Windows 버전 둘 다에서 사용할 수 있고 경로에 설정됩니다. Windows의 명령 프롬프트 또는 Linux의 bash 셸에 `javac` 또는 `java` 명령을 입력하여 Java를 사용할 수 있습니다. 

**node.js**: node.js는 DSVM의 Linux 및 Windows 버전 둘 다에서 사용할 수 있고 경로에 설정됩니다. Windows의 명령 프롬프트 또는 Linux의 bash 셸에 `node` 또는 `npm` 명령을 입력하여 node.js에 액세스할 수 있습니다. Windows에서는 node.js 응용 프로그램을 개발하기 위한 그래픽 IDE를 제공하기 위해 Visual Studio용 Node.js 도구 확장이 설치됩니다. 

**F#**: Windows에서 사용할 수 있고 Visual Studio Community Edition을 통해 또는 `fsc` 명령만 실행할 수 있는 `Developer Command Prompt for Visual Studio`에서 액세스할 수 있습니다. 


