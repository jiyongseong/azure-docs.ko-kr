Visual Studio에서 서버 탐색기를 사용하여 Azure에 가상 컴퓨터를 만들 수 있습니다.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>서버 탐색기에서 Azure 가상 컴퓨터 만들기
[Azure 관리 포털](http://go.microsoft.com/fwlink/?LinkID=253103)에서 가상 컴퓨터를 만들 수 있지만, Azure에서도 서버 탐색기에 명령을 사용하여 가상 컴퓨터를 만들 수 있습니다. 예를 들어 가상 컴퓨터를 사용하여 부하 분산된 공용 끝점 뒤에 프런트 엔드를 제공할 수 있습니다.

### <a name="to-create-a-new-virtual-machine"></a>새 가상 컴퓨터 만들기
1. 서버 탐색기에서 **Azure** 노드를 열고 **Virtual Machines**를 클릭합니다.
2. 상황에 맞는 메뉴에서 **가상 컴퓨터 만들기**를 클릭합니다.
   
    **새 가장 컴퓨터 만들기** 마법사가 나타납니다.
   
    ![가상 컴퓨터 만들기 명령](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. **구독 선택** 페이지에서, 가상 컴퓨터를 만들 때 사용할 구독을 선택한 후 **다음**을 클릭합니다.
   
    Azure에 로그인하지 않은 경우 **로그인** 을 클릭하여 로그인합니다. 그런 다음 Azure 구독이 선택되지 않은 경우 드롭다운 목록 상자에서 선택합니다.
4. **가상 컴퓨터 이미지 선택** 페이지의 **이미지 형식** 드롭다운 목록 상자에서 이미지 형식을 선택한 다음 **이미지 이름** 목록 상자에서 가장 컴퓨터 이미지를 선택합니다. 완료되면 **확인**을 클릭합니다.
   
    ![가상 컴퓨터 이미지 페이지 선택](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    다음의 이미지 형식을 선택할 수 있습니다.
   
   * **공용 이미지** 는 Windows Server 및 SQL Server와 같은 서버 소프트웨어와 운영 체제의 가상 컴퓨터 이미지를 나열합니다.
   * **MSDN 이미지** 는 Visual Studio 및 Microsoft Dynamics와 같은 MSDN 구독자가 사용 가능한 소프트웨어의 가상 컴퓨터 이미지를 나열합니다.
   * **개인 이미지** 는 사용자가 만든 특수화 및 일반화된 가상 컴퓨터 이미지를 나열합니다.
     
     전문화 및 일반화된 가상 컴퓨터에 대해 알아보려면 [VM 이미지](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/)를 참조하세요. 새롭게 미리 구성된 가상 컴퓨터를 빠르게 만들기 위해 사용할 템플릿으로 가상 컴퓨터를 설정하는 방법에 대한 정보는 [템플릿으로 사용할 Windows 가상 컴퓨터를 캡처하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) 을 참조하세요.
     
     가상 컴퓨터 이미지 이름을 클릭하여 페이지 오른쪽에 있는 이미지에 대한 정보를 볼 수 있습니다.
     
     > [!NOTE]
     > **공용 이미지** 또는 **MSDN 이미지** 목록은 읽기 전용이므로 가상 컴퓨터 이미지를 추가할 수 없습니다. 만드는 모든 가상 컴퓨터는 **개인 이미지** 목록에 추가됩니다.
     > 
     > 
     
     Visual Studio 수준 구독이 있는 MSDN 구독자인 경우에 Visual Studio 및 다른 여러 이미지를 포함하는 미리 작성된 Azure 가상 컴퓨터를 만들 수 있습니다. 자세한 내용은 [MSDN 구독자를 위한 Images Visual Studio 2013 갤러리 이미지를 사용하여 Visual Studio에서 가상 컴퓨터 만들기](http://visualstudio2013msdngalleryimage.azurewebsites.net) 및 [MSDN 구독](https://www.visualstudio.com/products/msdn-subscriptions-vs)을 참조하세요.|
5. **가상 컴퓨터 기본 설정** 페이지에서 컴퓨터 이름을 입력한 다음 크기, 사용자 이름 및 암호를 포함한 가상 컴퓨터에 대한 사양을 추가합니다. 완료되면 **확인**을 클릭합니다.
   
    원격 데스크톱을 사용하여 컴퓨터에 로그인하기 위해 새 이름과 암호를 사용하므로 잊어버리지 않도록 기록해 두는 것이 좋습니다. Visual Studio에 Azure 가상 컴퓨터를 만든 후 [Azure 관리 포털](http://go.microsoft.com/fwlink/?LinkID=253103)에서 크기 및 다른 설정을 변경할 수 있습니다.
   
   > [!NOTE]
   > 더 큰 크기의 가상 컴퓨터를 선택하면 추가 요금이 부과될 수 있습니다. 자세한 내용은 [가상 컴퓨터 가격 정보](https://azure.microsoft.com/pricing/details/virtual-machines/) 를 참조하세요.
   > 
   > 
6. Visual Studio에서 만든 가상 컴퓨터는 클라우드 서비스를 필요로 합니다. **클라우드 서비스 설정** 페이지에서, 가상 컴퓨터에 대한 클라우드 서비스를 선택하거나 또는 클라우드 서비스가 아직 없거나 새로운 것을 사용하려는 경우 드롭다운 목록에서 **<새로 만들기...>**를 선택합니다. 저장소 계정도 필요하므로 **저장소 계정** 드롭다운 목록 상자에서 저장소 계정을 선택 (또는 새로운 저장소 계정을 생성)합니다. 자세한 내용은 [Microsoft Azure 저장소 소개](../articles/storage/common/storage-introduction.md) 를 참조하세요.
7. 가상 네트워크를 지정하려는 경우 (선택 사항) 가상 네트워크 및 서브넷 드롭다운 목록 상자에서 선택합니다.
   
    가용성 집합의 멤버인 가상 컴퓨터는 각기 다른 장애 도메인에 배포됩니다. 자세한 내용은 [Azure 가상 네트워크](https://azure.microsoft.com/services/virtual-network/) 를 참조하세요.
8. 가상 컴퓨터를 가용성 집합에 포함시키려는 경우(선택 사항) **가용성 집합 지정** 확인란을 선택한 후 드롭다운 목록 상자에서 가용성 집합을 선택합니다. 완료되면 **다음** 단추를 선택합니다.
   
    가용성 집합에 가상 컴퓨터를 추가하면 네트워크 오류, 로컬 디스크 하드웨어 오류 및 계획된 가동 중지 시간 중에도 응용 프로그램을 사용하는데 도움이 됩니다. 가상 네트워크, 서브넷 및 가용성 집합을 만들려면 [Azure 관리 포털](http://go.microsoft.com/fwlink/?LinkID=253103) 을 사용해야 합니다. 자세한 내용은 [가상 컴퓨터의 가용성 관리](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) 를 참조하세요.
9. **끝점** 페이지에서 가상 컴퓨터의 사용자에게 가능하게 할 공용 끝점을 지정합니다. 예를 들어 기본으로 가능한 원격 데스크톱 및 PowerShell 끝점 외에도 HTTP (포트 80)를 사용 가능하도록 선택할 수 있습니다. 끝점을 추가하려면 **포트 이름** 드롭다운 목록 상자에서 하나를 선택한 후 **추가** 단추를 클릭합니다. 끝점을 제거하려면 끝점 목록의 이름 옆에 있는 빨간색 **X** 를 선택합니다.
   
    ![가상 컴퓨터 마법사의 끝점 페이지입니다.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    가상 컴퓨터에 대해 선택한 클라우드 서비스에 따라 사용할 수 있는 끝점이 다릅니다. 자세한 내용은 [Azure 서비스 끝점](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) 을 참조하세요.
   
   > [!NOTE]
   > 공용 끝점을 사용 가능하게 하면 가상 컴퓨터의 서비스가 인터넷에서도 사용할 수 있도록 합니다. 끝점에 대한 액세스 제어 목록 설정 등 가상 컴퓨터에 끝점과 서비스를 설치하고 올바르게 구성되도록 확인합니다. 자세한 내용은 [가상 컴퓨터로 끝점을 설정하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) 을 참조하세요.
   > 
   > 
10. 가상 컴퓨터 설정 구성을 마친 후 **만들기** 단추를 선택하여 가상 컴퓨터를 만듭니다.
    
     Azure가 가상 컴퓨터를 만들면 **Azure 활동 로그** 는 가상 컴퓨터 만들기 작업의 진행률을 보여 줍니다.
    
     ![가상 컴퓨터 활동 로그 - 진행 중입니다.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     가상 컴퓨터 정보만 보려면 **Azure 활동 로그**에서 **Virtual Machines** 탭을 선택합니다.
    
     ![가상 컴퓨터 활동 로그 - 완료했습니다.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     작업이 성공적으로 완료되면 서버 탐색기의 **가상 컴퓨터** 노드 아래에 새로운 가상 컴퓨터가 나타납니다. **원격 데스크톱을 사용하여 연결** 바로 가기를 클릭해 로그인할 수 있습니다.
    
     ![서버 탐색기에 표시되는 가상 컴퓨터입니다.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>가상 컴퓨터 관리
가상 컴퓨터 구성 페이지에서 종료, 연결, 새로 고침 및 선택한 가상 컴퓨터에 검사점을 추가하는 것 외에도 가상 컴퓨터를 확인하거나 설정을 변경할 수 있습니다. 다음을 수행할 수 있습니다.

* 가상 컴퓨터 크기를 변경합니다.
* 가상 컴퓨터와 함께 사용할 가용성 집합을 선택합니다.
* 공용 끝점에 대한 설정을 추가, 제거 또는 변경합니다.
* 가상 컴퓨터 확장을 추가, 제거 또는 구성합니다.
* 가상 컴퓨터에 연결된 디스크에 대한 정보를 봅니다.

### <a name="view-or-change-virtual-machine-settings"></a>가상 컴퓨터 설정 보기 또는 변경
1. 서버 탐색기의 **Azure 가상 컴퓨터** 노드에서 가상 컴퓨터를 선택합니다.
2. 바로 가기 메뉴에서 **구성** 을 선택하여 가상 컴퓨터 구성 페이지를 확인합니다.
   
    ![Azure 가상 컴퓨터 구성 페이지](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. 가상 컴퓨터 정보를 확인하거나 변경합니다.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>가상 컴퓨터의 상태를 저장하거나 복원합니다.
가상 컴퓨터를 구성하고 그 위에 소프트웨어를 설치하므로 가상 컴퓨터 검사점을 만들어 정기적으로 진행을 저장하는 것이 좋습니다. 검사점은 가상 컴퓨터의 현재 상태의 스냅숏 또는 이미지입니다. 가상 컴퓨터에 문제가 발생 거나 가상 컴퓨터를 다시 구성하는 경우 처음부터 다시 시작하는 대신 이전 검사점 상태로 복원하여 시간을 절약할 수 있습니다.

### <a name="to-create-a-virtual-machine-checkpoint"></a>가상 컴퓨터 검사점을 만들려면
1. 서버 탐색기의 **Azure 가상 컴퓨터** 노드에서 가상 컴퓨터를 선택합니다.
2. 바로 가기 메뉴에서 **구성** 을 선택하여 가상 컴퓨터 구성 페이지를 확인합니다.
3. 구성 페이지에서 **이미지 캡처** 단추를 선택합니다.
   
    ![Azure 구성 페이지 캡처 단추](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    **가상 컴퓨터 캡처** 대화 상자가 나타납니다.
   
    ![Azure 캡처 가상 컴퓨터 대화 상자](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. 이미지 레이블 및 설명을 제공합니다. 기본 레이블 및 설명이 제공되지만 원하는 경우 덮어쓸 수 있습니다.
5. 이 가상 컴퓨터에 이미 Sysprep을 실행하는 경우 **가상 컴퓨터에서 Sysprep을 실행했습니다** 상자를 선택합니다.
   
    Sysprep은 특별히 가상 컴퓨터 버전의 Windows에서 시스템 특정 데이터를 제거하는 도구로, 해당 데이터를 다른 사람이 사용할 수 있는 템플릿으로 만듭니다. 자세한 내용은 [템플릿으로 사용할 Windows 가상 컴퓨터를 캡처하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) 을 참조하세요. Sysprep를 실행하기 전에 VM을 백업합니다.
6. 구성과 캡처 설정을 마친 후 **캡처** 단추를 선택하여 검사점을 만듭니다.
   
    Azure가 검사점을 만들면 **Azure 활동 로그** 는 작업의 진행률을 보여 줍니다.
   
    ![가상 컴퓨터 검사점 캡처하기](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    검사점 작업이 완료되면 **Azure 활동 로그**에서 볼 수 있습니다.
   
    ![완료된 검사점 작업](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>가상 컴퓨터 검사점을 관리하려면
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>가상 컴퓨터를 이전에 저장된 상태로 복원하려면
* [단계별 작업: PowerShell - 2부를 사용하여 Microsoft Azure 가상 컴퓨터의 클라우드 복원 수행](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx)에 설명된 단계를 진행합니다.

### <a name="to-delete-a-checkpoint"></a>검사점을 삭제하려면
1. [Azure 관리 포털](http://go.microsoft.com/fwlink/?LinkID=253103)로 이동합니다.
2. 가상 컴퓨터 구성 페이지에서 페이지 위쪽의 **이미지** 탭을 선택합니다.
3. 삭제하려는 검사점을 선택한 후 페이지 아래에서 **삭제** 단추를 선택합니다.

## <a name="shut-down-your-virtual-machine"></a>가상 컴퓨터 종료
1. 서버 탐색기의 **Azure 가상 컴퓨터** 노드에서 종료하려는 가상 컴퓨터를 선택합니다.
2. 바로 가기 메뉴에서 **종료** 명령을 선택하거나 또는 **구성**을 선택하여 가상 컴퓨터 구성 페이지를 확인한 후 **종료** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계
가상 컴퓨터를 만드는 방법을 자세히 알아보려면 [Linux를 실행하는 가상 컴퓨터 만들기](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

