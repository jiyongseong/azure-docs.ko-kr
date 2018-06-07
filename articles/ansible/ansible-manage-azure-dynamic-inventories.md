---
title: Ansible을 사용하여 Azure 동적 인벤토리 관리
description: Ansible을 사용하여 Azure 동적 인벤토리를 관리하는 방법을 알아봅니다.
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, 동적 인벤토리
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: f29f4ec64b79738cae2ad684610f4817739825a9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Ansible을 사용하여 Azure 동적 인벤토리 관리
Ansible은 다양한 원본(Azure와 같은 클라우드 원본 포함)에서 *동적 인벤토리*로 인벤토리 정보를 가져오는 데 사용할 수 있습니다. 이 문서에서는 [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md)을 사용하여 두 개의 가상 머신을 만들고, 해당 가상 머신 각각에 태그를 지정하고, 태그가 지정된 가상 머신에 Nginx를 설치하는 Ansible Azure 동적 인벤토리를 구성합니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- **Azure 자격 증명** - [Azure 자격 증명 만들기 및 Ansible 구성](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>테스트 가상 머신 만들기

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 엽니다.

1. 이 자습서의 가상 머신을 보관할 Azure 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. 다음 방법 중 하나를 사용하여 Azure에 두 개의 Linux 가상 머신을 만듭니다.

    - **Ansible 플레이북** - [Azure에서 Ansible을 사용하여 기본 가상 머신 만들기](/azure/virtual-machines/linux/ansible-create-vm) 문서에서 Ansible 플레이북으로부터 가상 머신을 만드는 방법을 보여 줍니다. 플레이북을 사용하여 두 개의 가상 머신 중 하나 또는 둘 다를 정의하는 경우 암호 대신 SSH 연결이 사용되는지 확인합니다.

    - **Azure CLI** - Cloud Shell에서 다음 명령을 각각 실행하여 두 가상 머신을 만듭니다.

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>가상 머신 태그 지정
사용자 정의 범주별로 [태그를 사용하여 Azure 리소스를 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli)할 수 있습니다. 

다음 [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) 명령을 입력하여 `ansible-inventory-test-vm1` 가상 머신에 `nginx` 키를 사용한 태그를 지정합니다.

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>동적 인벤토리 생성
가상 머신이 정의되고 태그가 지정되면 동적 인벤토리를 생성해야 합니다. Ansible은 Azure Resource Manager에 API를 요청하여 Azure 리소스의 동적 인벤토리를 생성하는 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py)라는 Python 스크립트를 제공합니다. 다음 단계에서는 `azure_rm.py` 스크립트를 사용하여 두 개의 테스트 Azure Virtual Machines에 연결하는 과정을 안내합니다.

1. GNU `wget` 명령을 사용하여 `azure_rm.py` 스크립트를 검색합니다.

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. `chmod` 명령을 사용하여 `azure_rm.py` 스크립트에 대한 액세스 권한을 변경합니다. 다음 명령은 `+x` 매개 변수를 사용하여 지정된 파일(`azure_rm.py`)의 실행을 허용합니다.

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. [ansible 명령](https://docs.ansible.com/ansible/2.4/ansible.html)을 사용하여 리소스 그룹에 연결합니다. 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. 연결되면 다음과 비슷한 결과가 표시됩니다.

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>가상 머신 태그 사용
원하는 태그가 설정되면 태그를 "사용하도록 설정"해야 합니다. 태그를 사용하도록 설정하는 한 가지 방법은 **export** 명령을 통해 `AZURE_TAGS`라는 환경 변수에 태그를 내보내는 것입니다.

```azurecli-interactive
export AZURE_TAGS=nginx
```

태그를 내보낸 후에는 `ansible` 명령을 다시 시도할 수 있습니다.

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

이제 하나의 가상 머신(태그가 **AZURE_TAGS** 환경 변수로 내보낸 값과 일치하는 가상 머신)만 표시됩니다.

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>태그가 지정된 VM에 Nginx 설정
태그의 목적은 가상 머신의 하위 그룹을 빠르고 쉽게 사용할 수 있도록 하는 것입니다. 예를 들어 `nginx` 태그를 할당한 가상 머신에만 Nginx를 설치하려고 한다고 가정해 보겠습니다. 다음 단계에서는 수행하는 방법이 쉽다는 것을 보여 줍니다.

1. 다음과 같이 `nginx.yml`이라는 이름의 파일(플레이북 포함)을 만듭니다.

  ```azurecli-interactive
  vi nginx.yml
  ```

1. 새로 만든 `nginx.yml` 파일에 다음 코드를 삽입합니다.

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. `nginx.yml` 플레이북을 실행합니다.

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. 플레이북이 실행되면 다음 출력과 비슷한 결과가 표시됩니다.

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Nginx 설치 테스트
이 섹션에서는 가상 머신에 Nginx가 설치되었는지 테스트하는 한 가지 방법을 설명합니다.

1. [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) 명령을 사용하여 `ansible-inventory-test-vm1` 가상 머신의 IP 주소를 검색합니다. 그런 다음, 반환된 값(가상 머신의 IP 주소)은 가상 머신에 연결하기 위한 SSH 명령에 대한 매개 변수로 사용됩니다.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. [nginx -v](https://nginx.org/en/docs/switches.html) 명령은 일반적으로 Nginx 버전을 출력하는 데 사용됩니다. 그러나 Nginx가 설치되었는지 여부를 확인하는데도 사용할 수 있습니다. `ansible-inventory-test-vm1` 가상 머신에 연결된 상태에서 이를 입력합니다.

    ```azurecli-interactive
    nginx -v
    ```

1. `nginx -v` 명령이 실행되면 Nginx가 설치되었음을 나타내는 Nginx 버전(두 번째 줄)이 표시됩니다.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. SSH 세션의 연결을 끊으려면 **&lt;Ctrl>D** 키보드 조합을 누릅니다.

1. `ansible-inventory-test-vm2` 가상 머신에 대해 앞의 단계를 수행하면 Nginx를 얻을 수 있는 위치를 나타내는 정보 메시지가 표시됩니다(현재 설치되어 있지 않음을 나타냄).

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Azure에서 Ansible을 사용하여 기본 가상 머신 만들기](/azure/virtual-machines/linux/ansible-create-vm)
