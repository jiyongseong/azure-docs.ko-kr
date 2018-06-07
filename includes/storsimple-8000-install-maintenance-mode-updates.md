### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 유지 관리 모드 업데이트 설치

StorSimple 장치에 유지 관리 모드 업데이트를 적용하면 모든 I/O 요청이 일시 중지됩니다. NVRAM(비휘발성 임의 액세스 메모리) 등의 서비스 또는 클러스터링 서비스는 중지됩니다. 이 모드를 종료하거나 입력하면 두 컨트롤러 모두 다시 부팅됩니다. 이 모드를 종료하면 모든 서비스가 다시 시작되고 정상 상태입니다. (몇 분이 걸릴 수 있습니다.)

> [!IMPORTANT]
> * 유지 관리 모드를 시작하기 전에 Azure Portal에서 두 장치 컨트롤러가 정상인지 확인합니다. 컨트롤러가 정상 상태가 아니면 [Microsoft 지원 서비스](../articles/storsimple/storsimple-8000-contact-microsoft-support.md)에 다음 단계를 문의하세요.
> * 유지 관리 모드에 있는 경우, 먼저 하나의 컨트롤러에 업데이트한 다음 다른 컨트롤러에 업데이트해야 합니다.

1. 직렬 콘솔 연결에 PuTTY 사용 [PuTTy를 사용하여 직렬 콘솔에 연결](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)에서 자세한 지침을 따르세요. 명령 프롬프트에서 **Enter**키를 누릅니다. 옵션 1을 선택하여 모든 권한으로 장치에 로그온합니다.

2. 컨트롤러를 유지 관리 모드로 배치하려면 다음을 입력합니다.
    
    `Enter-HcsMaintenanceMode`

    두 컨트롤러 모두 유지 관리 모드로 다시 시작합니다.

3. 유지 관리 모드 업데이트를 설치합니다. 형식:

    `Start-HcsUpdate`

    확인하라는 메시지가 표시됩니다. 업데이트를 확인하고 나면 현재 액세스 중인 컨트롤러에 업데이트가 설치됩니다. 업데이트가 설치되고 나면 컨트롤러가 다시 시작됩니다.

4. 업데이트 상태를 모니터링합니다. 현재 컨트롤러가 업데이트되며 다른 명령을 처리할 수 없으면 피어 컨트롤러에 로그인합니다. 형식:

    `Get-HcsUpdateStatus`

    `RunInProgress`가 `True`이면 업데이트가 아직 진행 중입니다. `RunInProgress`가 `False`이면 업데이트가 완료된 것입니다.

5. 디스크 펌웨어 업데이트가 적용되고 업데이트된 컨트롤러가 다시 시작되면 디스크 펌웨어 버전을 확인합니다. 업데이트된 컨트롤러에서 다음을 입력합니다.

    `Get-HcsFirmwareVersion`
   
    예상된 디스크 펌웨어 버전은 `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`입니다.

6. 유지 관리 모드를 종료합니다. 각 장치 컨트롤러에 대해 다음 명령을 입력합니다.

    `Exit-HcsMaintenanceMode`

    유지 관리 모드를 종료하면 컨트롤러가 다시 시작됩니다.

7. Azure Portal로 돌아갑니다. 유지 관리 모드 업데이트가 설치되었는지 24시간 동안 포털에 표시되지 않을 수도 있습니다.