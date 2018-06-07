<!--author=alkohli last changed:02/10/2017-->


#### <a name="to-create-a-new-service"></a>새 서비스를 만들려면

1. Microsoft 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그온합니다.

2. Azure Portal에서 **리소스 만들기**를 클릭하고 Marketplace에서 **모두 보기**를 클릭합니다.

    ![StorSimple 장치 관리자 만들기](./media/storsimple-8000-create-new-service/createssdevman1.png)

    _StorSimple 실제_를 검색합니다. **StorSimple 실제 장치 시리즈**를 선택하여 클릭한 후 **만들기**를 클릭합니다. 또는 Azure Portal에서 **+**를 클릭한 다음 **저장소** 아래에서 **StorSimple 실제 장치 시리즈**를 클릭합니다.

    ![StorSimple 장치 관리자 만들기](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. **StorSimple 장치 관리자** 블레이드에서 다음 단계를 수행합니다.
   
   1. 서비스에 고유한 **리소스 이름**을 지정합니다. 이 이름은 서비스를 식별하는 데 사용할 수 있는 친숙한 이름입니다. 이름은 문자, 숫자 및 하이픈이 될 수 있는 2자에서 50자 사이여야 합니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.

   2. 드롭다운 목록에서 **구독** 을 선택합니다. 구독은 대금 청구 계정에 연결됩니다. 이 필드는 구독이 하나만 있는 경우에는 나타나지 않습니다.

   3. **리소스 그룹**의 경우 **기존 항목을 사용**하거나 **새 그룹을 만듭니다**. 자세한 내용은 [Azure 리소스 그룹](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)을 참조하세요.
   
   4. 서비스의 **위치** 를 지정합니다. 일반적으로 장치를 배포하려는 지리적 지역에 가장 가까운 위치를 선택합니다. 다음 사항을 고려할 수도 있습니다. 
      
      * StorSimple 장치로 배포하려는 Azure에 부하가 있는 경우, 해당 데이터 센터를 사용해야 합니다.
      * StorSimple 장치 관리자 서비스와 Azure Storage는 두 개의 별도 위치에 있을 수 있습니다. 이 경우, StorSimple 장치 관리자 및 Azure Storage 계정을 별도로 만들어야 합니다. Azure Storage 계정을 만들려면 Azure Portal의 Azure Storage 서비스로 이동하고 [Azure Storage 계정 만들기](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)의 단계를 수행합니다. 이 계정을 만든 후에 [서비스에 대한 새 저장소 계정 구성](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service)의 단계를 수행하여 StorSimple 장치 관리자 서비스에 추가합니다.

   5. **새 저장소 계정 만들기** 를 선택하여 서비스를 포함하는 저장소 계정을 자동으로 만듭니다. 이 저장소 계정에 대해 이름을 지정합니다. 다른 위치에 있는 데이터가 필요하면 확인란의 선택을 취소합니다.

   6. 대시보드에서 이 서비스에 대한 빠른 링크가 필요한 경우 **대시보드에 고정**을 확인하세요.
      
   7. **만들기**를 클릭하여 StorSimple 장치 관리자를 만듭니다.

       ![StorSimple 장치 관리자 만들기](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
서비스 생성은 몇 분 정도가 소요됩니다. 서비스가 성공적으로 만들어지면 알림이 표시되고 새 서비스 블레이드가 열립니다.
   
![StorSimple 장치 관리자 만들기](./media/storsimple-8000-create-new-service/createssdevman5.png)


