---
title: "웹앱에 대 한 TLS 상호 인증을 구성하는 방법"
description: "TLS에 클라이언트 인증서 인증을 사용 하 여 웹앱을 구성하는 방법에 알아봅니다."
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>웹앱에 대 한 TLS 상호 인증을 구성하는 방법
## <a name="overview"></a>개요
다양 한 유형에 대 한 인증을 사용하여 Azure 웹앱에 대한 액세스를 제한할 수 있습니다. 이렇게 하는 한 가지 방법은 TLS/SSL을 통해 요청되면 클라이언트 인증서를 사용하여 인증하는 것입니다. 이 메커니즘은 TLS 상호 인증 또는 클라이언트 인증서 인증이라고 하고, 이 문서에서는 클라이언트 인증서 인증을 사용하여 웹앱을 설치하는 방법을 자세히 다룹니다.

> **참고:** HTTP를 통해 사이트에 액세스하고 HTTPS를 통해서는 액세스하지 않는 경우 클라이언트 인증서가 제공되지 않습니다. 따라서 응용 프로그램에 클라이언트 인증서가 필요한 경우 HTTP를 통한 응용 프로그램 요청을 허용해서는 안 됩니다.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>클라이언트 인증서 인증에 대 한 웹앱을 구성합니다.
클라이언트 인증서를 요구하도록 웹앱을 설치하려면 웹앱에 대해 clientCertEnabled 사이트 설정을 추가하고 true로 설정합니다. 이 설정은 현재 포털의 관리 환경을 통해 사용할 수 없으며, REST API를 사용하여 수행해야 합니다.

[ARMClient 도구](https://github.com/projectkudu/ARMClient) 를 사용하여 REST API 호출을 쉽게 만들 수 있습니다. 이 도구를 사용하여 로그인 후 다음 명령을 실행 해야 합니다.

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

{}의 모든 항목은 웹앱 정보로 대체하고 다음의 JSON content와 함께 enableclient.json이라고 불리는 파일을 만드세요.

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

웹앱이 어떤 곳(예: 미국 중부 북쪽 또는 미국 서부 등)에 위치하든 “위치" 값을 변경해야 합니다.

https://resources.azure.com을 사용하여 `clientCertEnabled` 속성을 `true`로 대칭 이동할 수도 있습니다.

> **참고:** Powershell에서 ARMClient를 실행하는 경우 역 틱(`)을 사용하여 JSON 파일에 대한 @ 기호를 이스케이프해야 합니다.
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>웹앱에서 클라이언트 인증서에 액세스
ASP.NET을 사용할 때 클라이언트 인증서 인증을 사용하도록 앱을 구성한 경우 **HttpRequest.ClientCertificate** 속성을 통해 인증서를 사용할 수 있습니다. 다른 응용 프로그램 스택의 경우 "X-ARR-ClientCert" 요청 헤더의 Base64로 인코딩된 값을 통해 앱에서 클라이언트 인증서를 사용할 수 있습니다. 응용 프로그램은 이 값으로부터 인증서를 생성하고, 응용 프로그램에서 인증 및 권한 부여 목적으로 사용할 수 있습니다.

## <a name="special-considerations-for-certificate-validation"></a>인증서 유효성 검사에 대한 특별 고려 사항
응용 프로그램으로 가는 클라이언트 인증서는 AZure 웹앱 플랫폼에 의해 모든 유효성 검사를 통해 가지 않습니다. 이 인증서의 유효성을 검사하는 것은 웹앱의 책임입니다. 인증을 위해 인증서 속성의 유효성을 검사하는 샘플 ASP.NET 코드는 다음과 같습니다.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
