---
title: "Azure Database for MySQL에 응용 프로그램 연결"
description: "이 문서에는 MySQL용 Azure Database와 연결하는 응용 프로그램에 대한 현재 지원되는 연결 문자열이 나열되어 있습니다(ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python 및 Ruby)."
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e7b200fd1de79f0bca680bdedc34fa376cf07d68
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>MySQL용 Azure Database에 응용 프로그램을 연결하는 방법
이 항목에는 템플릿 및 예제와 함께 MySQL용 Azure Database에서 지원되는 연결 문자열 형식이 나열되어 있습니다. 연결 문자열에 다른 매개 변수 및 설정을 사용할 수도 있습니다.

- 인증서를 가져오려면 [SSL 구성 방법](./howto-configure-ssl.md)을 참조하세요.
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} = 올바른 인증을 위한 사용자 ID 형식입니다.  사용자 ID만 사용하는 경우 인증이 실패합니다.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

이 예제에서 서버 이름은 `mydemoserver`이고, 데이터베이스 이름은 `wpdb`이고, 사용자 이름은 `WPAdmin`이고, 암호는 `mypassword!2`입니다. 결과적으로 연결 문자열은 다음과 같습니다.

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>파이썬
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Azure Portal에서 연결 문자열 세부 정보 가져오기
[Azure Portal](https://portal.azure.com)에서 MySQL용 Azure Database 서버로 이동하고 **연결 문자열**을 클릭하여 사용자 인스턴스에 대한 문자열 목록을 가져옵니다. ![Azure Portal의 연결 문자열 창](./media/howto-connection-strings/connection-strings-on-portal.png)

문자열은 드라이버, 서버 및 다른 데이터베이스 연결 매개 변수와 같은 세부 정보를 제공합니다. 데이터베이스 이름, 암호 등과 같은 고유한 매개 변수를 사용하여 이러한 예제를 수정합니다. 그런 다음, 이 문자열을 사용하여 사용자 코드와 응용 프로그램에서 서버에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 연결 라이브러리에 대한 자세한 내용은 [개념 - 연결 라이브러리](./concepts-connection-libraries.md)를 참조하세요.
