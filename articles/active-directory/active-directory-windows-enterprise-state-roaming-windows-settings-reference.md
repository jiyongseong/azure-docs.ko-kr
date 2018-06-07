---
title: Windows 10 로밍 설정 참조 | Microsoft Docs
description: Windows 10에서 로밍 또는 백업되는 모든 설정의 전체 목록입니다.
services: active-directory
keywords: 엔터프라이즈 상태 로밍, windows 클라우드
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 741b76935b5a6d9b2a6869ef57caa0ac0dc6351a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159080"
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 로밍 설정 참조
다음은 Windows 10에서 로밍 또는 백업되는 모든 설정의 전체 목록입니다. 

## <a name="devices-and-endpoints"></a>장치 및 끝점
Windows 10의 동기화, 백업 및 복원 프레임워크에서 지원되는 장치 및 계정 유형에 대한 요약 정보는 다음 테이블을 참조하세요.

| 계정 유형 및 작업 | 데스크톱 | 모바일 |
| --- | --- | --- |
| Azure Active Directory: 동기화 |예 |아니오 |
| Azure Active Directory: 백업/복원 |아니오 |아니오 |
| Microsoft 계정: 동기화 |예 |예 |
| Microsoft 계정: 백업/복원 |아니오 |예 |

## <a name="what-is-backup"></a>백업이란?
Windows 설정은 동기화가 기본이지만 장치에 설치된 응용 프로그램 목록 같은 일부 설정은 백업되기만 합니다. Backup은 모바일 장치에 대해서만 사용할 수 있으며 현재 엔터프라이즈 상태 로밍 사용자를 위해서는 사용할 수 없습니다. Backup은 Microsoft 계정을 사용하고 설정 및 응용 프로그램 데이터를 OneDrive에 저장합니다. 사용자가 설정 앱을 사용하여 장치의 동기화를 비활성화하면 평소에는 동기화되는 응용 프로그램 데이터가 백업되기만 합니다. Backup 데이터는 새 장치를 처음으로 실행하는 동안 복원 작업을 통해서만 액세스할 수 있습니다. Backup은 장치 설정을 통해 비활성화할 수 있으며 사용자의 OneDrive 계정을 통해 관리 및 삭제됩니다.

## <a name="windows-settings-overview"></a>Windows 설정 개요
다음 설정 그룹은 Windows 10 장치에서 최종 사용자가 설정 동기화를 활성화/비활성화하는 데 사용할 수 있습니다.

* 테마: 바탕 화면 배경, 사용자 타일, 작업 표시줄 위치 등 
* Internet Explorer 설정: 검색 기록, 입력한 URL, 즐겨찾기 등 
* 암호: [Windows 자격 증명 보관](https://technet.microsoft.com/library/jj554668.aspx), Wi-Fi 프로필 포함 
* 언어 기본 설정: 맞춤법 사전, 시스템 언어 설정 
* 간편한 액세스: 내레이터, 화상 키보드, 돋보기 
* 기타 Windows 설정: Windows 설정 세부 정보를 참조하세요.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-syncyoursettings.png)

Microsoft Edge 브라우저 설정 그룹(즐겨찾기, 읽기 목록) 동기화는 Microsoft Edge 브라우저 설정 메뉴 옵션을 통해 최종 사용자가 사용하거나 사용하지 않도록 설정할 수 있습니다.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-edge.png)

Windows 10 버전 1803 이상의 경우 Internet Explorer 설정 그룹(즐겨찾기, 입력한 URL) 동기화는 Internet Explorer 설정 메뉴 옵션을 통해 최종 사용자가 활성화하거나 비활성화할 수 있습니다. 

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Windows 설정 세부 정보
다음 테이블에서 설정 그룹 열의 기타 항목이 참조하는 설정은 설정 > 계정 > 설정 동기화 > 기타 Windows 설정으로 이동하여 비활성화할 수 있습니다. 

설정 그룹 열의 내부 항목이 참조하는 설정 및 앱은 앱 자체 내부에서 동기화를 비활성화하거나 MDM(모바일 장치 관리) 또는 그룹 정책 설정을 사용하여 장치 전체의 동기화를 비활성하는 방법으로만 비활성화할 수 있습니다.
로밍 또는 동기화하지 않는 설정은 그룹에 소속되지 않습니다.

