## <a name="deployment-customization"></a>배포 사용자 지정

배포 프로세스에서는 푸시하는 .zip 파일에 실행 준비 앱이 포함되어 있다고 가정합니다. 기본적으로 사용자 지정은 실행되지 않습니다. 지속적인 통합을 사용하여 가져오는 동일한 빌드 프로세스를 사용하도록 설정하려면 응용 프로그램 설정에 다음을 추가합니다.

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

.zip 푸시 배포를 사용하는 경우 이 설정은 기본적으로 **false**입니다. 지속적인 통합 배포에 대한 기본값은 **true**입니다. **true**로 설정하면 배포 중에 배포 관련 설정이 사용됩니다. 이러한 설정은 .zip 파일의 루트에 있는 앱 설정으로 구성하거나 .deployment 구성 파일에서 구성할 수 있습니다. 자세한 내용은 배포 참조의 [리포지토리 및 배포 관련 설정](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)을 참조하세요.