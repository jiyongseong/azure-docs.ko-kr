## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell에 로컬 파일 전송
`clouddrive` 디렉터리가 Azure Portal 저장소 블레이드에 동기화됩니다. 이 블레이드를 사용하여 파일 공유 간에 로컬 파일을 전송합니다. Cloud Shell 내의 파일을 업데이트하면 블레이드를 새로 고칠 때 파일 저장소 GUI에 반영됩니다.

### <a name="download-files"></a>파일 다운로드

![로컬 파일 목록](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Azure Portal에서 마운트된 파일 공유로 이동합니다.
2. 대상 파일을 선택합니다.
3. **다운로드** 단추를 선택합니다.

### <a name="upload-files"></a>파일 업로드

![업로드할 로컬 파일](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. 마운트된 파일 공유로 이동합니다.
2. **업데이트** 단추를 선택합니다.
3. 업로드할 파일을 하나 이상 선택합니다.
4. 업로드를 확인합니다.

이제 Cloud Shell의 `clouddrive` 디렉터리에서 액세스할 수 있는 파일이 표시됩니다.