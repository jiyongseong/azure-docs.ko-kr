### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>가상 컴퓨터용 TCP 끝점 만들기
인터넷에서 SQL 서버에 연결하려면, 가상 머신에 들어오는 TCP 통신을 수신하는 끝점이 있어야 합니다. 이 Azure 구성 단계에서는 들어오는 TCP 포트 트래픽을 가상 컴퓨터에 액세스 가능한 TCP 포트로 보냅니다.

> [!NOTE]
> 동일한 클라우드 서버 또는 가상 네트워크 내에서 연결하는 경우, 공개적으로 엑세스 할 수 있는 끝점을 만들 필요가 없습니다. 이 경우, 다음 단계를 계속 할 수 있습니다. 자세한 내용은 [연결 시나리오](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)를 참조하세요.
> 
> 

1. Azure Portal에서 **가상 머신(클래식)** 를 선택합니다.
2. 그런 후 SQL Server 가상 머신을 선택합니다.
3. **끝점**을 선택하고 끝점 블레이드 맨 위에 있는 **추가** 단추를 클릭합니다.
   
    ![포털의 끝점 만들기 단계](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. **끝점 추가** 블레이드에서 **이름**(예: SQLEndpoint)을 지정합니다.
5. **프로토콜**로 **TCP**를 선택합니다.
6. **공용 포트**에 대해 포트 번호(예: **57500**)를 지정합니다.
7. **개인 포트**에 대해 기본값이 **1433**인 SQL Server의 수신 대기 포트를 지정합니다.
8. **확인** 을 클릭하여 끝점을 만듭니다.

