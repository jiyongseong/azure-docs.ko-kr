* **vCenter 추가**에서 vSphere 호스트 또는 vCenter Server에 대한 식별 이름을 지정하고 서버의 IP 주소 또는 FQDN을 지정합니다. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다. VMware vCenter 또는 vSphere ESXi 서버에 연결할 계정을 선택합니다. **확인**을 클릭합니다.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > vCenter 또는 호스트 서버에 대해 관리자 권한이 없는 계정으로 VMware vCenter Server 또는 VMware vSphere 호스트를 추가하는 경우 계정에 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 컴퓨터, vSphere Distributed Switch에 대한 권한이 설정되어 있는지 확인합니다. 또한 VMware vCenter 서버에 저장소 보기 권한을 사용하도록 설정되어야 합니다.
