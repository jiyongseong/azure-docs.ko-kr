### <a name="determine-the-dns-name-of-the-virtual-machine"></a>가상 컴퓨터의 DNS 이름 확인
다른 컴퓨터에서 SQL Server 데이터베이스 엔진에 연결하려면 가상 머신의 DNS(Domain Name System) 이름을 알아야 합니다. 이 이름은 인터넷에서 가상 컴퓨터를 식별하는 이름입니다. IP 주소를 사용할 수 있지만 Azure가 중복 또는 유지 관리를 위해 리소스를 이동할 경우 IP 주소가 변경될 수 있습니다. DNS 이름은 새 IP 주소로 리디렉션할 수 있으므로 안정적입니다.  

1. Azure Portal(또는 이전 단계)에서 **가상 머신(클래식)** 를 선택합니다.
2. SQL VM을 선택합니다.
3. **가상 머신** 블레이드에서 가상 머신의 **DNS 이름**을 복사합니다.
   
    ![DNS 이름](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>다른 컴퓨터에서 데이터베이스 엔진에 연결
1. 인터넷에 연결된 컴퓨터에서 SQL Server Management Studio를 엽니다.
2. **서버에 연결** 또는 **데이터베이스 엔진에 연결** 대화 상자의 **서버 이름** 상자에 가상 머신의 DNS 이름(이전 작업에서 확인된 이름) 및 공개 끝점 포트 번호를 *DNS이름,포트번호* 형식(예: **mysqlvm.cloudapp.net,57500**)으로 입력합니다.
   
    ![SSMS를 사용하여 연결](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    이전에 맞는 공개 끝점 포트 번호를 기억하지 못하는 경우 **가상 머신** 블레이드의 **끝점** 영역에서 찾을 수 있습니다.
   
    ![공용 포트](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. **인증** 상자에 **SQL Server 인증**을 선택합니다.
4. **로그인** 상자에, 이전 작업에서 만든 로그인 이름을 입력합니다.
5. **암호** 상자에, 이전 작업에서 만든 로그인의 암호를 입력합니다.
6. **Connect**를 클릭합니다.

