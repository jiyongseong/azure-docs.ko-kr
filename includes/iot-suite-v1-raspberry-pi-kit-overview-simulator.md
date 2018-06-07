## <a name="overview"></a>개요

이 자습서에서는 다음 단계를 완료합니다.

- Azure 구독에서 미리 구성된 원격 모니터링 솔루션의 인스턴스를 배포합니다. 이 단계에서는 여러 Azure 서비스를 자동으로 배포하고 구성합니다.
- 사용자 컴퓨터 및 원격 모니터링 솔루션과 통신하도록 장치를 설정합니다.
- 샘플 장치 코드를 업데이트하여 원격 모니터링 솔루션에 연결하고 솔루션 대시보드에서 볼 수 있는 시뮬레이션된 원격 분석을 보냅니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [!NOTE]
> 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

### <a name="required-software"></a>필수 소프트웨어

Raspberry Pi의 명령줄에 원격으로 액세스할 수 있도록 데스크톱 컴퓨터에 SSH 클라이언트가 필요합니다.

- Windows에서는 SSH 클라이언트를 포함하지 않습니다. [PuTTY](http://www.putty.org/)를 사용하는 것이 좋습니다.
- 대부분의 Linux 배포판 및 Mac OS는 명령줄 SSH 유틸리티를 포함합니다. 자세한 내용은 [Linux 또는 Mac OS를 사용하는 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)를 참조하세요.

### <a name="required-hardware"></a>필수 하드웨어

Raspberry Pi의 명령줄에 원격으로 연결할 수 있는 데스크톱 컴퓨터이며

[Raspberry Pi 3용 Microsoft IoT 시작 키트][lnk-starter-kits] 또는 동등한 구성 요소입니다. 이 자습서에서는 키트에서 다음 항목을 사용합니다.

- Raspberry Pi 3
- MicroSD 카드(NOOBS 포함)
- USB 미니 케이블
- 이더넷 케이블

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/