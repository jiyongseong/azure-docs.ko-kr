## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 함수 앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 `myResourceGroup`이라는 리소스 그룹을 만듭니다.  
Cloud Shell을 사용하지 않는 경우 먼저 `az login`을 사용하여 로그인합니다.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. App Service 계획에 대해 지원되는 모든 위치를 확인하려면 [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations) 명령을 실행합니다.