1. 보호하려는 서버의 로컬 폴더(예: C:\Temp)에 설치 관리자를 복사합니다. 명령 프롬프트에서 관리자 권한으로 다음 명령을 실행합니다.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. 이제 에이전트를 구성 서버에 등록해야 합니다.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>모바일 서비스 설치 관리자 명령줄 인수

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| 매개 변수|type|설명|가능한 값|
|-|-|-|-|
|/Role|필수|MS(Mobility Service) 설치 여부 또는 MT(MasterTarget) 설치 여부를 지정합니다.|MS </br> MT|
|/InstallLocation|옵션|Mobility Service가 설치되는 위치입니다.|컴퓨터의 모든 폴더|
|/Platform|필수|Mobility Service가 설치되는 플랫폼을 지정합니다. </br> </br>- **VMware**: *VMware vSphere ESXi 호스트*, *Hyper-V 호스트* 및 *물리적 서버*에서 실행되는 VM에 모바일 서비스를 설치하는 경우 이 값을 사용합니다. </br> - **Azure**: Azure IaaS VM에 에이전트를 설치하는 경우 이 값을 사용합니다. | VMware </br> Azure|
|/Silent|옵션|설치 관리자를 자동 모드에서 실행하도록 지정합니다.| 해당 없음|

>[!TIP]
> 설치 로그는 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log에 있습니다.

#### <a name="mobility-service-registration-command-line-arguments"></a>모바일 서비스 등록 명령줄 인수

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | 매개 변수|type|설명|가능한 값|
  |-|-|-|-|
  |/CSEndPoint |필수|구성 서버의 IP 주소| 모든 유효한 IP 주소|
  |/PassphraseFilePath|필수|암호의 위치 |모든 유효한 UNC 또는 로컬 파일 경로|


>[!TIP]
> 에이전트 구성 로그는 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log에 있습니다.
