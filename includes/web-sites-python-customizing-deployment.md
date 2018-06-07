Azure는 **다음 조건 모두가 참일 경우**응용 프로그램에서 Python을 사용하도록 결정합니다.

* 루트 폴더에 있는 requirements.txt 파일
* 루트 폴더에 있는 .py 파일이나 python을 지정하는 runtime.txt

이런 경우, Python 특정 배포 스크립트가 사용되어 파일 표준 동기화뿐만 아니라 다음과 같은 추가 Python 작업도 수행합니다.

* 가상 환경 자동 관리
* pip를 사용하여 requirements.txt에 나열된 패키지 설치
* 선택한 Python 버전에 따라 적절한 web.config 만들기
* Django 응용 프로그램에 대한 정적 파일 수집

스크립트를 사용자 지정하지 않고도 기본 배포 단계의 특정 측면을 제어할 수 있습니다.

모든 Python 특정 배포 단계를 건너뛰려는 경우, 다음과 같은 빈 파일을 만들면 됩니다.

    \.skipPythonDeployment

배포를 더 효율적으로 제어하려면 다음 파일을 만들어 기본 배포 스크립트를 재정하면 됩니다.

    \.deployment
    \deploy.cmd

[Azure 명령줄 인터페이스][Azure command-line interface]를 사용하여 파일을 만들 수 있습니다.  프로젝트 폴더에서 다음 명령을 사용합니다.

    azure site deploymentscript --python

이러한 파일이 존재하지 않으면 Azure는 임시 배포 스크립트를 만들고 실행합니다.  위의 명령을 사용하여 만드는 것과 같습니다.

[Azure command-line interface]: http://azure.microsoft.com/downloads/
