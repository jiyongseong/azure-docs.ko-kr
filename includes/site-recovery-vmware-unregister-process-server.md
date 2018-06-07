프로세스 서버를 등록 취소하는 단계는 구성 서버와의 연결 상태에 따라 달라집니다.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>연결된 상태인 프로세스 서버 등록 취소

1. 관리자 권한으로 프로세스 서버에 원격 로그인합니다.
2. **제어판**을 실행하고 **프로그램 > 프로그램 제거**를 엽니다.
3. Uninstall a program by the name **Microsoft Azure Site Recovery 구성/프로세스 서버** 이름으로 프로그램을 제거합니다.
4. 3단계가 완료되면 **Microsoft Azure Site Recovery 구성/프로세스 서버**를 제거할 수 있습니다.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>연결 해제된 상태인 프로세스 서버 등록 취소

> [!WARNING]
> 아래 단계는 프로세스 서버가 설치된 가상 머신을 복구할 방법이 없는 경우 사용해야 합니다.

1. 관리자 권한으로 구성 서버에 로그온합니다.
2. 관리 명령 프롬프트를 열고 `%ProgramData%\ASR\home\svsystems\bin` 디렉터리를 찾습니다.
3. 이제 명령을 실행합니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 그러면 시스템에서 프로세스 서버의 세부 정보를 제거합니다.
