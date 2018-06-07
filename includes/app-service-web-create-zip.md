## <a name="create-a-project-zip-file"></a>프로젝트 ZIP 파일 만들기

프로젝트의 모든 것에 대한 ZIP 아카이브를 만듭니다. 다음 명령은 터미널의 기본 도구를 사용합니다.

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

나중에 이 ZIP 파일을 Azure에 업로드하고 이를 App Service에 배포합니다.