## <a name="configure-your-application-to-access-azure-storage"></a>Azure 저장소에 액세스하도록 응용 프로그램 구성
저장소 서비스에 액세스하려면 응용 프로그램을 인증하는 두 가지 방법이 있습니다.

* 공유 키: 테스트 목적으로만 공유 키 사용
* 공유 액세스 서명(SAS): 프로덕션 응용 프로그램에 대해 SAS 사용

### <a name="shared-key"></a>공유 키
공유 키 인증은 응용 프로그램이 계정 이름과 계정 키를 사용하여 저장소 서비스에 액세스하는 것을 의미합니다. 이 시작 설명서에서는 이 라이브러리를 사용하는 방법을 신속하게 표시하는 용도로 공유 키 인증을 사용합니다.

> [!WARNING] 
> **테스트 목적으로만 공유 키 인증을 사용합니다.** 연결된 저장소 계정에 대한 전체 읽기/쓰기 액세스를 부여하는 계정 이름 및 계정 키가 앱을 다운로드한 모든 사람에게 배포됩니다. 이 방법은 신뢰할 수 없는 클라이언트가 키를 손상시킬 수 있는 위험이 있으므로 좋은 방법은 **아닙니다** .
> 
> 

공유 키 인증을 사용하면 [연결 문자열](../articles/storage/common/storage-configure-connection-string.md)이 만들어집니다. 연결 문자열은 다음과 같이 구성됩니다.  

* **DefaultEndpointsProtocol** - HTTP 또는 HTTPS를 선택할 수 있습니다. 그러나 HTTPS를 사용하는 것이 좋습니다.
* **계정 이름** - 저장소 계정의 이름입니다.
* **계정 키** - [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동하고 **키** 아이콘을 클릭하여 이 정보를 찾습니다.
* (선택 사항) **EndpointSuffix** - Azure 중국 또는 Azure 거버넌스와 같이 다른 끝점 접미사가 붙은 지역의 저장소 서비스에 사용됩니다.

공유 키 인증을 사용하는 연결 문자열의 예는 다음과 같습니다.

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>공유 액세스 서명(SAS)
모바일 응용 프로그램에서 Azure Storage 서비스에 대해 클라이언트의 요청을 인증하는 데 권장되는 방법은 공유 액세스 서명(SAS)을 사용하는 것입니다. SAS를 사용하면 지정된 사용 권한 집합과 함께 지정된 기간 동안 리소스에 대한 클라이언트 액세스를 부여할 수 있습니다.
저장소 계정 소유자는 모바일 클라이언트에서 사용할 SAS를 생성해야 합니다. SAS를 생성하려면 클라이언트로 배포되는 SAS를 생성하는 별도의 서비스를 작성할 수 있습니다. 테스트를 위해 [Microsoft Azure Storage Explorer](http://storageexplorer.com) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 SAS를 생성합니다. SAS를 만들 때, SAS가 유효한 시간 간격 및 SAS가 클라이언트에 부여하는 사용 권한을 지정할 수 있습니다.

다음 예제에서는 Microsoft Azure Storage Explorer를 사용하여 SAS를 생성하는 방법을 보여 줍니다.

1. 아직 설치하지 않은 경우 [Microsoft Azure Storage Explorer를 설치](http://storageexplorer.com)
2. 구독에 연결합니다.
3. 저장소 계정에 클릭하고 왼쪽 아래에서 "동작" 탭을 클릭합니다. "공유 액세스 서명 가져오기"를 클릭하여 SAS에 대한 "연결 문자열"을 생성합니다.
4. 다음은 저장소 계정의 blob 서비스에 대한 서비스, 컨테이너 및 개체 수준에서 읽기 및 쓰기 권한을 부여하는 SAS 연결 문자열 예제입니다.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

위에 표시된 것처럼 SAS을 사용하는 경우, 응용 프로그램에서 계정 키가 노출되지 않습니다. [공유 액세스 서명: SAS 모델 이해](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)를 확인하여 SAS 및 SAS 사용 모범 사례에 대해 자세히 알아볼 수 있습니다.

