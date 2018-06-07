도메인 이름의 레코드가 전파된 후 브라우저를 사용하여 Azure App Service의 웹앱에 액세스하는 데 사용자 지정 도메인 이름을 사용할 수 있는지 확인할 수 있습니다.

> [!NOTE]
> CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 등의 서비스를 사용하여 CNAME을 사용할 수 있는지 확인할 수 있습니다.
> 
> 

웹앱을 Traffic Manager 끝점으로 아직 추가하지 않은 경우 이름 확인이 적용되기 전에 사용자 지정 도메인 이름이 Traffic Manager로 라우팅할 때 이를 수행해야 합니다. 그런 다음 Traffic Manager가 웹앱으로 라우팅됩니다. [끝점 추가 또는 삭제](../articles/traffic-manager/traffic-manager-endpoints.md) 의 내용을 참조하여 Traffic Manager 프로필에서 웹앱을 끝점으로 추가합니다.

> [!NOTE]
> 끝점을 추가할 때 웹앱이 목록에 나열되지 않는 경우 웹앱이 **표준** App Service 계획 모드로 구성되었는지 확인합니다. **표준** 모드로 구성된 웹앱만이 Traffic Manager에서 작동합니다.
> 
> 

1. 브라우저에서 [Azure Portal](https://portal.azure.com)을 엽니다.
2. **Web Apps** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인**을 선택합니다.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. **사용자 지정 도메인** 블레이드에서 **호스트 이름 추가**를 클릭합니다.
4. **호스트 이름** 텍스트 상자에 이 웹앱과 연결할 Traffic Manager 도메인 이름을 입력합니다.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. **유효성 검사** 를 클릭하여 도메인 이름 구성을 저장합니다.
6. **유효성 검사** 를 클릭하면 Azure에서 도메인 검증 워크플로가 시작됩니다. 여기서는 호스트 이름 가용성 및 보고서 성공 여부 외에도 도메인 소유권을 확인하거나 자세한 오류 지침과 그에 대한 해결 방법을 확인할 수 있습니다.    
7. 유효성 검사가 성공하면 **호스트 이름 추가** 단추가 활성화되며 호스트 이름을 할당할 수 있게 됩니다. 이제 브라우저에서 사용자 지정 도메인 이름으로 이동합니다. 이제 사용자 지정 도메인 이름을 사용하여 앱이 실행되고 있음을 확인할 수 있습니다. 
   
   구성이 완료되면 사용자 지정 도메인 이름이 웹앱의 **도메인 이름** 섹션에 표시됩니다.

이때 브라우저에 Traffic Manager 도메인 이름을 입력하여 웹앱으로 성공적으로 이동하는지 확인할 수 있습니다.

