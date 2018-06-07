#### <a name="to-install-mpio-on-the-host"></a>호스트에 MPIO를 설치하려면
1. Windows Server 호스트에서 서버 관리자를 엽니다. 기본적으로 관리자 그룹의 구성원이 Windows Server 2012 R2 또는 Windows Server 2012를 실행하는 컴퓨터에 로그온하면 서버 관리자가 시작됩니다. 서버 관리자가 아직 열려있지 않으면 **시작 > 서버 관리자**를 클릭합니다.
   
    ![서버 관리자](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. **서버 관리자 > 대시보드 > 역할 및 기능 추가**를 클릭합니다. 이렇게 하면 **역할 및 기능 추가** 마법사가 시작됩니다.
   
    ![역할 및 기능 추가 마법사 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. **역할 및 기능 추가** 마법사에서 다음을 수행합니다.
   
   * **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
   * **설치 유형 선택** 페이지에서 **역할 기반 또는 기능 기반** 설치의 기본 설정을 수락합니다. **다음**을 누릅니다.
     
       ![역할 및 기능 추가 마법사 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * **선택 대상 서버** 페이지에서 **서버 풀에서 서버 선택**을 선택합니다. 호스트 서버가 자동으로 검색됩니다. **다음**을 누릅니다.
   * **서버 역할 선택** 페이지에서 **다음**을 클릭합니다.
   * **기능 선택** 페이지에서 **다중 경로 I/O**를 선택하고 **다음**을 클릭합니다.
     
       ![역할 및 기능 추가 마법사 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * **설치 선택 확인** 페이지에서 선택 사항을 확인한 다음 아래 표시된 대로 **필요한 경우 자동으로 대상 서버 다시 시작**을 선택합니다. **Install**을 클릭합니다.
     
       ![역할 및 기능 추가 마법사 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * 설치가 완료되면 알림이 표시됩니다. **닫기** 를 클릭하여 마법사를 닫습니다.
     
       ![역할 및 기능 추가 마법사 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

