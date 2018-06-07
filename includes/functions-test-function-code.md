## <a name="test"></a>함수 테스트

cURL을 사용하여 Mac 또는 Linux 컴퓨터나 Windows의 Bash를 사용하여 배포된 함수를 테스트합니다. 다음 cURL 명령을 실행하고 `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꿉니다. 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![브라우저에 표시된 함수 응답.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

명령줄에 사용할 수 있는 cURL이 없는 경우 웹 브라우저의 주소에 동일한 URL을 입력하면 됩니다. 다시, `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꾸고 쿼리 문자열 `&name=<yourname>`을 URL에 추가하고 요청을 실행합니다. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![브라우저에 표시된 함수 응답.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
