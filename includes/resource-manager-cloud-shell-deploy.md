## <a name="deploy-template-from-cloud-shell"></a>Cloud Shell에서 템플릿 배포

[Cloud Shell](../articles/cloud-shell/overview.md)을 사용하여 템플릿을 배포할 수 있습니다. 그러나 먼저 Cloud Shell용 저장소 계정에 템플릿을 로드해야 합니다. Cloud Shell을 사용해 본 적이 없다면 [Azure Cloud Shell 개요](../articles/cloud-shell/overview.md)에서 Cloud Shell 설정 방법을 참조하세요.

1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.

1. Cloud Shell 리소스 그룹을 선택합니다. 이름 패턴은 `cloud-shell-storage-<region>`입니다.

   ![리소스 그룹 선택](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Cloud Shell용 저장소 계정을 선택합니다.

   ![저장소 계정 선택](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. **Blob**을 선택합니다.

   ![Blob 선택](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. **+컨테이너**를 선택합니다.

   ![컨테이너 추가](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. 컨테이너에 이름과 액세스 수준을 지정합니다. 이 문서의 샘플 템플릿에는 중요한 정보가 없으므로 익명 읽기 액세스를 허용합니다. **확인**을 선택합니다.

   ![컨테이너 값 제공](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. 만든 컨테이너를 선택합니다.

   ![새 컨테이너 선택](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. **업로드**를 선택합니다.

   ![Blob 업로드](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. 템플릿을 찾아서 업로드합니다.

   ![파일 업로드](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. 업로드된 후 템플릿을 선택합니다.

   ![새 템플릿 선택](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. URL을 복사합니다.

   ![URL 복사](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. 프롬프트를 엽니다.

   ![Cloud Shell 열기](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
