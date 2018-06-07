## <a name="prerequisites"></a>필수 조건
CDN 관리 코드를 작성하려면 먼저 코드가 Azure Resource Manager와 상호 작용하도록 몇 가지 준비 작업을 수행해야 합니다. 이러한 준비를 위해 다음이 필요합니다.

* 이 자습서에서 만든 CDN 프로필을 담기 위한 리소스 그룹 만들기
* 응용 프로그램에 대한 인증을 제공하도록 Azure Active Directory 구성
* 리소스 그룹에 권한을 적용하여 Azure AD 테넌트의 권한이 있는 사용자만 CDN 프로필과 상호 작용하게 만들기

### <a name="creating-the-resource-group"></a>리소스 그룹 만들기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기**를 클릭합니다.
3. **리소스 그룹**을 검색하고 리소스 그룹 창에서 **만들기**를 클릭합니다.

    ![새 리소스 그룹 만들기](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. 리소스 그룹 이름을 *CdnConsoleTutorial*로 지정합니다.  구독을 선택하고 가까운 위치를 선택합니다.  원한다면 **대시보드에 고정** 확인란을 클릭하여 리소스 그룹을 포털 대시보드에 고정할 수 있습니다.  고정하면 나중에 쉽게 찾을 수 있습니다.  항목을 선택한 후에 **만들기**를 클릭합니다.

    ![리소스 그룹에 이름 지정하기](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. 리소스 그룹을 만들고 대시보드에 고정하지 않았다면 **찾아보기**, **리소스 그룹**을 클릭하여 찾을 수 있습니다.  리소스 그룹을 클릭하여 엽니다.  **구독 ID**를 적어둡니다. 나중에 필요합니다.

    ![리소스 그룹에 이름 지정하기](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Azure AD 응용 프로그램 만들기 및 사용 권한 적용
Azure Active Directory로 앱을 인증하는 방법은 두 가지가 있습니다. 개별 사용자 또는 서비스 주체 인증입니다. 서비스 주체는 Windows의 서비스 계정과 비슷합니다.  특정 사용자에게 CDN 프로필과 상호 작용하는 권한을 부여하는 대신 서비스 주체에게 권한을 부여합니다.  일반적으로, 서비스 주체는 자동화된 비대화형 프로세스에 사용됩니다.  이 자습서에서는 대화형 콘솔 앱을 작성하지만, 서비스 주체 인증 방식에 초점을 맞출 것입니다.

Azure Active Directory 응용 프로그램 만들기를 비롯하여 여러 반계로 구성된 서비스 주체를 만듭니다.  만들려면 [이 자습서를 따라야 합니다](../articles/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> [연결된 자습서](../articles/resource-group-create-service-principal-portal.md)의 모든 단계를 따르세요.  설명한 대로 완료하는 것이 *중요합니다*.  나중에 이 정보가 필요하므로 **테넌트 ID**, **테넌트 도메인 이름**(사용자 지정 도메인을 지정하지 않을 경우, 일반적으로는 *.onmicrosoft.com* 도메인), **클라이언트 ID**, **클라이언트 인증 키**를 메모합니다.  **클라이언트 ID**와 **클라이언트 인증 키**를 신중하게 보호해야 합니다. 이 자격 증명을 사용하는 사람은 누구나 서비스 주체로 작업을 실행할 수 있습니다.
>
> 다중 테넌트 응용 프로그램 구성 단계에 도달하면 **아니요**를 선택합니다.
>
> [역할에 응용 프로그램 할당](../articles/azure-resource-manager/resource-group-create-service-principal-portal.md#assign-application-to-role) 단계에 도달하면 **독자** 역할 대신 앞서 만든 리소스 그룹 *CdnConsoleTutorial*을 사용하여 **CDN 프로필 참가자** 역할을 할당합니다.  리소스 그룹에서 응용 프로그램에 **CDN 프로필 참가자** 역할을 할당한 후에 이 자습서로 돌아옵니다. 
>
>

서비스 주체를 만들고 **CDN 프로필 참가자** 역할을 할당했으면 리소스 그룹의 **사용자** 블레이드는 다음 이미지처럼 보여야 합니다.

![사용자 블레이드](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>대화형 사용자 인증
서비스 주체 대신 대화형 개별 사용자 인증을 사용할 경우, 그 과정은 서비스 주체 인증과 유사합니다.  사실, 똑같은 절차를 따르면서 사소한 몇 가지만 변경하면 됩니다.

> [!IMPORTANT]
> 서비스 주체 대신 개별 사용자 인증을 사용할 경우에만 다음 단계를 수행하세요.
>
>

1. 응용 프로그램을 만들 때 **웹 응용 프로그램** 대신 **네이티브 응용 프로그램**을 선택합니다.

    ![네이티브 응용 프로그램](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. 다음 페이지에서 **URI 리디렉션** 메시지가 표시됩니다.  이 URI은 유효성을 검사하지 않지만, 입력한 내용을 기억해야 합니다. 나중에 필요합니다.
3. **클라이언트 인증 키**를 만들 필요가 없습니다.
4. 서비스 주체를 **CDN 프로필 참가자** 역할에 할당하는 대신 개별 사용자 또는 그룹을 할당합니다.  이 예제에서는 *CDN 데모 사용자* 를 **CDN 프로필 참가자** 역할에 할당한 것을 알 수 있습니다.  

    ![개별 사용자 액세스](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
