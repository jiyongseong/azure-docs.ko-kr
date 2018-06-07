---
title: Azure Storage 탐색기 문제 해결 가이드 | Microsoft Docs
description: Azure의 두 가지 디버깅 기능 개요
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 531ca6d781ae62aacd85dce600e3ea8b46ccf360
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777080"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage 탐색기 문제 해결 가이드

Microsoft Azure Storage 탐색기는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 작업할 수 있게 하는 독립 실행형 앱입니다. 앱은 Azure, National Clouds 및 Azure Stack에서 호스트되는 저장소 계정에 연결할 수 있습니다.

이 가이드에는 Storage 탐색기에 나타나는 일반적인 문제에 대한 솔루션이 요약되어 있습니다.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>오류: 인증서 체인의 자체 서명된 인증서(및 유사 오류)

인증서 오류는 다음과 같은 두 상황 중 하나에서 발생합니다.

1. 앱이 "투명 프록시"를 통해 연결됩니다. 즉, 회사 서버와 같은 서버가 HTTPS 트래픽을 가로 채고 해독한 다음 자체 서명된 인증서를 사용하여 암호화하는 것을 의미합니다.
2. 수신한 HTTPS 메시지에 자체 서명된 SSL 인증서를 삽입하는 응용 프로그램을 실행하고 있습니다. 인증서 삽입을 수행하는 응용 프로그램에는 바이러스 백신, 네트워크 트래픽 검사 소프트웨어 등이 포함됩니다.

Storage 탐색기가 자체 서명된 또는 신뢰할 수 없는 인증서를 발견하면, 수신된 HTTPS 메시지가 변경된 것인지 더 이상은 알 수 없습니다. 자체 서명된 인증서의 복사본이 있으면 다음 단계를 통해 Storage 탐색기가 이를 신뢰하게 할 수 있습니다.

1. 인증서의 Base-64 인코딩 X.509(.cer) 사본 가져오기
2. **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음, 파일 선택기를 사용하여 해당 .cer 파일을 찾고 선택하고 엽니다.

이 문제는 여러 인증서(루트 및 중간)의 결과로 발생할 수도 있습니다. 오류를 해결하려면 인증서를 모두 추가해야 합니다.

인증서의 출처를 확신하지 못할 경우 다음 단계를 통해 확인할 수 있습니다.

1. OpenSSL을 설치합니다.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html)(라이트 버전이면 충분함)
    * Mac 및 Linux: 운영 체제에 포함되어야 합니다.
