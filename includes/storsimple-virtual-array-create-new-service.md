#### <a name="to-create-a-new-service"></a>새 서비스를 만들려면

1.  URL <https://portal.azure.com/>에서 Microsoft 계정 자격 증명을 사용하여 Azure Portal에 로그온합니다. 정부 포털에서 장치를 배포하는 경우 <https://portal.azure.us/>에 로그인합니다.

2.  Azure Portal에서 **+리소스 만들기** &gt; **저장소** &gt; **StorSimple 가상 시리즈**를 클릭합니다.

    ![새 서비스 만들기](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  열린 **StorSimple 장치 관리자** 블레이드에서 다음을 수행합니다.

    1.  서비스에 고유한 **리소스 이름**을 지정합니다. 이 리소스 이름은 서비스를 식별하는 데 사용할 수 있는 친숙한 이름입니다. 이름은 문자, 숫자 및 하이픈이 될 수 있는 2자에서 50자 사이여야 합니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.

    2.  드롭다운 목록에서 **구독** 을 선택합니다. 구독은 대금 청구 계정에 연결됩니다. 이 필드는 구독이 하나만 있는 경우에는 나타나지 않습니다.

    3.  **리소스 그룹**의 경우 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 자세한 내용은 [Azure 리소스 그룹](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)을 참조하세요.

    4.  서비스의 **위치** 를 지정합니다. 각 지역에서 어떤 서비스가 가능한지에 대한 자세한 정보는 [Azure 지역](https://azure.microsoft.com/regions/#services) 을 참조하세요. 일반적으로 장치를 배포하려는 지리적 지역에 가장 가까운 **위치**를 선택합니다. 다음 사항을 고려할 수도 있습니다.

        -   Azure에 StorSimple 장치에 배포하려는 기존 워크로드가 있는 경우에는 해당 데이터 센터를 사용하는 것이 좋습니다.

        -   StorSimple 장치 관리자 및 Azure Storage는 두 곳의 다른 위치에 있을 수 있습니다. 이 경우, StorSimple 장치 관리자 및 Azure Storage 계정을 별도로 만들어야 합니다. Azure Storage 계정을 만들려면 Azure Portal의 Azure Storage 서비스로 이동하고 [Azure Storage 계정 만들기](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)의 단계를 수행합니다. 이 계정을 만든 후에 [서비스에 대한 새 저장소 계정 구성](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service)의 단계를 수행하여 StorSimple 장치 관리자 서비스에 추가합니다.

        -   정부 포털에서 가상 장치를 배포하는 경우 미국 아이오와 및 미국 버지니아 위치에서 StorSimple 장치 관리자 서비스를 사용할 수 있습니다.

    5.  **새 Azure 저장소 계정 만들기** 를 선택하여 서비스를 포함하는 저장소 계정을 자동으로 만듭니다. **Storage 계정 이름**을 지정합니다. 다른 위치에 있는 데이터가 필요하면 확인란의 선택을 취소합니다.

    6.  대시보드에서 이 서비스에 대한 빠른 링크가 필요한 경우 **대시보드에 고정**을 확인하세요.

    7.  **만들기**를 클릭하여 StorSimple 장치 관리자를 만듭니다.

        ![새 서비스 만들기](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

**서비스** 방문 페이지로 이동됩니다. 서비스 생성은 몇 분 정도가 소요됩니다. 서비스가 성공적으로 만들어진 후 적절하게 알림이 표시되며 서비스 상태가 **활성**으로 변경됩니다.


