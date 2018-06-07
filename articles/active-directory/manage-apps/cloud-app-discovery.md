---
title: Azure Active Directory에서 Cloud App Discovery 서비스 설정 | Microsoft Docs
description: Cloud App Discovery를 사용하여 응용 프로그램을 찾고 관리하여 클라우드 사용 및 섀도 IT에 대한 실행 가능한 정보를 제공합니다.
services: active-directory
keywords: Cloud App Discovery, 응용 프로그램 관리
documentationcenter: ''
author: barbkess
manager: mtillman
tags: ignite
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 04/25/2018
ms.author: barbkess
ms.reviewer: nigu
ms.openlocfilehash: 0b1c525ea0333d91538986810981eee10d1c4f2a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058234"
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Azure AD에서 Cloud App Discovery 설정

Azure AD의 Cloud App Discovery는 이제 Microsoft Cloud App Security에서 사용할 수 있는 데이터와의 통합을 기반으로 합니다. Cloud App Discovery는 트래픽 로그를 15,000개 이상의 클라우드 앱으로 구성된 Cloud App Security 카탈로그와 비교하여 클라우드 사용 및 섀도 IT에 대한 지속적인 정보를 제공합니다. 이 문서에서는 설치 프로세스를 설명하고, 각 단계에 대한 자세한 정보에 연결되는 링크를 제공합니다. 또한 방화벽 및 프록시 정보와 로그 파일 지원에 대해서도 설명합니다.

## <a name="prerequisites"></a>필수 조건

* 조직에 제품 사용을 위한 Azure AD Premium P1 라이선스가 있어야 합니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.
* Cloud App Discovery를 설정하려면 Azure Active Directory의 전역 관리자이거나 보안 읽기 권한자이어야 합니다.

## <a name="setup-steps"></a>설정 단계

1. [스냅숏 보고서](cloud-app-discovery-create-snapshot-reports.md)를 로그 형식에서 로그가 Cloud App Discovery에 사용 가능한 정보를 제공하는지 확인하도록 설정합니다. 또한 이들은 방화벽과 프록시 서버에서 수동으로 업로드한 트래픽 로그에 대한 임시 정보를 제공합니다.

