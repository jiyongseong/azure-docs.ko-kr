# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell에서 파일 유지
Cloud Shell은 Azure File 저장소를 활용하여 세션 간에 파일을 유지합니다.

## <a name="set-up-a-clouddrive-file-share"></a>Clouddrive 파일 공유 설정
처음 시작 시 Cloud Shell은 세션 간에 파일을 유지하기 위해 새 또는 기존 파일 공유를 연결하도록 요구합니다.

> [!NOTE]
> Bash 및 PowerShell은 동일한 파일 공유를 공유합니다. 하나의 파일 공유는 Cloud Shell에서 자동 탑재와 연결될 수 있습니다.

### <a name="create-new-storage"></a>새 저장소 만들기

기본 설정을 사용하고 구독만 선택하면 Cloud Shell은 가장 가까운 지원되는 지역에서 사용자를 대신에 3개 리소스를 만듭니다.
* 리소스 그룹: `cloud-shell-storage-<region>`
* Storage 계정: `cs<uniqueGuid>`
* 파일 공유: `cs-<user>-<domain>-com-<uniqueGuid>`

![구독 설정](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

파일 공유는 `$Home` 디렉터리에서 `clouddrive`로 마운트됩니다. 이것은 일회성 작업이며 파일 공유는 후속 세션에서 자동으로 마운트됩니다. 

> [!NOTE]
> 보안을 위해 각 사용자는 자신의 저장소를 프로비전해야 합니다.  RBAC(역할 기반 액세스 제어)의 경우 사용자에게는 기여자 액세스 권한 이상이 있어야 합니다.

Bash에서 파일 공유는 `$Home` 디렉터리에서 데이터를 자동으로 유지하기 위해 만든 5GB 이미지를 포함합니다. 

### <a name="use-existing-resources"></a>기존 리소스 사용

고급 옵션을 사용하여 기존 리소스를 연결할 수 있습니다. 저장소 설정 프롬프트가 나타나면 **고급 옵션 표시**를 선택하여 추가 옵션을 봅니다.  할당된 Cloud Shell 지역, 로컬 중복 저장소 및 지역 중복 구성 저장소 계정에 대해 드롭다운 메뉴가 필터링됩니다.

Bash에서 기존 파일 공유는 `$Home` 디렉터리를 유지하기 위해 만든 5GB 이미지를 수신합니다.

![리소스 그룹 설정](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure 리소스 정책으로 리소스 만들기 제한
Cloud Shell에서 생성된 Storage 계정에 `ms-resource-usage:azure-cloud-shell` 태그가 지정됩니다. 사용자가 Cloud Shell에서 저장소 계정을 만드는 것을 허용하지 않으려면 이 특정 태그로 트리거되는 [태그에 대한 Azure 리소스 정책](../articles/azure-policy/json-samples.md)을 만듭니다.

## <a name="supported-storage-regions"></a>지원되는 저장소 지역
연결된 Azure Storage 계정은 사용자가 마운트하는 Cloud Shell 컴퓨터와 동일한 지역에 있어야 합니다.

할당된 지역을 찾으려면 다음과 같은 작업을 수행할 수 있습니다.
* "고급 저장소 설정" 대화 상자에서 메모 보기
* 사용자를 위해 만든 저장소 계정의 이름 참조(예: `cloud-shell-storage-westus`)
* `env` 실행 및 `ACC_LOCATION` 변수 찾기

Cloud Shell 컴퓨터는 아래 지역에 위치합니다.
|영역|지역|
|---|---|
|아메리카|미국 동부, 미국 중남부, 미국 서부|
|유럽|북유럽, 서유럽|
|아시아 태평양|인도 중부, 동남 아시아|

