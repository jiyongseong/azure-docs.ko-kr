### <a name="prerequisites"></a>필수 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [Azure Blob Storage 계정](../articles/storage/common/storage-create-storage-account.md)은 저장소 계정 이름 및 해당 선택키를 포함합니다. 이 정보는 Azure 포털에서 저장소 계정의 속성에 나열됩니다. [Azure Storage](../articles/storage/common/storage-introduction.md)에 대해 자세히 읽어봅니다.

논리 앱에서 Azure Blob 저장소 계정을 사용하기 전에 Azure Blob 저장소 계정에 연결합니다. Azure Portal의 논리 앱 내에서 이 작업을 쉽게 수행할 수 있습니다.  

다음 단계를 사용하여 Azure Blob 저장소 계정에 연결합니다.  

1. 논리 앱을 만듭니다. 논리 앱 디자이너에서 트리거를 추가하고 동작을 추가합니다. 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택한 다음 검색 상자에 "blob"을 입력합니다. 다음 동작 중 하나를 선택합니다.  
   
    ![Azure Blob 저장소 연결 만들기 단계](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. 이전에 Azure Storage에 대한 연결을 만들지 않은 경우 연결 세부 정보를 지정하라는 메시지가 표시됩니다.   
   
    ![Azure Blob 저장소 연결 만들기 단계](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. 저장소 계정 세부 정보를 입력합니다. 별표가 있는 속성은 필수 사항입니다.
   
   | 속성 | 세부 정보 |
   | --- | --- |
   | 연결 이름 * |연결의 이름을 입력합니다. |
   | Azure 저장소 계정 이름 * |저장소 계정 이름을 입력하세요. 저장소 계정 이름은 Azure 포털의 저장소 속성에 표시됩니다. |
   | Azure 저장소 계정 액세스 키 * |저장소 계정 키를 입력합니다. 액세스 키는 Azure 포털의 저장소 속성에 표시됩니다. |
   
    이러한 자격 증명을 사용하여 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. 
4. **만들기**를 선택합니다.
5. 연결이 만들어졌는지 확인합니다. 이제 논리 앱의 다른 단계를 진행합니다. 
   
    ![Azure Blob 저장소 연결 만들기 단계](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