2. [연속 보고](https://docs.microsoft.com/cloud-app-security/discovery-docker)를 Cloud App Security 로그 수집기를 통해 네트워크에서 전달된 모든 로그를 분석하도록 설정합니다. 이를 통해 새 앱과 사용량 추세를 파악할 수 있습니다.

3. 로그가 현재 지원되지 않는 경우 Cloud App Discovery가 로그를 분석하도록 [사용자 지정 로그 파서를 설정](https://docs.microsoft.com/cloud-app-security/custom-log-parser)합니다.
  
## <a name="log-processing-flow"></a>로그 처리 흐름

보고서 생성은 데이터 규모에 따라 몇 분에서 몇 시간까지 걸릴 수 있습니다. 분석 대상은 다음과 같습니다.

* **업로드** 네트워크의 웹 트래픽 로그를 포털에 업로드합니다.
* **구문 분석** Cloud App Security가 각 데이터 원본에 대한 전용 구문 분석기로 트래픽 로그에서 트래픽 데이터를 구문 분석 및 추출합니다.
* **분석** 트래픽 데이터를 Cloud App Security 카탈로그에 대해 분석하여 15,000여 클라우드 앱을 식별합니다. 활성 사용자 및 IP 주소는 분석의 일환으로 식별됩니다.
* **보고서 생성** Cloud App Security가 로그 파일에서 추출된 데이터 보고서를 생성하여 Cloud App Discovery에 제공합니다.

> [!NOTE]
> * 연속 보고서 데이터는 하루에 두 번 분석됩니다.
> * 로그 수집기는 데이터 업로드 전에 데이터를 압축합니다. 로그 수집기의 아웃바운드 트래픽은 수신된 트래픽 로그 크기의 10%선입니다.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Cloud App Discovery에 트래픽 로그 사용

Cloud App Discovery는 트래픽 로그에서 데이터를 사용합니다. 로그에 더 자세한 내용을 추가할수록 더 나은 정보가 확보됩니다. Cloud App Discovery에는 다음과 같은 특성을 갖는 웹 트래픽 데이터가 필요합니다.

* 트랜잭션 날짜
* 원본 IP 주소
* 원본 사용자 **권장**
* 대상 IP 주소 
* 대상 URL **권장**(클라우드 앱 검색에서는 URL이 IP 주소보다 더 정확함)
* 총 데이터 크기
* 클라우드 앱 사용량 패턴 관련 정보를 위해 업로드 또는 다운로드된 크기
* 수행 작업(허용/차단) 

Cloud App Discovery는 로그에 포함되지 않은 특성을 표시하거나 분석할 수 없습니다. 예를 들어 **Cisco ASA 방화벽** 표준 로그 형식은 **트랜잭션당 업로드 된 바이트 크기**, **사용자 이름** 또는 **대상 URL**은 포함하지 않고 대상 IP 주소만 포함합니다. 따라서 이 데이터 원본에서 클라우드 앱에 대한 정보가 적을 수 있습니다. Cisco ASA 방화벽의 경우 정보 수준을 6.1로 설정합니다.

Cloud App Discovery 보고서를 생성하려면 트래픽 로그가 다음 조건에 맞아야 합니다.

1.  데이터 원본이 [지원되는 방화벽 또는 프록시 서버](#supported-firewalls-and-proxies)입니다.
2.  로그 형식이 예상 표준 형식과 일치합니다. 이 조건은 업로드 시 확인됩니다. 로그 형식을 최적화하려면 [스냅숏 Cloud App Discovery 보고서 만들기](cloud-app-discovery-create-snapshot-reports.md)를 참조하세요.
3.  이벤트는 90일 이하입니다.
4.  로그 파일이 올바르며 아웃바운드 트래픽 정보를 포함합니다.

## <a name="supported-firewalls-and-proxy-servers"></a>지원되는 방화벽 및 프록시 서버

* Barracuda - 웹앱 방화벽(W3C)
* Blue Coat Proxy SG - 액세스 로그(W3C)
* Check Point
* Cisco ASA FirePOWER
* Cisco ASA 방화벽(Cisco ASA 방화벽의 경우 정보 수준을 6으로 설정)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – URL 로그
* Clavister NGFW(Syslog)
* Dell Sonicwall
* Digital Arts i-FILTER
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway(W3C)
* Palo Alto 시리즈 방화벽
* Sophos SG
* Sophos Cyberoam
* Squid(공통)
* Squid(기본)
* Websense - Web Security Solutions - 조사용 상세 보고서(CSV)
* Websense - Web Security Solutions - 인터넷 활동 로그(CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery는 IPv4 및 IPv6 주소를 모두 지원합니다.

로그가 지원되지 않는 경우 **데이터 원본**으로 **기타**를 선택하고 업로드할 로그와 장치를 지정합니다. Cloud App Security 클라우드 분석 팀에서 로그를 검토합니다. 로그 유형에 대한 지원이 추가되면 이를 사용자에게 알리지만, 그 대신 사용자가 로그 형식에 부합하는 사용자 지정 구문 분석기를 정의할 수 있습니다. 자세한 내용은 [사용자 지정 로그 분석기 사용](https://docs.microsoft.com/cloud-app-security/custom-log-parser)을 참조하세요

## <a name="data-attributes-according-to-vendor-documentation"></a>데이터 특징(공급업체 설명서 참조)

| 데이터 원본         | 대상 앱 URL | 대상 앱 IP 주소 | 사용자 이름 | 원래 IP 주소 | 총 트래픽 | 업로드된 바이트 |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **예**        | **예**       | **예**  | **예**   | 아니오            | 아니오             |
| Blue Coat                               | **예**        | 아니오            | **예**  | **예**   | **예**       | **예**        |
| 검사점                              | 아니오             | **예**       | 아니오       | **예**   | 아니오            | 아니오             |
| Cisco ASA                               | 아니오             | **예**       | 아니오       | **예**   | **예**       | 아니오             |
| Cisco FWSM                              | 아니오             | **예**       | 아니오       | **예**   | **예**       | 아니오             |
| Cisco Ironport WSA                      | **예**        | **예**       | **예**  | **예**   | **예**       | **예**        |
| Cisco Meraki                            | **예**        | **예**       | 아니오       | **예**   | 아니오            | 아니오             |
| Clavister NGFW(Syslog)                 | **예**        | **예**       | **예**  | **예**   | **예**       | **예**        |
| Dell SonicWall                          | **예**        | **예**       | 아니오       | **예**   | **예**       | **예**        |
| Fortigate                               | 아니오             | **예**       | 아니오       | **예**   | **예**       | **예**        |
| Juniper SRX                             | 아니오             | **예**       | 아니오       | **예**   | **예**       | **예**        |
| Juniper SSG                             | 아니오             | **예**       | 아니오       | **예**   | **예**       | **예**        |
| McAfee SWG                              | **예**        | 아니오            | 아니오       | **예**   | **예**       | **예**        |
| MS TMG                                  | **예**        | 아니오            | **예**  | **예**   | **예**       | **예**        |
| Palo Alto Networks                      | 아니오             | **예**       | **예**  | **예**   | **예**       | **예**        |
| Sophos                                  | **예**        | **예**       | **예**  | **예**   | **예**       | 아니오             |
| Squid(공통)                          | **예**        | 아니오            | **예**  | **예**   | 아니오            | **예**        |
| Squid(기본)                          | **예**        | 아니오            | **예**  | **예**   | 아니오            | **예**        |
| Websense - 조사용 보고서(CSV)   | **예**        | **예**       | **예**  | **예**   | **예**       | **예**        |
| Websense - 인터넷 활동 로그(CEF)  | **예**        | **예**       | **예**  | **예**   | **예**       | **예**        |
| Zscaler                                 | **예**        | **예**       | **예**  | **예**   | **예**       | **예**        |


## <a name="next-steps"></a>다음 단계
다음 링크를 사용하여 Azure AD에서 Cloud App Discovery 설정을 계속합니다.

* [스냅숏 보고서 만들기](cloud-app-discovery-create-snapshot-reports.md)
* [연속 보고 구성](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [사용자 지정 로그 구문 분석기 사용](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
