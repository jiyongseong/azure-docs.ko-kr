## <a name="setting-up-powershell-for-resource-manager-templates"></a>리소스 관리자 템플릿에 대한 PowerShell 설정
리소스 관리자에서 Azure PowerShell을 사용하려면 먼저 올바른 Windows PowerShell 및 Azure PowerShell 버전이 있어야 합니다.

### <a name="verify-powershell-versions"></a>PowerShell 버전 확인
Windows PowerShell 버전 3.0 또는 4.0이 있는지 확인합니다. Windows PowerShell의 버전을 확인하려면 Windows PowerShell 명령 프롬프트에 다음 명령을 입력합니다.

    $PSVersionTable

다음과 같은 유형의 정보가 제공됩니다.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


**PSVersion** 값이 3.0 또는 4.0인지 확인합니다. 그렇지 않으면 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)을 참조하세요.

### <a name="set-your-azure-account-and-subscription"></a>Azure 계정 및 구독 설정
Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

Azure PowerShell 명령 프롬프트를 열고 다음 명령으로 Azure에 로그온합니다.

    Connect-AzureRmAccount

여러 Azure 구독이 있는 경우 다음 명령을 사용하여 Azure 구독을 나열할 수 있습니다.

    Get-AzureRmSubscription

다음과 같은 유형의 정보가 제공됩니다.

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 현재 Azure 구독을 설정할 수 있습니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Azure 구독 및 계정에 대한 자세한 내용은 [방법: 구독에 연결](/powershell/azureps-cmdlets-docs#step-3-connect)을 참조하세요.

