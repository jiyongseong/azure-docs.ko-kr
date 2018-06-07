---
title: Azure Cloud Shell에서 Bash를 사용하여 Ansible 실행
description: Azure Cloud Shell에서 Bash를 사용하여 다양한 Ansible 작업을 수행하는 방법 알아보기
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Azure Cloud Shell에서 Bash를 사용하여 Ansible 실행

이 자습서에서는 Cloud Shell의 Bash에서 다양한 Ansible 작업을 수행하는 방법을 알아봅니다. 이러한 작업에는 가상 머신에 연결하고 Ansible 플레이북을 생성하여 Azure 리소스 그룹을 만들고 삭제하는 작업이 포함됩니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- **Azure 자격 증명** - [Azure 자격 증명 만들기 및 Ansible 구성](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Azure Cloud Shell 구성** - Azure Cloud Shell을 처음 사용하는 경우 Cloud Shell을 시작하고 구성하는 방법이 [Azure Cloud Shell의 Bash 빠른 시작](https://docs.microsoft.com/azure/cloud-shell/quickstart) 문서에 설명되어 있습니다. 다음에서 Cloud Shell에 대한 전용 웹 사이트를 시작합니다.

[![Cloud Shell 시작](https://shell.azure.com/images/launchcloudshell.png "Cloud Shell 시작")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Ansible을 사용하여 VM에 연결
Ansible은 Azure Resource Manager에 API를 요청하여 Azure 리소스의 동적 인벤토리를 생성하는 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py)라는 Python 스크립트를 만들었습니다. 다음 단계에서는 `azure_rm.py` 스크립트를 사용하여 Azure 가상 머신에 연결하는 과정을 안내합니다.

1. Cloud Shell에서 Bash를 엽니다. 셸 형식은 Cloud Shell 창의 왼쪽에 표시됩니다.

1. 사용할 가상 머신이 없는 경우 다음 명령을 Cloud Shell에 입력하여 테스트에 사용할 가상 머신을 만듭니다.

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. GNU `wget` 명령을 사용하여 `azure_rm.py` 스크립트를 검색합니다.

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. `chmod` 명령을 사용하여 `azure_rm.py` 스크립트에 대한 액세스 권한을 변경합니다. 다음 명령은 `+x` 매개 변수를 사용하여 지정된 파일(`azure_rm.py`)의 실행을 허용합니다.

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. [ansible 명령](https://docs.ansible.com/ansible/2.4/ansible.html)을 사용하여 가상 머신에 연결합니다. 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  연결되면 출력과 유사한 결과가 표시됩니다.

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. 이 섹션에서 리소스 그룹 및 가상 머신을 만든 경우

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Cloud Shell에서 플레이 북 실행
[ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) 명령은 대상 호스트에서 작업을 실행하는 Ansible 플레이북을 실행합니다. 이 섹션에서는 Cloud Shell을 사용하여 두 개의 플레이북(하나는 리소스 그룹 생성용으로 다른 하나는 리소스 그룹 삭제용으로)을 만들고 실행하는 과정을 안내합니다. 

1. 다음과 같이 `rg.yml`이라는 파일을 만듭니다.

  ```azurecli-interactive
  vi rg.yml
  ```

1. 다음 콘텐츠를 Cloud Shell 창에 복사합니다. (이제 VI 편집기의 인스턴스를 호스트합니다.)

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. 파일을 저장하고 `:wq`를 입력하고 &lt;Enter>를 눌러서 VI 편집기를 종료합니다.

1. `ansible-playbook` 명령을 사용하여 `rg.yml` 플레이북을 실행합니다.

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 다음 출력과 유사한 결과가 표시됩니다.

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. 배포 확인:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. 리소스 그룹을 만들었으니 리소스 그룹을 삭제할 두 번째 Ansible 플레이북을 만듭니다.

  ```azurecli-interactive
  vi rg2.yml
  ```

1. 다음 콘텐츠를 Cloud Shell 창에 복사합니다. (이제 VI 편집기의 인스턴스를 호스트합니다.)

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. 파일을 저장하고 `:wq`를 입력하고 &lt;Enter>를 눌러서 VI 편집기를 종료합니다.

1. `ansible-playbook` 명령을 사용하여 `rg2.yml` 플레이북을 실행합니다.

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 다음 출력과 유사한 결과가 표시됩니다.

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Ansible을 사용하여 기본 가상 머신 만들기](/azure/virtual-machines/linux/ansible-create-vm)
