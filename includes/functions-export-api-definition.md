## <a name="export-an-api-definition"></a>API 정의 내보내기
[함수에 대한 OpenAPI 정의 만들기](../articles/azure-functions/functions-openapi-definition.md)를 통해 함수에 대한 OpenAPI 정의를 만들었습니다. 이 프로세스의 다음 단계는 PowerApps 및 Microsoft Flow가 해당 사용자 지정 API에서 사용할 수 있도록 API 정의를 내보내는 것입니다.

> [!IMPORTANT]
> PowerApps 및 Microsoft Flow 테넌트에 사용하는 것과 동일한 자격 증명을 사용하여 Azure에 로그인해야 합니다. 이렇게 하면 Azure에서 사용자 지정 API를 만들고 PowerApps 및 Microsoft Flow 둘 다에서 사용하도록 지정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱 이름(예: **function-demo-energy**) > **플랫폼 기능** > **API 정의**를 차례로 클릭합니다.

    ![API 정의](media/functions-export-api-definition/api-definition.png)

1. **PowerApps + Microsoft Flow로 내보내기**를 클릭합니다.

    ![API 정의 원본](media/functions-export-api-definition/export-api-1.png)

1. 오른쪽 창에서 표에 지정된 것처럼 설정을 사용합니다.

    |설정|설명|
    |--------|------------|
    |**내보내기 모드**|**기본**을 선택하여 사용자 지정 API를 자동으로 생성합니다. **수동**을 선택하면 API 정의가 내보내지지만 PowerApps 및 Microsoft Flow에 수동으로 가져와야 합니다. 자세한 내용은 [PowerApps 및 Microsoft Flow로 내보내기](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md)를 참조하세요.|
    |**환경**|사용자 지정 API를 저장해야 하는 환경을 선택합니다. 자세한 내용은 [환경 개요(PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) 또는 [환경 개요(Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/)를 참조하세요.|
    |**사용자 지정 API 이름**|이름(예: `Turbine Repair`)을 입력합니다.|
    |**API 키 이름**|앱 및 흐름 작성자가 사용자 지정 API UI에서 보게 되는 이름입니다. 이 예제에서 유용한 정보가 포함되어 있습니다.|
 
    ![PowerApps 및 Microsoft Flow로 내보내기](media/functions-export-api-definition/export-api-2.png)

1. **확인**을 클릭합니다. 이제 사용자 지정 API가 빌드되고 지정한 환경에 추가됩니다.