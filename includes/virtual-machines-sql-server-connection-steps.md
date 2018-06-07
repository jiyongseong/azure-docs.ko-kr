### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Windows 방화벽에서 데이터베이스 엔진의 기본 인스턴스용 TCP 포트 열기
1. 원격 데스크톱을 사용하여 가상 머신에 연결합니다. VM에 연결하는 방법에 대한 자세한 내용은 [원격 데스크톱으로 SQL VM 열기](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop)를 참조하세요.
2. 시작 화면에서 로그인한 후 **WF.msc**를 입력하고 ENTER를 누릅니다.
   
    ![방화벽 프로그램 시작](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. **고급 보안이 포함된 Windows 방화벽**의 왼쪽 창에 있는 작업 창에서 **인바운드 규칙**을 마우스 오른쪽 단추로 클릭한 후 **새 규칙**을 클릭합니다.
   
    ![새 규칙](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. **새 인바운드 규칙 마법사** 대화 상자의 **규칙 유형**에서 **포트**를 선택하고 **다음**을 클릭합니다.
5. **프로토콜 및 포트** 대화 상자에서 기본 **TCP**를 사용합니다. **특정 로컬 포트** 상자에 데이터베이스 엔진의 인스턴스 포트 번호(기본 인스턴스의 경우 **1433** 또는 끝점 단계에서 개인 포트에 대해 선택한 포트)를 입력합니다.
   
    ![TCP 포트 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. **다음**을 클릭합니다.
7. **작업** 대화 상자에서 **연결 허용**을 선택한 후 **다음**을 클릭합니다.
   
    **보안 정보:** **안전한 경우 연결 허용**을 선택하면 추가 보안을 제공할 수 있습니다. 사용자 환경에서 추가 보안을 구성하려는 경우 이 옵션을 선택하세요.
   
    ![연결 허용](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. **프로필** 대화 상자에서 **공용**, **개인** 및 **도메인**을 선택합니다. 그런 후 **Next** 를 클릭합니다.
   
    **보안 정보:** **공개**를 선택하면 인터넷을 통한 액세스가 허용됩니다. 가능하면 더 제한적인 프로필을 선택하세요.
   
    ![공개 프로필](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. **이름** 대화 상자에서 이 규칙의 이름 및 설명을 입력한 후 **마침**을 클릭합니다.
   
    ![규칙 이름](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

필요한 경우 다른 구성 요소의 추가 포트를 엽니다. 자세한 내용은 [SQL Server 액세스를 허용하도록 Windows 방화벽 구성](http://msdn.microsoft.com/library/cc646023.aspx)을 참조하십시오.

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>TCP 프로토콜을 수신 대기하도록 SQL Server 구성

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>혼합 모드 인증을 위해 SQL Server 구성
SQL Server 데이터베이스 엔진은 도메인 환경에서만 Windows 인증을 사용할 수 있습니다. 다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 혼합 모드 인증을 위해 SQL Server를 구성하십시오. 혼합 모드 인증은 SQL Server 인증과 Windows 인증을 모두 허용합니다.

> [!NOTE]
> Azure Virtual Network를, 구성된 도메인 환경으로 구성한 경우, 혼합 모드 인증은 구성할 필요가 없습니다.
> 
> 

1. 가상 머신에 연결되어 있는 동안 시작 페이지에서 **SQL Server Management Studio** 를 입력하고 선택한 아이콘을 클릭합니다.
   
    처음으로 Management Studio를 열 때 사용자 Management Studio 환경이 만들어져야 합니다. 어느 정도 시간이 걸릴 수 있습니다.
2. Management Studio에서 **서버에 연결** 대화 상자가 표시됩니다. **서버 이름** 상자에 개체 탐색기를 사용하여 데이터베이스 엔진에 연결할 가상 머신의 이름을 입력합니다(가상 머신 이름 대신 **(로컬)** 또는 점(.)을 **서버 이름**으로 사용할 수 있음). **Windows 인증**을 선택하고, **사용자 이름** 상자의 ***your_VM_name*\your_local_administrator**를 그대로 둡니다. **Connect**를 클릭합니다.
   
    ![서버에 연결](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. SQL Server Management Studio 개체 탐색기에서 SQL Server 인스턴스의 이름(가상 머신 이름)을 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.
   
    ![서버 속성](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. **보안** 페이지의 **서버 인증**에서 **SQL Server 및 Windows 인증 모드**를 선택한 후 **확인**을 클릭합니다.
   
    ![인증 모드 선택](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. SQL Server Management Studio 대화 상자에서 **확인** 을 클릭하여 SQL Server를 다시 시작해야 하는 요구 사항을 확인합니다.
6. 개체 탐색기에서 서버를 마우스 오른쪽 단추로 클릭한 후 **다시 시작**을 클릭합니다. SQL Server 에이전트가 실행 중인 경우 에이전트도 다시 시작해야 합니다.
   
    ![다시 시작](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. SQL Server Management Studio 대화 상자에서 **예** 를 클릭하여 SQL Server를 다시 시작한다는 데 동의합니다.

### <a name="create-sql-server-authentication-logins"></a>SQL Server 인증 로그인 만들기
다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 SQL Server 인증 로그인을 하나 이상 만들어야 합니다.

1. SQL Server Management Studio 개체 탐색기에서 새 로그인을 만들 서버 인스턴스의 폴더를 확장합니다.
2. **보안** 폴더를 마우스 오른쪽 단추로 클릭하고, **새로 만들기**를 가리키고 **로그인...** 을 선택합니다.
   
    ![새 로그인](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. **로그인 - 신규** 대화 상자의 **일반** 페이지에 있는 **로그인 이름** 상자에 새 사용자의 이름을 입력합니다.
4. **SQL Server 인증**을 선택합니다.
5. **암호** 상자에 새 사용자의 암호를 입력합니다. **암호 확인** 상자에 암호를 다시 입력합니다.
6. 필요한 암호 적용 옵션(**암호 정책 강제 적용**, **암호 만료 강제 적용** 및 **다음 로그인할 때 반드시 암호 변경**)을 선택합니다. 이 로그인을 직접 사용하는 경우 다음 로그인 시 암호를 변경하도록 요구할 필요가 없습니다.
7. **기본 데이터베이스** 목록에서 로그인의 기본 데이터베이스를 선택합니다. **master** 가 이 옵션의 기본값입니다. 사용자 데이터베이스를 아직 만들지 않은 경우 **master**로 설정된 상태로 둡니다.
   
    ![로그인 속성](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. 처음 로그인을 만드는 경우 이 로그인을 SQL Server 관리자로 지정할 수 있습니다. 그렇게 하는 경우 **서버 역할** 페이지에서 **sysadmin**을 선택합니다.
   
   > [!NOTE]
   > sysadmin 고정 서버 역할의 구성원은 데이터베이스 엔진을 완전히 제어할 수 있습니다. 따라서 이 역할의 구성원은 신중하게 제한해야 합니다.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. 확인을 클릭합니다.

SQL Server 로그인에 대한 자세한 내용은 [로그인 만들기](http://msdn.microsoft.com/library/aa337562.aspx)를 참조하십시오.

