## <a name="prepare-your-raspberry-pi"></a>Raspberry Pi 준비

### <a name="install-raspbian"></a>Raspbian 설치

처음으로 Raspberry Pi를 사용하는 경우 키트에 포함된 SD 카드에서 NOOBS를 사용하여 Raspbian 운영 체제를 설치해야 합니다. [Raspberry Pi 소프트웨어 가이드][lnk-install-raspbian]는 Raspberry Pi에 운영 체제를 설치하는 방법을 설명합니다. 이 자습서에서는 Raspberry Pi에 Raspbian 운영 체제를 설치했다고 가정합니다.

> [!NOTE]
> [Raspberry Pi 3용 Microsoft Azure IoT 시작 키트][lnk-starter-kits]에 포함된 SD 카드에는 이미 NOOBS가 설치되어 있습니다. 이 카드에서 Raspberry Pi를 부팅할 수 있으며 Raspbian OS를 설치할 수 있습니다.

### <a name="set-up-the-hardware"></a>하드웨어 설정

이 자습서에서는 [Raspberry Pi 3용 Microsoft Azure IoT 시작 키트][lnk-starter-kits]에 포함된 BME280 센서를 사용하여 원격 분석 데이터를 생성합니다. Raspberry Pi 솔루션 대시보드에서 메서드 호출을 처리하는 경우를 나타내는 데 LED를 사용합니다.

브레드보드의 구성 요소는 다음과 같습니다.

- 빨강 LED
- 220옴 저항기(빨강, 빨강, 밤색)
- BME280 센서

다음 다이어그램에서는 하드웨어를 연결하는 방법을 보여 줍니다.

![Raspberry Pi의 하드웨어 설정][img-connection-diagram]

다음 표에서는 Raspberry Pi에서 브레드보드의 구성 요소로의 연결을 요약합니다.

| Raspberry Pi            | 브레드보드             |색         |
| ----------------------- | ---------------------- | ------------- |
| GND(14 핀)            | LED -ve 핀(18A)      | 자주색          |
| GPCLK0(7 핀)          | 저항기(25A)         | 주황색          |
| SPI_CE0(24 핀)        | CS(39A)               | 파랑          |
| SPI_SCLK(23 핀)       | SCK(36A)              | 노랑        |
| SPI_MISO(21 핀)       | SDO(37A)              | 흰색         |
| SPI_MOSI(19 핀)       | SDI(38A)              | 녹색         |
| GND(6 핀)             | GND(35A)              | 검정         |
| 3.3V(1 핀)           | 3Vo(34A)              | 빨강           |

하드웨어 설정을 완료하려면 다음을 수행해야 합니다.

- Raspberry Pi를 키트에 포함된 전원 공급 장치에 연결합니다.
- 키트에 포함된 이더넷 케이블을 사용하여 Raspberry Pi를 네트워크에 연결합니다. 또는 Raspberry Pi에 대해 [무선 연결][lnk-pi-wireless]을 설정할 수 있습니다.

이제 Raspberry Pi의 하드웨어 설정을 완료했습니다.

### <a name="sign-in-and-access-the-terminal"></a>터미널 로그인 및 액세스

Raspberry Pi의 터미널 환경에 액세스하는 두 가지 옵션이 있습니다.

- 키보드 및 모니터가 Raspberry Pi에 연결된 경우 Raspbian GUI를 사용하여 터미널 창에 액세스할 수 있습니다.

- 데스크톱 컴퓨터에서 SSH를 사용하여 Raspberry Pi의 명령줄에 액세스합니다.

#### <a name="use-a-terminal-window-in-the-gui"></a>GUI에서 터미널 창 사용

Raspbian에 대한 기본 자격 증명은 사용자 이름 **pi** 및 암호 **raspberry**입니다. GUI의 작업 표시줄에서 모니터 모양의 아이콘을 사용하여 **터미널** 유틸리티를 시작할 수 있습니다.

#### <a name="sign-in-with-ssh"></a>SSH를 사용하여 로그인

Raspberry Pi에 명령줄 액세스를 위해 SSH를 사용할 수 있습니다. [SSH(Secure Shell)][lnk-pi-ssh] 문서에서는 Raspberry Pi에서 SSH를 구성하는 방법 및 [Windows][lnk-ssh-windows] 또는 [Linux 및 Mac OS][lnk-ssh-linux]에서 연결하는 방법을 설명합니다.

사용자 이름 **pi** 및 암호 **raspberry**로 로그인합니다.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>선택 사항: Raspberry Pi의 폴더 공유

필요에 따라 다음 데스크톱 환경을 사용하여 Raspberry Pi의 폴더를 공유할 수 있습니다. 폴더를 공유하면 `nano` 또는 `vi`을 사용하는 대신 원하는 데스크톱 텍스트 편집기(예: [Visual Studio Code](https://code.visualstudio.com/) 또는 [Sublime Text](http://www.sublimetext.com/))를 사용하여 Raspberry Pi의 파일을 편집할 수 있습니다.

Windows 폴더를 공유하려면 Raspberry Pi Samba 서버를 구성합니다. 데스크톱에서 SFTP 클라이언트를 포함한 기본 제공 [SFTP](https://www.raspberrypi.org/documentation/remote-access/) 서버를 사용합니다.

### <a name="enable-spi"></a>SPI 사용

샘플 응용 프로그램을 실행하기 전에 Raspberry Pi에서 SPI(Serial Peripheral Interface) 버스를 사용하도록 설정해야 합니다. Raspberry Pi는 SPI 버스를 통해 BME280 센서 장치와 통신합니다. 다음 명령을 사용하여 구성 파일을 편집합니다.

```sh
sudo nano /boot/config.txt
```

다음과 같은 줄을 찾습니다.

`#dtparam=spi=on`

- 줄의 주석 처리를 제거하려면 시작 부분에서 `#`을 삭제합니다.
- 변경 내용을 저장하고(**Ctrl-O**, **Enter**) 편집기를 종료합니다(**Ctrl-X**).
- SPI를 사용하려면 Raspberry Pi를 다시 부팅합니다. 다시 부팅하면 터미널의 연결이 끊어지므로 Raspberry Pi를 다시 시작할 경우 다시 로그인해야 합니다.

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-v1-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/