| 설정 | 데스크톱 | 모바일 | 그룹 |
| --- | --- | --- | --- |
| **계정**: 계정 사진 |sync |X |테마 |
| **계정**: 기타 계정 설정 |X |X | |
| **고급 모바일 광대역**: 인터넷 연결 공유 네트워크 이름(Bluetooth를 통해 모바일 Wi-Fi 핫스폿 자동 검색 활성화) |X |X |암호 |
| **앱 데이터**: 개별 앱이 데이터 동기화 가능 |백업 동기화 |백업 동기화 |내부 |
| **앱 목록**: 설치된 앱 목록 |X |backup |기타 |
| **Bluetooth**: 모든 Bluetooth 설정 |X |X | |
| **명령 프롬프트**: 명령 프롬프트 "기본값" 설정 |sync |X | |
| **자격 증명**: 자격 증명 보관 |sync |sync |암호 |
| **날짜, 시간 및 지역**: 자동 시간(인터넷 시간 동기화) |sync |sync |언어 |
| **날짜, 시간 및 지역**: 24시간 |sync |X |언어 |
| **날짜, 시간 및 지역**: 날짜 및 시간 |sync |X |언어 |
| **날짜, 시간 및 지역**: 시간대 | |X |언어 |
| **날짜, 시간 및 지역**: 일광 절약 시간제 |sync |X |언어 |
| **날짜, 시간 및 지역**: 국가/지역 |sync |X |언어 |
| **날짜, 시간 및 지역**: 시작 요일 |sync |X |언어 |
| **날짜, 시간 및 지역**: 지역 형식(로캘) |sync |X |언어 |
| **날짜, 시간 및 지역**: 간단한 날짜 |sync |X |언어 |
| **날짜, 시간 및 지역**: 자세한 날짜 |sync |X |언어 |
| **날짜, 시간 및 지역**: 간단한 시간 |sync |X |language |
| **날짜, 시간 및 지역**: 자세한 시간 |sync |X |언어 |
| **바탕 화면 개인 설정**: 바탕 화면 테마(배경, 시스템 색, 기본 시스템 소리, 화면 보호기) |sync |X |테마 |
| **바탕 화면 개인 설정**: 슬라이드 쇼 배경 화면 |sync |X |테마 |
| **바탕 화면 개인 설정**: 작업 표시줄 설정(위치, 자동 숨김 등) |sync |X |테마 |
| **바탕 화면 개인 설정**: 시작 화면 레이아웃 |X |backup | |
| **장치**: 연결된 공유 프린터 |X |X |기타 |
| **Edge 브라우저**: 읽기 목록 |sync |sync |내부 |
| **Edge 브라우저**: 즐겨찾기 |sync |sync |내부 |
| **Edge 브라우저**: 상위 사이트 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 형식이 지정된 URL <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 즐겨찾기 표시줄 설정 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 홈 단추 표시 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 팝업 차단 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 각 다운로드에서 수행할 작업 확인 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 암호 저장 제안 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 보내기 시 요청 추적 안 함 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 양식 항목 저장 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 입력할 때 검색 및 사이트 추천 단어 표시 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 쿠키 기본 설정 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 사이트에서 내 장치에 보호된 미디어 라이선스를 저장하도록 허용 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **Edge 브라우저**: 화면 판독기 설정 <sup>[[1]](#footnote-1)</sup> |sync |sync |내부 |
| **고대비**: 켜기 또는 끄기 |sync |X |간편한 액세스 |
| **고대비**: 테마 설정 |sync |X |간편한 액세스 |
| **Internet Explorer**: 탭 열기(URL 및 제목) |sync |sync |Internet Explorer |
| **Internet Explorer**: 읽기 목록 |sync |sync |Internet Explorer |
| **Internet Explorer**: 입력한 URL |sync |sync |Internet Explorer |
| **Internet Explorer**: 검색 기록 |sync |sync |Internet Explorer |
| **Internet Explorer**: 즐겨찾기 |sync |sync |Internet Explorer |
| **Internet Explorer**: 제외된 URL |sync |sync |Internet Explorer |
| **Internet Explorer**: 홈 페이지 |sync |sync |Internet Explorer |
| **Internet Explorer**: 도메인 제안 |sync |sync |Internet Explorer |
| **키보드**: 사용자가 화상 키보드를 켜거나 끌 수 있음 |sync |X |간편한 액세스 |
| **키보드**: 스티커 켜기(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **키보드**: 필터 키 켜기(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **키보드**: 토글 키 켜기(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **Internet Explorer**: 도메인 언어: 중국어(간체) QWERTY - 자체 학습 활성화 |sync |X |언어 |
| **언어**: CHS QWERTY - 동적 후보 순위 활성화 |sync |X |언어 |
| **언어**: CHS QWERTY - 문자 집합 중국어 간체 |sync |X |언어 |
| **언어**: CHS QWERTY - 문자 집합 중국어 번체 |sync |X |언어 |
| **언어**: CHS QWERTY - 퍼지 핀잉 |sync |backup |언어 |
| **언어**: CHS QWERTY - 퍼지 쌍 |sync |backup |언어 |
| **언어**: CHS QWERTY - 완전 핀잉 |sync |X |언어 |
| **언어**: CHS QWERTY - 이중 핀잉 |sync |X |언어 |
| **언어**: CHS QWERTY - 읽기 자동 교정 |sync |X |언어 |
| **언어**: CHS QWERTY - C/E 전환 키, Shift |sync |X |언어 |
| **언어**: CHS QWERTY - C/E 전환 키, Ctrl |sync |X |언어 |
| **언어**: CHS WUBI - 단일 문자 입력 모드 |sync |X |언어 |
| **언어**: CHS WUBI - 나머지 후보 코딩 표시 |sync |X |language |
| **언어**: CHS WUBI - 4 코딩이 유효하지 않을 때 경고음 |sync |X |언어 |
| **언어**: CHS 보포모포 - CJK Ext-A 포함 |sync |X |언어 |
| **언어**: 일본어 IME - 예측 입력 및 사용자 지정 단어 |sync |sync |언어 |
| **언어**: 한국어(KOR) IME |X |X |언어 |
| **언어**: 필기 인식 |X |X |언어 |
| **언어**: 언어 프로필 |sync |backup |언어 |
| **언어**: 맞춤법 검사 - 맞춤법 오류 자동 수정 및 강조 표시 |sync |backup |언어 |
| **언어**: 키보드 목록 |sync |backup |언어 |
| **잠금 화면**: 모든 잠금 화면 설정 |X |X | |
| **돋보기**: 켜기 또는 끄기(마스터 토글) |X |X |간편한 액세스 |
| **돋보기**: 반전 색 켜기 또는 끄기(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **돋보기**: 추적 - 키보드 포커스 추적 |sync |X |간편한 액세스 |
| **돋보기**: 추적 - 마우스 커서 추적 |sync |X |간편한 액세스 |
| **돋보기**: 사용자가 로그인하면 시작(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **마우스**: 마우스 커서의 크기 변경 |sync |X |기타 |
| **마우스**: 마우스 커서의 색상 변경 |sync |X |기타 |
| **마우스**: 모든 기타 설정 |X |X | |
| **내레이터**: 빠른 실행 |sync |X |간편한 액세스 |
| **내레이터**: 사용자가 내레이터 말하는 속도를 변경할 수 있음 |sync |X |간편한 액세스 |
| **내레이터**: 사용자가 공통 항목에 대한 내레이터 읽기 힌트를 켜거나 끌 수 있음(기본적으로 켜져 있음) |sync |X |간편한 액세스 |
| **내레이터**: 사용자는 입력되는 문자를 소리로 재생하는 옵션을 켜거나 끌 수 있음(기본적으로 켜져 있음) |sync |X |간편한 액세스 |
| **내레이터**: 사용자는 입력되는 단어를 소리로 재생하는 옵션을 켜거나 끌 수 있음(기본적으로 켜져 있음) |sync |X |간편한 액세스 |
| **내레이터**: 내레이터 뒤에 커서 삽입(기본적으로 켜져 있음) |sync |X |간편한 액세스 |
| **내레이터**: 내레이터 커서의 시각적 강조 표시 활성화(기본적으로 켜져 있음) |sync |X |간편한 액세스 |
| **내레이터**: 오디오 재생 큐(기본적으로 켜져 있음) |sync |X |간편한 액세스 |
| **내레이터**: 사용자가 손가락을 떼면 터치 키보드의 키 활성화(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **간편한 액세스**: 깜박이 커서의 두께 설정 |sync |X |간편한 액세스 |
| **간편한 액세스**: 배경 이미지 제거(기본적으로 꺼져 있음) |sync |X |간편한 액세스 |
| **전원 및 절전**: 모든 설정 |X |X | |
| **시작 화면 개인 설정**: 강조 색(phone 전용) |X |sync |테마 |
| **입력**: 맞춤법 사전 |sync |backup |언어 |
| **입력**: 맞춤법이 틀린 단어 자동 교정 |sync |backup |언어 |
| **입력**: 맞춤법이 틀린 단어에 강조 표시 |sync |backup |언어 |
| **입력**: 입력할 때 텍스트 제안 표시 |sync |backup |언어 |
| **입력할 때**: 텍스트 제안 선택 후에 공백을 추가 |sync |backup |언어 |
| **입력**: 스페이스바를 두 번 탭하면 마침표 추가 |sync |backup |언어 |
| **입력**: 각 문장의 첫 번째 문자를 대문자로 표기 |sync |backup |언어 |
| **입력**: Shift 키를 두 번 탭하면 모든 문자를 대문자로 표기 |sync |backup |언어 |
| **입력**: 입력할 때 키 소리 재생 |sync |backup |언어 |
| **입력**: 터치 키보드의 개인 설정 데이터 |sync |backup |언어 |
| **Wi-Fi**: Wi-Fi 프로필(WPA만) |sync |sync |암호 |

###### <a name="footnote-1"></a>각주 1
지원되는 최소 OS 업데이트 버전은 Windows 작성자 업데이트(빌드 15063)입니다. 

## <a name="related-topics"></a>관련된 항목
* [엔터프라이즈 상태 로밍 개요](active-directory-windows-enterprise-state-roaming-overview.md)
* [Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화](active-directory-windows-enterprise-state-roaming-enable.md)
* [설정 및 데이터 로밍 FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [설정 동기화에 대한 그룹 정책 및 MDM 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [문제 해결](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
