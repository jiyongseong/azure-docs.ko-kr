
[az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 앱을 만듭니다. 

웹앱은 API에 대한 호스팅 공간을 제공하고, 배포된 앱을 확인하도록 URL을 제공합니다.

다음 명령에서 *\<app_name>*을 고유한 이름으로 바꿉니다. `<app_name>`이 고유하지 않으면 "지정된 이름이 <app_name>인 웹 사이트가 이미 있습니다."라는 오류 메시지가 표시됩니다. 웹앱의 기본 URL은 `https://<app_name>.azurewebsites.net`입니다. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```