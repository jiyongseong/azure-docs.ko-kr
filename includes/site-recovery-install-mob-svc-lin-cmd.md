1. 보호하려는 서버의 로컬 폴더(예: /tmp)에 설치 관리자를 복사합니다. 터미널에서 다음 명령을 실행합니다.
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. 설치가 완료되면 Mobility Service를 구성 서버에 등록해야 합니다. 다음 명령을 실행하여 Mobility Service를 구성 서버에 등록합니다.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>모바일 서비스 설치 관리자 명령줄

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|매개 변수|type|설명|가능한 값|
|-|-|-|-|
|-r |필수|MS(Mobility Service) 설치 여부 또는 MT(MasterTarget) 설치 여부를 지정합니다.|MS </br> MT|
|일시 중지되고 |옵션|Mobility Service가 설치되는 위치입니다.|/usr/local/ASR|
|-v|필수|Mobility Service가 설치되는 플랫폼을 지정합니다. </br> </br>- **VMware**: *VMware vSphere ESXi 호스트*, *Hyper-V 호스트* 및 *물리적 서버*에서 실행되는 VM에 모바일 서비스를 설치하는 경우 이 값을 사용합니다. </br> - **Azure**: Azure IaaS VM에 에이전트를 설치하는 경우 이 값을 사용합니다.| VMware </br> Azure|
|-q|옵션|설치 관리자를 자동 모드에서 실행하도록 지정합니다.| 해당 없음|


#### <a name="mobility-service-configuration-command-line"></a>모바일 서비스 구성 명령줄

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|매개 변수|type|설명|가능한 값|
|-|-|-|-|
|-i |필수|구성 서버의 IP|모든 유효한 IP 주소|
|-P |필수|연결 암호가 저장되는 파일의 전체 파일 경로입니다.|모든 유효한 폴더|
