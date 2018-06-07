## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage 계정을 IoT Hub에 연결

시뮬레이션된 장치 앱에서 파일을 Blob에 업로드하므로 IoT Hub에 연결된 [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) 계정이 있어야 합니다. IoT Hub와 Azure Storage 계정을 연결하면 IoT Hub는 SAS URI를 생성합니다. 장치는 Blob 컨테이너에 안전하게 파일을 업로드하는 데 SAS URI를 사용할 수 있습니다. IoT Hub 서비스 및 장치 SDK에서는 SAS URI를 생성하는 프로세스를 조정하여 파일을 업로드하는 데 장치를 사용할 수 있도록 합니다.

[Azure Portal을 사용하여 파일 업로드 구성](../articles/iot-hub/iot-hub-configure-file-upload.md)의 지침에 따라 Azure Storage 계정을 IoT Hub에 연결합니다. Blob 컨테이너가 IoT Hub와 연결되고 파일 알림을 사용하는지 확인합니다.

![포털에서 파일 알림 사용](media/iot-hub-associate-storage/enable-file-notifications.png)