<!--author=alkohli last changed: 09/28/17-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>최소 StorSimple 장치 설치를 완료하려면

   > [!NOTE]
   > 최소 장치 설정이 완료되면 장치 이름을 변경할 수 없습니다.
   
1. **장치** 블레이드의 테이블 형식 장치 목록에서 장치를 선택하고 클릭합니다. 장치가 **설정 준비 완료** 상태입니다. **장치 구성** 블레이드가 열립니다.

     ![StorSimple 최소 장치 설치 네트워크 인터페이스](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. **장치 구성** 블레이드에서:
   
   1. 장치의 **이름**을 제공합니다. 기본 장치 이름은 장치 모델 및 일련 번호와 같은 정보를 반영합니다. 최대 64자의 친숙한 이름을 할당하여 장치를 관리할 수 있습니다.
   2. 장치가 배포되는 지리적 위치에 기반한 **표준 시간대**를 설정합니다. 장치는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.
   3. **DATA 0 설정** 아래에서:

       1. 데이터 0 네트워크 인터페이스는 설치 마법사를 통해 구성된 네트워크 설정(IP, 서브넷, 게이트웨이)이 활성화된 대로 표시됩니다. 또한 데이터 0은 클라우드뿐만 아니라 iSCSI에 대해서도 자동으로 활성화됩니다.

       2. 컨트롤러 0과 컨트롤러 1에 대한 고정 IP 주소를 입력합니다. **컨트롤러 고정 IP 주소는 장치 IP 주소를 통해 접근할 수 있는 서브넷 내의 사용 가능한 IP여야 합니다.** 데이터 0 인터페이스가 IPv4에 대해 구성된 경우 고정 IP 주소는 IPv4 형식으로 제공해야 합니다. IPv6 구성에 대한 접두사를 제공하는 경우 고정 IP 주소는 이러한 필드에 자동으로 채워집니다.

            ![StorSimple 최소 장치 설치 네트워크 인터페이스](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            컨트롤러의 고정 IP 주소는 가비지 수집 및 장치에 대한 업데이트를 제공하는 데 사용됩니다. 따라서 고정 IP는 라우팅 가능하고 인터넷에 연결할 수 있어야 합니다. [Test-HcsmConnection][Test] cmdlet을 사용하여 고정된 컨트롤러 IP가 라우팅할 수 있는지 확인할 수 있습니다. 다음 예제는 고정된 컨트롤러 IP가 인터넷으로 라우팅되고 Microsoft 업데이트 서버에 액세스할 수 있음을 보여줍니다.

            ![라우팅 가능한 IP를 표시하는 Test-HcsmConnection](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. **확인**을 클릭합니다. 장치 구성이 시작됩니다. 장치 구성이 완료되면 알림이 표시됩니다. 장치 상태가 **장치** 블레이드에서 **온라인**으로 변경됩니다.

    ![StorSimple 최소 장치 설치 네트워크 인터페이스](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx
