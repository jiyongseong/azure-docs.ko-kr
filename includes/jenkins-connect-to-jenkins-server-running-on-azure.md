1. 브라우저에서 Jenkins 가상 머신으로 이동합니다. Jenkins 대시보드는 안전하지 않은 HTTP를 통해 액세스할 수 없으므로 SSH를 사용하여 가상 머신에 연결해야 한다는 메시지가 표시됩니다.

    ![Jenkins에서는 SSH를 사용하여 Jenkins 가상 머신에 연결하는 방법을 설명하는 정보를 제공합니다.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. SSH에 액세스할 수 있는 bash 또는 터미널 창을 엽니다.

1. 다음 `ssh` 명령을 입력합니다. 여기서 **&lt;사용자 이름>** 및 **&lt;도메인>** 자리 표시자는 Jenkins 가상 머신을 만들 때 지정한 값으로 바꿉니다.

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. `ssh` 프롬프트에 따라 Jenkins 가상 머신에 로그인합니다.

1. 다음 명령을 입력하여 Jenkins 잠금을 해제할 초기 암호를 검색합니다.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. bash 또는 터미널 창에 표시된 암호를 클립보드에 복사합니다.

1. 브라우저에서 `http://localhost:8080`로 이동합니다.

1. 이전에 검색한 암호를 **관리 암호** 필드에 붙여넣고 **계속**을 선택합니다.

    ![Jenkins는 Jenkins 가상 머신에 처음 연결할 때 가상 머신의 잠금을 해제하는 데 사용해야 하는 초기 암호를 저장합니다.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. **Install suggested plugins**(제안된 플러그 인 설치)를 선택합니다.

    ![Jenkins에서는 초기 입력 시 제한된 플러그 인 모음을 쉽게 설치할 수 있습니다.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. **Create First Admin User**(첫 번째 관리 사용자 만들기) 페이지에서 Jenkins 대시보드의 관리 사용자 및 암호를 만들고 **저장하고 끝내기**를 선택합니다.

1. **Jenkins is ready!**(Jenkins가 준비되었습니다.) 페이지에서 **Start using Jenkins**(Jenkins를 사용하여 시작)를 선택합니다.

    ![Jenkins가 설치되고 액세스가 구성되면 Jenkins를 사용하여 작업을 만들고 빌드하기 시작할 수 있습니다.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)