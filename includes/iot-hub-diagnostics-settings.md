### <a name="enable-logging-with-diagnostics-settings"></a>진단 설정에 대한 로깅 사용

1. [Azure Portal][lnk-portal]에 로그인하고 IoT Hub로 이동합니다.
1. **진단 설정**을 선택합니다.
1. **진단 켜기**를 선택합니다.

   ![진단 켜기][1]

1. 진단 설정에 이름을 지정합니다.
1. 로그를 보낼 위치를 선택합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.
   * 저장소 계정에 보관
   * 이벤트 허브로 스트림
   * Log Analytics에 보내기
1. 모니터링하려는 작업을 선택하고 해당 작업에 대한 로그를 사용하도록 설정합니다. 진단 설정이 보고할 수 있는 작업은 같습니다.
   * 연결
   * 장치 원격 분석
   * 클라우드-장치 메시지
   * 장치 ID 작업
   * 파일 업로드
   * 메시지 라우팅
   * 클라우드-장치 쌍 작업
   * 장치-클라우드 쌍 작업
   * 쌍 작업
   * 업무 작업
   * 직접 메서드  
1. 새 설정을 저장합니다. 

PowerShell 사용하여 진단 설정을 켜려면 다음 코드를 사용합니다.

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 설정** 블레이드에 로그가 나타납니다. 진단을 구성하는 방법에 대한 자세한 내용은 [Azure 리소스의 로그 데이터 수집 및 사용][lnk-diagnostics-settings]을 참조하세요.

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
