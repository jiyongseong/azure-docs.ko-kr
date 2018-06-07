
## <a name="start-your-powershell-session"></a>PowerShell 세션 시작
우선 최신 [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) 을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

> [!NOTE]
> 이 항목의 예제에서는 [Azure Resource Manager 배포 모델](../articles/azure-resource-manager/resource-group-overview.md)을 사용하므로 예제에 [Azure Resource Manager cmdlet](http://msdn.microsoft.com/library/azure/mt125356.aspx)이 사용됩니다. 
> 
> 

[**Connect-AzureRmAccount**](http://msdn.microsoft.com/library/mt619267.aspx) cmdlet을 실행하면 자격 증명을 입력할 수 있는 로그인 화면이 나타납니다. Azure 포털에 로그인하는 데 사용하는 동일한 자격 증명을 사용합니다.

    Connect-AzureRmAccount

여러 구독이 있는 경우 [**Set-AzureRmContext**](http://msdn.microsoft.com/library/mt619263.aspx) cmdlet을 사용하여 PowerShell 세션이 사용해야 하는 구독을 선택합니다. 현재 PowerShell 세션이 사용 중인 구독을 보려면 [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx)를 실행합니다. 모든 구독을 보려면 [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx)을 실행합니다.

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

