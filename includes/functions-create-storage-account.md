## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

함수는 Azure Storage의 범용 계정을 사용하여 함수에 대한 상태 및 기타 정보를 유지 관리합니다. [az storage account create](/cli/azure/storage/account#create) 명령을 사용하여 만든 리소스 그룹에 범용 저장소 계정을 만듭니다.

다음 명령에서 `<storage_name>` 자리 표시자를 전역적으로 고유한 저장소 계정 이름으로 바꿉니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

저장소 계정을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```