2. OpenSSL을 실행합니다.

    * Windows: 설치 디렉터리를 열고 **/bin/** 을 클릭한 다음 **openssl.exe**를 두 번 클릭합니다.
    * Mac 및 Linux: 터미널에서 **openssl**을 실행합니다.
3. `s_client -showcerts -connect microsoft.com:443` 실행
4. 자체 서명된 인증서를 찾습니다. 어떤 인증서가 자체 서명된 것인지 확실하지 않은 경우 제목(`("s:")`)과 발급자(`("i:")`)가 같은 위치를 찾습니다.
5. 자체 서명된 인증서를 찾았으면 각 인증서에 대해 **----- BEGIN CERTIFICATE -----** 부터 **----- END CERTIFICATE -----** 까지 모든 줄을 복사하여 새 .cer 파일에 붙여넣습니다.
6. Storage 탐색기를 열고 **편집** > **SSL 인증서** > **인증서 가져오기**를 클릭한 다음 파일 선택기를 사용하여 만든 .cer 파일을 찾고 선택하고 엽니다.

위의 단계를 사용하여 자체 서명된 인증서를 찾을 수 없는 경우 사용자 의견 도구를 통해 당사에 문의하여 도움을 받습니다. 또는 명령줄에서 `--ignore-certificate-errors` 플래그를 사용하여 Storage 탐색기를 시작합니다. 이 플래그로 시작하면 Storage 탐색기가 인증서 오류를 무시하게 됩니다.

## <a name="sign-in-issues"></a>로그인 문제

로그인할 수 없는 경우 다음 문제 해결 방법을 시도해 봅니다.

* macOS에서 "인증 대기 중..." 대화 상자 위에 로그인 창이 나타나지 않으면 [이러한 조치](#Resetting-the-Mac-Keychain)를 시도해 보세요.
* Storage 탐색기 다시 시작
* 인증 창이 비어 있는 경우 1분 이상 기다렸다가 인증 대화 상자를 닫습니다.
* 컴퓨터와 Storage 탐색기에 대해 프록시와 인증서 설정이 올바르게 구성되었는지 확인합니다.
* Windows를 사용 중이며 같은 컴퓨터와 로그인에서 Visual Studio 2017에 대한 액세스 권한이 있는 경우 Visual Studio 2017에 로그인해 봅니다.

이러한 방법으로 문제를 해결하지 못한 경우 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다.

## <a name="unable-to-retrieve-subscriptions"></a>구독을 검색할 수 없음

성공적으로 로그인한 후 구독을 검색할 수 없는 경우 다음 단계에 따라 문제를 해결합니다.

* 해당 계정이 원하는 구독에 액세스할 수 있는지 확인합니다. 사용할 Azure 환경에 대한 포털에 로그인하여 액세스 권한이 있는지 확인할 수 있습니다.
* 올바른 Azure 환경(Azure, Azure China, Azure Germany, Azure US Government 또는 사용자 지정 환경)을 사용하여 로그인했는지 확인합니다.
* 프록시 뒤에 있는 경우 Storage 탐색기 프록시를 제대로 구성했는지 확인합니다.
* 계정을 제거하고 다시 추가해 봅니다.
* Storage 탐색기가 구독을 로드하는 동안 개발자 도구 콘솔(도움말 > 개발자 도구 설정/해제)을 살펴봅니다. 오류 메시지(빨간색 텍스트) 또는 "테넌트에 대해 구독을 로드할 수 없음" 텍스트가 포함된 메시지가 있는지 찾아봅니다. 우려되는 메시지가 있으면 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다.

## <a name="cannot-remove-attached-account-or-storage-resource"></a>연결된 계정 또는 저장소 리소스를 제거할 수 없음

연결된 계정이나 저장소 리소스를 UI를 통해 제거할 수 없는 경우 다음 폴더를 삭제하여 연결된 리소스를 모두 수동으로 삭제할 수 있습니다.

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  위의 폴더를 삭제하기 전에 Storage 탐색기를 닫습니다.

> [!NOTE]
>  SSL 인증서를 가져온 적이 있는 경우 `certs` 디렉터리의 콘텐츠를 백업합니다. 다음에는 백업을 사용하여 SSL 인증서를 다시 가져올 수 있습니다.

## <a name="proxy-issues"></a>프록시 문제

먼저 입력한 다음 정보가 모두 올바른지 확인합니다.

* 프록시 URL 및 포트 번호
* 프록시가 요구하는 경우 사용자 이름과 암호

### <a name="common-solutions"></a>일반적인 솔루션

그래도 문제가 계속되면 다음 문제 해결 방법을 시도해 봅니다.

* 프록시를 사용하지 않고 인터넷에 연결할 수 있으면 프록시 설정을 사용하지 않고 Storage 탐색기가 작동하는지 확인합니다. 이 경우 프록시 설정에 문제가 있을 수 있습니다. 프록시 관리자와 함께 문제를 식별합니다.
* 프록시 서버를 사용하는 다른 응용 프로그램이 예상대로 작동하는지 확인합니다.
* 사용할 Azure 환경에 대한 포털에 로그인하여 확인
* 서비스 끝점에서 응답을 수신할 수 있는지 확인합니다. 끝점 URL 중 하나를 브라우저에 입력합니다. 연결할 수 있으면 InvalidQueryParameterValue 또는 유사한 XML 응답을 수신해야 합니다.
* 다른 사용자가 Storage 탐색기와 프록시 서버를 함께 사용하고 있는 경우 연결할 수 있는지 확인합니다. 연결할 수 있다면 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

### <a name="tools-for-diagnosing-issues"></a>문제를 진단하기 위한 도구

Windows용 Fiddler와 같은 네트워킹 도구가 있으면 다음과 같은 문제를 진단할 수 있습니다.

* 프록시를 통해 작업해야 하는 경우 프록시를 통해 연결하도록 네트워킹 도구를 구성해야 할 수 있습니다.
* 네트워킹 도구에서 사용하는 포트 번호를 확인합니다.
* Storage 탐색기에 로컬 호스트 URL과 네트워킹 도구의 포트 번호를 프록시 설정으로 입력합니다. 올바르게 수행되면 네트워킹 도구가 Storage 탐색기에서 만든 네트워크 요청을 관리 및 서비스 끝점에 기록하기 시작합니다. 예를 들어 브라우저에서 Blob 엔드포인트에 대해 https://cawablobgrs.blob.core.windows.net/을 입력하면 다음과 유사한 응답이 수신됩니다. 이 응답을 통해 리소스에 액세스할 수는 없지만 리소스가 있음을 알 수 있습니다.

![코드 샘플](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>프록시 서버 관리자 문의

프록시 설정이 올바른 경우 프록시 서버 관리자에게 문의해야 할 수도 있습니다.

* 프록시가 Azure 관리 또는 리소스 끝점에 대한 트래픽을 차단하지 않는지 확인합니다.
* 프록시 서버에서 사용하는 인증 프로토콜을 확인합니다. Storage 탐색기는 현재 NTLM 프록시를 지원하지 않습니다.

## <a name="unable-to-retrieve-children-error-message"></a>"하위 항목을 검색할 수 없음" 오류 메시지

프록시를 통해 Azure에 연결된 경우 프록시 설정이 올바른지 확인합니다. 구독 또는 계정 소유자로부터 리소스에 대한 액세스가 부여된 경우 해당 리소스에 대한 읽기 또는 목록 권한이 있는지 확인합니다.

## <a name="issues-with-sas-url"></a>SAS URL 문제
SAS URL을 사용하여 서비스에 연결하고 이 오류가 발생하는 경우:

* URL이 리소스를 읽거나 나열하는 데 필요한 권한을 제공하는지 확인합니다.
* URL이 만료되지 않았는지 확인합니다.
* SAS URL이 액세스 정책을 기반으로 하는 경우 액세스 정책이 철회되지 않았는지 확인합니다.

실수로 잘못된 SAS URL을 사용하여 첨부하고 분리할 수 없는 경우 다음 단계를 따릅니다.
1.  Storage 탐색기를 실행 하는 경우 F12 키를 눌러 개발자 도구 창을 엽니다.
2.  [응용 프로그램] 탭을 클릭한 다음 왼쪽 트리에서 로컬 저장소 > file://을 클릭합니다.
3.  문제가 있는 SAS URI의 서비스 유형과 연결된 키를 찾습니다. 예를 들어 잘못된 SAS URI가 Blob 컨테이너에 대한 것이면 `StorageExplorer_AddStorageServiceSAS_v1_blob`으로 명명된 키를 찾아봅니다.
4.  키의 값은 JSON 배열이어야 합니다. 잘못된 URI와 연결된 개체를 찾아 제거합니다.
5.  Storage 탐색기를 다시 로드하려면 Ctrl+R 키를 누릅니다.

## <a name="linux-dependencies"></a>Linux 종속성

Ubuntu 16.04 이외의 다른 Linux 배포판의 경우 몇 가지 종속성을 수동으로 설치해야 합니다. 일반적으로 다음과 같은 패키지가 필요합니다.
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* 최신 GCC

배포판에 따라 다른 패키지를 설치해야 할 수 있습니다. Storage 탐색기 [릴리스 정보](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)는 일부 배포판에 대한 특정 단계를 포함합니다.

## <a name="resetting-the-mac-keychain"></a>Mac 키 집합 다시 설정
macOS 키 집합은 Storage 탐색기의 인증 라이브러리에서 문제를 유발하는 상태가 될 수 있습니다. 키 집합의 이러한 상태를 해제하려면 다음 조치를 시도해 보세요.
1. Storage 탐색기를 닫습니다.
2. 키 집합을 엽니다(**cmd+space**, keychain 입력, enter 키 누름).
3. "로그인" 키 집합을 선택합니다.
4. 자물쇠 아이콘을 클릭하여 키 집합을 잠급니다(완료되면 자물쇠가 잠긴 모양으로 바뀌며 열려 있는 앱에 따라 몇 초 정도 걸릴 수 있음).

    ![이미지](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage 탐색기를 시작합니다.
6. "서비스 허브가 키 집합에 액세스하려고 합니다"와 같은 팝업 창이 표시됩니다. 그러면 Mac 관리자 계정 암호를 입력하고 **항상 허용**을 클릭합니다(**항상 허용**을 사용할 수 없는 경우 **허용** 클릭).
7. 로그인을 시도합니다.

## <a name="next-steps"></a>다음 단계

어떤 솔루션으로도 문제를 해결하지 못할 경우 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)합니다. 왼쪽 아래 모서리의 "GitHub에 문제 보고" 단추를 사용하면 신속하게 GitHub로 이동할 수 있습니다.

![사용자 의견](./media/storage-explorer-troubleshooting/feedback-button.PNG)
