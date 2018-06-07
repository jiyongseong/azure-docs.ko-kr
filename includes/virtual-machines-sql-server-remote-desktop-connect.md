
1. Azure 가상 컴퓨터를 만들고 실행한 후에 Azure Portal의 Virtual Machines 아이콘을 클릭하면 VM을 볼 수 있습니다.

1. 새 VM에 대한 줄임표(**...**)를 클릭합니다.

1. **Connect**를 클릭합니다.

   ![포털에서 VM에 연결](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. 브라우저가 VM에 대해 다운로드한 **RDP** 파일을 엽니다.

1. 원격 데스크톱 연결이 이 원격 연결의 게시자를 식별할 수 없다고 알립니다. **연결**을 클릭하여 계속합니다.

1. **Windows 보안** 대화 상자에서 **다른 계정 사용**을 클릭합니다. **More choices**(옵션 더 보기)를 클릭해야 보일 수도 있습니다. VM을 만들 때 구성한 사용자 이름과 암호를 지정합니다. 사용자 이름 앞에 백슬래시를 추가해야 합니다.

   ![원격 데스크톱 인증](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. **확인**을 클릭하여 연결합니다.
