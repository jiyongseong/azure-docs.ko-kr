## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>가상 컴퓨터 만들기

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 *myVM*이라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. 또한 이 명령은 *azureuser*를 관리자 사용자 이름으로 설정합니다. 나중에 이 이름을 사용하여 VM에 연결합니다. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`을 기록해 둡니다. 이 주소는 나중의 단계에서 VM에 액세스하는 데 사용됩니다.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기 

기본적으로 Azure에 배포된 Linux VM에는 SSH 연결만이 가능합니다. 이 VM이 웹 서버가 되기 때문에 인터넷에서 포트 80을 열어야 합니다. [az vm open-port](/cli/azure/vm#az_vm_open_port) 명령을 사용하여 원하는 포트를 엽니다.  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>VM에 SSH 수행


VM의 공용 IP 주소를 알고 있는 경우 [az network public-ip list](/cli/azure/network/public-ip#list) 명령을 실행합니다. 나중에 몇 단계에서 이 IP 주소가 필요합니다.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

다음 명령을 사용하여 가상 컴퓨터와의 SSH 세션을 만듭니다. 가상 컴퓨터의 올바른 공용 IP 주소로 대체합니다. 이 예제에서 IP 주소는 *40.68.254.142*입니다. *azureuser*는 VM을 만들 때 설정한 관리자 사용자 이름입니다.

```bash
ssh azureuser@40.68.254.142
```

