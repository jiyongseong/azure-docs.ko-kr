---
title: PostgreSQL용 Azure 데이터베이스에서 SSL 연결 구성
description: SSL 연결을 올바르게 사용하기 위해 PostgreSQL용 Azure 데이터베이스 및 연결된 응용 프로그램을 구성하는 방법에 대한 지침 및 정보
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.custom: ''
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f3efb99ddb47f167a0d9cbef064890e817a18841
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 SSL 연결 구성
PostgreSQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용해서 PostgreSQL 서비스에 클라이언트 응용 프로그램을 연결하는 것을 선호합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간 SSL 연결을 적용하면 서버와 응용 프로그램 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 SSL 연결을 요구하도록 구성됩니다. 필요에 따라 클라이언트 응용 프로그램이 SSL 연결을 지원하지 않을 경우 데이터베이스 서비스에 연결하기 위해 SSL을 요구하지 않도록 설정할 수 있습니다. 

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용
Azure Portal 및 CLI를 통해 프로비전된 모든 MySQL용 Azure 데이터베이스 서버의 경우 SSL 연결 적용이 기본적으로 활성화됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="configure-enforcement-of-ssl"></a>SSL 적용 구성
필요에 따라 SSL 연결 적용을 사용하지 않도록 설정할 수 있습니다. Microsoft Azure는 항상 향상된 보안을 위해 **SSL 연결 적용** 설정을 사용하는 것을 권장합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용
PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장**을 클릭합니다. 

![연결 보안 - SSL 적용 사용 안 함](./media/concepts-ssl-connection-security/1-disable-ssl.png)

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용
Azure CLI에서 `Enabled` 또는 `Disabled` 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>응용 프로그램 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.
Drupal 및 Django와 같은 데이터베이스 서비스용 PostgreSQL을 사용하는 많은 일반적인 응용 프로그램 프레임워크는 기본적으로 설치하는 동안 SSL을 사용하도록 설정하지 않습니다. SSL 연결의 사용 설정은 설치 후 또는 응용 프로그램 특정 CLI 명령을 통해 수행되어야 합니다. PostgreSQL 서버에 SSL 연결이 적용되어 있고 연결된 응용 프로그램이 올바르게 구성되어 있지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 연결을 위해 인증서 확인이 필요한 응용 프로그램
경우에 따라 안전한 연결을 위해 응용 프로그램에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. 다음 단계를 참조하여 .cer 파일을 얻고, 인증서를 디코딩한 후 응용 프로그램에 바인딩하세요.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>CA(인증 기관)에서 인증서 파일 다운로드 
PostgreSQL용 Azure 데이터베이스 서버와 함께 SSL을 통해 통신하는 데 필요한 인증서는 [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)에 있습니다. 인증서 파일을 로컬로 다운로드합니다.

### <a name="download-and-install-openssl-on-your-machine"></a>컴퓨터에 OpenSSL 다운로드 및 설치 
응용 프로그램을 데이터베이스 서버에 안전하게 연결하기 위해 필요한 인증서 파일을 디코딩하려면 OpenSSL을 로컬 컴퓨터에 설치해야 합니다.

#### <a name="for-linux-os-x-or-unix"></a>Linux, OS X 또는 Unix
OpenSSL 라이브러리는 [OpenSSL Software Foundation](http://www.openssl.org)에서 직접 소스 코드로 제공됩니다. 다음 지침에서는 Linux PC에 OpenSSL을 설치하는 데 필요한 단계를 안내합니다. 이 문서에서는 Ubuntu 12.04 이상에서 작동하는 것으로 알려진 명령을 사용합니다.

터미널 세션을 열고 OpenSSL을 다운로드합니다.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
다운로드한 패키지에서 파일 압축을 풉니다.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
파일 압축을 푼 디렉터리를 입력합니다. 기본적으로 다음과 같아야 합니다.

```bash
cd openssl-1.1.0e
```
다음 명령을 실행하여 OpenSSL을 구성합니다. /usr/local/openssl과 다른 폴더의 파일을 원하면 다음을 적절하게 변경합니다.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
이제 OpenSSL이 올바르게 구성되었으므로 인증서를 변환하기 위해 컴파일해야 합니다. 컴파일하려면 다음 명령을 실행합니다.

```bash
make
```
컴파일이 완료되면 다음 명령을 실행하여 OpenSSL을 실행 가능하도록 설치할 준비가 되었습니다.
```bash
make install
```
시스템에 OpenSSL이 성공적으로 설치되었는지 확인하려면 다음 명령을 실행하여 동일한 출력이 나타나는지 확인합니다.

```bash
/usr/local/openssl/bin/openssl version
```
성공적이라면 다음과 같은 메시지가 표시되어야 합니다.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Windows의 경우
다음 방법을 수행하면 Windows PC에서 OpenSSL을 설치할 수 있습니다.
1. **(권장)** Window 10 이상에서 기본 제공되는 Windows용 Bash 기능을 사용하면 OpenSSL이 기본적으로 설치됩니다. Windows 10의 Windows용 Bash 기능을 사용하도록 설정하는 방법에 대한 지침은 [여기](https://msdn.microsoft.com/commandline/wsl/install_guide)에 나와 있습니다.
2. 커뮤니티에서 제공되는 Win32/64 응용 프로그램을 다운로드하여, OpenSSL Software Foundation에서 특정 Windows 설치 관리자를 제공하거나 보증하지는 않지만 사용 가능한 설치 관리자의 목록은 [여기](https://wiki.openssl.org/index.php/Binaries)에서 확인할 수 있습니다.

### <a name="decode-your-certificate-file"></a>인증서 파일 디코딩
다운로드한 루트 CA 파일은 암호화된 형식입니다. OpenSSL을 사용하여 인증서 파일을 디코딩합니다. 이렇게 하려면 다음 OpenSSL 명령을 실행합니다.

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>SSL 인증서 인증을 사용하여 PostgreSQL용 Azure 데이터베이스에 연결
이제 인증서를 성공적으로 디코딩했으므로 SSL을 통해 데이터베이스 서버에 안전하게 연결할 수 있습니다. 서버 인증서 확인을 허용하려면 사용자의 홈 디렉터리에 있는 파일 ~/.postgresql/root.crt에 인증서를 배치해야 합니다. (Microsoft Windows에서 이 파일의 이름은 %APPDATA%\postgresql\root.crt입니다.) 다음은 PostgreSQL용 Azure 데이터베이스에 연결하기 위한 지침입니다.

#### <a name="using-psql-command-line-utility"></a>psql 명령줄 유틸리티 사용
다음 예제에서는 psql 명령줄 유틸리티를 사용하여 PostgreSQL 서버에 성공적으로 연결하는 방법을 보여 줍니다. 만든 `root.crt` 파일과 `sslmode=verify-ca` 또는 `sslmode=verify-full` 옵션을 사용합니다.

PostgreSQL 명령줄 인터페이스를 사용하여 다음 명령을 실행합니다.
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
성공하면 다음과 같은 출력이 표시됩니다.
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>pgAdmin GUI 도구 사용
SSL을 통해 안전하게 연결하도록 pgAdmin 4를 구성하려면 `SSL mode = Verify-CA` 또는 `SSL mode = Verify-Full`을 다음과 같이 설정해야 합니다.

![pgAdmin - 연결 - SSL 모드 Require 스크린샷](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL을 위한 연결 라이브러리](concepts-connection-libraries.md)에 따라 다양한 응용 프로그램 연결 옵션 검토
