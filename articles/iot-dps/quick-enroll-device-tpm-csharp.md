---
title: C#을 사용하여 Azure Device Provisioning Service에 TPM 장치 등록 | Microsoft Docs
description: Azure 빠른 시작 - C# 서비스 SDK를 사용하여 Azure IoT Hub Device Provisioning Service에 TPM 장치 등록
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 01/16/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c9492d47461e95c7baa28f82b8a95fd7e370ac16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>C# 서비스 SDK를 사용하여 IoT Hub Device Provisioning Service에 TPM 장치 등록

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


다음 단계에서는 [C# 서비스 SDK](https://github.com/Azure/azure-iot-sdk-csharp) 및 샘플 C# .NET Core 응용 프로그램을 사용하여 Azure IoT Hub Device Provisioning Service에서 TPM 장치에 대한 개별 등록을 프로그래밍 방식으로 만드는 방법을 보여줍니다. 필요에 따라 이 개별 등록 항목을 사용하여 시뮬레이션된 TPM 장치를 프로비전 서비스에 등록할 수 있습니다. 이러한 단계는 Windows 및 Linux 컴퓨터 모두에서 작동하지만, 이 문서에서는 Windows 개발 컴퓨터를 사용합니다.

## <a name="prepare-the-development-environment"></a>개발 환경 준비

1. 컴퓨터에 [Visual Studio 2017](https://www.visualstudio.com/vs/)을 설치했는지 확인합니다. 
2. 컴퓨터에 [.Net Core SDK](https://www.microsoft.com/net/download/windows)가 설치되어 있는지 확인합니다. 
3. 계속 진행하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다.
4. (선택 사항)이 빠른 시작의 끝에서 시뮬레이션된 장치를 등록하려면, 장치에 대한 인증 키를 가져오는 단계까지 [C# 장치 SDK를 사용하여 시뮬레이션된 TPM 장치 만들기 및 프로비전](quick-create-simulated-device-tpm-csharp.md)의 단계를 수행합니다. 이 빠른 시작에서 나중에 사용해야 하는 인증 키, 등록 ID 및 필요에 따라 장치 ID를 다운로드합니다. **Azure Portal을 사용하여 개별 등록을 만드는 단계는 수행하지 마세요.**

## <a name="get-the-connection-string-for-your-provisioning-service"></a>프로비전 서비스에 대한 연결 문자열 가져오기

이 빠른 시작의 샘플에서는 프로비전 서비스에 대한 연결 문자열이 필요합니다.
1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다. 
2. **공유 액세스 정책**을 클릭한 다음, 해당 속성을 열기 위해 사용하려는 액세스 정책을 클릭합니다. **액세스 정책** 창에서 기본 키 연결 문자열을 복사하고 적어 둡니다. 

    ![포털에서 프로비전 서비스 연결 문자열 가져오기](media/quick-enroll-device-tpm-csharp/get-service-connection-string.png)

## <a name="create-the-individual-enrollment-sample"></a>개별 등록 샘플 만들기 

이 섹션의 단계에서는 TPM 장치의 개별 등록을 프로비전 서비스에 추가하는 .NET Core 콘솔 앱을 만드는 방법을 보여줍니다. 일부를 수정하여 개별 등록을 추가할 [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) 콘솔 앱을 만들기 위해 다음과 같은 단계를 수행할 수 있습니다. IoT Core를 사용하여 개발하는 방법에 대해 자세히 알아보려면 [Windows IoT Core 개발자 설명서](https://docs.microsoft.com/windows/iot-core/)를 참조하세요.
1. Visual Studio에서 **콘솔 앱(.NET Core)** 프로젝트 템플릿을 사용하여 Visual C# .NET Core 콘솔 앱 프로젝트를 새 솔루션에 추가합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 이름을 **CreateTpmEnrollment**로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트](media//quick-enroll-device-tpm-csharp/create-app.png)

2. 솔루션 탐색기에서 **CreateTpmEnrollment** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고, **Microsoft.Azure.Devices.Provisioning.Service**를 검색하고, **설치**를 선택하여 **Microsoft.Azure.Devices.Provisioning.Service** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 프로비전 서비스 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 패키지 및 해당 종속성에 대한 참조를 다운로드하고, 설치하고, 추가합니다.

    ![NuGet 패키지 관리자 창](media//quick-enroll-device-tpm-csharp/add-nuget.png)

4. **Program.cs** 파일 맨 위에서 다른 `using` 문 뒤에 다음 `using` 문을 추가합니다.
   
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```
    
5. **Program** 클래스에 다음 필드를 추가합니다.  
   - **ProvisioningConnectionString** 자리 표시자 값을 등록을 만들려는 프로비전 서비스의 연결 문자열로 바꿉니다.
   - 필요에 따라 등록 ID, 인증 키, 장치 ID 및 프로비전 상태를 변경할 수 있습니다. 
   - 시뮬레이션된 장치를 프로비전하기 위해 [C# 장치 SDK를 사용하여 시뮬레이션된 TPM 장치 만들기 및 프로비전](quick-create-simulated-device-tpm-csharp.md)에서 이 빠른 시작을 사용하는 경우 빠른 시작 인증 키 및 등록 ID를 해당 빠른 시작에서 다운로드한 값으로 바꿉니다. 장치 ID를 해당 빠른 시작에서 제안한 값으로 바꾸거나, 고유한 값을 사용하거나, 이 샘플의 기본값을 사용할 수 있습니다.
        
   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```
    
6. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 개별 등록 항목을 만든 다음, **ProvisioningServiceClient**에서 **CreateOrUpdateIndividualEnrollmentAsync** 메서드를 호출하여 프로비전 서비스에 개별 등록을 추가합니다.
   
   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```
       
7. 마지막으로 **Main** 메서드의 본문을 다음 줄로 바꿉니다.
   
   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```
        
8. 솔루션을 빌드하십시오.

## <a name="run-the-individual-enrollment-sample"></a>개별 등록 샘플 실행
  
1. Visual Studio에서 샘플을 실행하여 TPM 장치에 대한 개별 등록 키를 만듭니다.
 
2. 성공적으로 만들어지면 명령 창에 새 개별 등록에 대한 속성이 표시됩니다.

    ![명령 출력의 등록 속성](media/quick-enroll-device-tpm-csharp/output.png)

3. 개별 등록을 만들었는지 확인하려면 Azure Portal의 Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택한 다음, **개별 등록** 탭을 선택합니다. 샘플에 사용된 등록 ID에 해당하는 새 등록 항목이 표시됩니다. 항목을 클릭하여 항목에 대한 인증 키 및 다른 속성을 확인합니다.

    ![포털의 등록 속성](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal.png)
 
4. (선택 사항)[C# 장치 SDK를 사용하여 시뮬레이션된 TPM 장치 만들기 및 프로비전](quick-create-simulated-device-tpm-csharp.md) 빠른 시작의 단계를 수행한 경우 해당 빠른 시작의 나머지 단계를 계속하여 시뮬레이션된 장치를 등록할 수 있습니다. Azure Portal을 사용하여 개별 등록을 만드는 단계를 건너뛰어야 합니다.

## <a name="clean-up-resources"></a>리소스 정리
C# 서비스 샘플을 탐색하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 C# 샘플 출력 창을 닫습니다.
2. Azure Portal에서 Device Provisioning Service로 이동하고, **등록 관리**를 클릭한 다음, **개별 등록** 탭을 선택합니다. 이 빠른 시작을 사용하여 만든 등록 항목에 대한 *등록 ID*를 선택하고, 블레이드 위쪽의 **삭제** 단추를 클릭합니다. 
3. [C# 장치 SDK를 사용하여 시뮬레이션된 TPM 장치 만들기 및 프로비전](quick-create-simulated-device-tpm-csharp.md) 빠른 시작의 단계를 수행하여 시뮬레이션된 TPM 장치를 만든 경우 다음을 수행합니다. 

    1. TPM 시뮬레이터 창 및 시뮬레이션된 장치에 대한 샘플 출력 창을 닫습니다.
    2. Azure Portal에서 장치가 프로비전된 IoT Hub로 이동합니다. **탐색기**의 왼쪽 메뉴에서 **IoT 장치**를 클릭하고, 장치 옆의 확인란을 선택하고, 창의 위쪽에서 **삭제**를 클릭합니다.
 
## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 TPM 장치에 대한 개별 등록 항목을 프로그래밍 방식으로 만들고, 필요에 따라 컴퓨터에 시뮬레이션된 TPM 장치를 만들고, Azure IoT Hub Device Provisioning 서비스를 사용하여 IoT Hub에 이 장치를 프로비전했습니다. 장치 프로비전에 대해 자세히 알아보려면 Azure Portal에서 Device Provisioning Service 설치에 대한 자습서를 살펴보세요. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning 서비스 자습서](./tutorial-set-up-cloud.md)

