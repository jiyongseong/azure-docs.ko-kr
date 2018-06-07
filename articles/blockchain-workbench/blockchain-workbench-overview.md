---
title: Azure Blockchain Workbench 개요
description: Azure Blockchain Workbench 및 관련 기능에 대해 간략히 설명합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/21/2018
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 1f1aa6b5a81285f2f6b3a298917f3d7c48853e29
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-blockchain-workbench"></a>Azure Blockchain Workbench란?

Azure Blockchain Workbench는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 응용 프로그램을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능의 모음입니다. Azure Blockchain Workbench는 개발자가 비즈니스 논리 및 스마트 계약을 만드는 데 집중할 수 있도록 블록체인 응용 프로그램을 빌드하기 위한 인프라 스캐폴딩을 제공합니다. 또한 일반적인 개발 작업을 자동화할 수 있도록 여러 Azure 서비스와 기능을 통합하여 블록체인 응용 프로그램을 쉽게 만들 수도 있습니다.

## <a name="create-blockchain-applications"></a>블록체인 응용 프로그램 만들기

Blockchain Workbench를 사용하면 구성 및 스마트 계약 코드 작성을 통해 블록체인 응용 프로그램을 정의할 수 있습니다. 블록체인 응용 프로그램 개발에 착수하고, 스캐폴딩 및 설정 지원 서비스를 작성하는 대신 계약을 정의하고 비즈니스 논리를 작성하는 데 집중할 수 있습니다.

## <a name="manage-applications-and-users"></a>응용 프로그램 및 사용자 관리

Azure Blockchain Workbench는 블록체인 응용 프로그램과 사용자를 관리하기 위한 웹 응용 프로그램 및 REST API를 제공합니다. Blockchain Workbench 관리자는 응용 프로그램 액세스를 관리하고 사용자를 응용 프로그램 역할에 할당할 수 있습니다. Azure AD 사용자는 응용 프로그램의 멤버에 자동으로 매핑됩니다.

## <a name="integrate-blockchain-with-applications"></a>블록체인과 응용 프로그램의 통합

Blockchain Workbench REST API와 메시지 기반 API를 사용하여 기존 시스템과 통합할 수 있습니다. API는 여러 분산 원장 기술, 저장소 및 데이터베이스 제공물을 대체하거나 사용할 수 있도록 하는 인터페이스를 제공합니다.

Blockchain Workbench는 메시지 기반 API에 보내진 메시지를 변환하여 트랜잭션을 해당 블록체인의 네이티브 API에 필요한 형식으로 빌드할 수 있습니다.  Workbench는 트랜잭션을 적절한 블록체인에 서명하고 라우팅할 수 있습니다. 

Workbench는 이벤트를 Service Bus 및 Event Grid에 자동으로 전달하여 다운스트림 소비자에게 메시지를 보냅니다. 개발자는 이러한 메시징 시스템 중 하나와 통합하여 트랜잭션을 구동하고 결과를 살펴볼 수 있습니다.

## <a name="deploy-a-blockchain-network"></a>블록체인 네트워크 배포

Azure Blockchain Workbench는 Azure Resource Manager 솔루션 템플릿을 사용하여 컨소시엄 블록체인 네트워크 설정을 미리 구성된 솔루션으로 간소화합니다. 이 템플릿은 컨소시엄을 운영하는 데 필요한 모든 구성 요소를 배포하는 간소화된 배포를 제공합니다. Blockchain Workbench는 현재 Ethereum을 지원합니다.

## <a name="use-active-directory-login"></a>Active Directory 로그인 사용

기존 블록체인 프로토콜을 사용하면 블록체인 ID가 네트워크의 주소로 표시됩니다. Azure Blockchain Workbench는 Active Directory ID와 연결하여 블록체인 ID를 추상화하므로, Active Directory ID를 사용하여 엔터프라이즈 응용 프로그램을 더 쉽게 빌드할 수 있습니다.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>온 체인 데이터를 오프 체인 저장소와 동기화

Azure Blockchain Workbench를 사용하면 블록체인의 데이터를 오프 체인 저장소에 자동으로 동기화하여 블록체인 이벤트와 데이터를 더 쉽게 분석할 수 있습니다. 블록체인에서 데이터를 직접 추출하는 대신 SQL Server와 같은 오프 체인 데이터베이스 시스템을 쿼리할 수 있습니다. 데이터 분석 작업을 수행하는 최종 사용자에게는 블록체인에 대한 특정 전문 지식이 필요하지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Blockchain Workbench 배포](blockchain-workbench-deploy.md)
