---
title: API 버전 프로필을 사용 하 여 Azure 스택의 python | Microsoft Docs
description: API 버전 프로필을 사용 하 여 Azure 스택의 python 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>API 버전 프로필을 사용 하 여 python Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="python-samples-for-azure-stack"></a>Azure 스택에 대 한 Python 샘플 

Azure 스택에서 가상 컴퓨터에 대 한 일반 관리 작업을 수행 하는 다음 코드 예제를 사용할 수 있습니다.

코드 예제를 보여 줍니다.

- 가상 컴퓨터를 만듭니다.
    - Linux 가상 머신 만들기
    - Windows 가상 머신 만들기
- 가상 컴퓨터를 업데이트 합니다.
    - 드라이브를 확장 합니다.
    - 가상 머신 태그 지정
    - 데이터 디스크 연결
    - 데이터 디스크 분리
- 가상 컴퓨터를 작동 합니다.
    - 가상 머신 시작
    - 가상 머신 중지
    - 가상 머신 다시 시작
- 가상 머신 나열
- 가상 머신 삭제

이러한 작업을 수행 하는 코드를 검토 하려면 체크 아웃 된 **run_example()** Python 스크립트 함수에서에서 **Hybrid/unmanaged-disks/example.py** GitHub 리포지토리에 있는 [ 가상 컴퓨터-python-관리](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88)합니다.

각 작업은 주석 및 인쇄 기능 명확 하 게 표시 되어 있습니다.
예는 반드시 위 목록에 나와 있는 순서 대로 되지 않습니다.


## <a name="run-the-python-sample"></a>Python 샘플 실행

1.  모를 경우 [Python 설치](https://www.python.org/downloads/)합니다.

    이 샘플 (및 SDK) 3.4, 3.5 및 3.6 Python 2.7 호환 됩니다.

2.  Python 개발을 위한 일반적인 권장 사항은 가상 환경을 사용 하는 것입니다. 
    자세한 내용은 참조 하세요. https://docs.python.org/3/tutorial/venv.html
    
    설치 하 고 가상 환경을 Python 3에서 "venv" 모듈과 함께 초기화 (설치 해야 [virtualenv](https://pypi.python.org/pypi/virtualenv) Python 2.7에 대 한):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  리포지토리를 복제 합니다.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Pip를 사용 하 여 종속성을 설치 합니다.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  만들기는 [서비스 사용자](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) Azure 스택과 함께 작동 하도록 합니다. 서비스 사용자에 게 있는지 확인 [참가자/소유자 역할](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) 구독에 있습니다.

6.  다음 변수를 설정 하 고 현재 프로그램 shell에 이러한 환경 변수를 내보냅니다. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  이 샘플을 실행 하려면 Ubuntu 16.04 LTS 및 windows Server 2012 R2-Datacenter 이미지 있어야 함을 Azure 스택 시장 위치에 참고 합니다. 일 수 있습니다 이러한 [Azure에서 다운로드](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) 또는 [플랫폼 이미지 리포지토리에 추가](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image)합니다.


8. 샘플을 실행합니다.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>메모

사용 하 여 VM의 OS 디스크를 검색 하려고 하 고 싶을 수도 있습니다 `virtual_machine.storage_profile.os_disk`합니다.
경우에 따라이 될 수 있습니다 원하는 방법, 제공 있는지 알아야 하지만 `OSDisk` 개체입니다.
으로 OS 디스크의 크기를 업데이트 하기 위해 `example.py` 않습니다, 해야 하지는 `OSDisk` 개체 하지만 `Disk` 개체입니다.
`example.py` 가져옵니다는 `Disk` 다음 개체:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>다음 단계

- [Azure Python 개발 센터](https://azure.microsoft.com/develop/python/)
- [Azure 가상 컴퓨터 설명서](https://azure.microsoft.com/services/virtual-machines/)
- [가상 컴퓨터에 대 한 경로 파악 중](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Microsoft Azure 구독이 없는 경우 무료 평가판 계정을 얻을 수 있습니다 [여기](http://go.microsoft.com/fwlink/?LinkId=330212)합니다.
