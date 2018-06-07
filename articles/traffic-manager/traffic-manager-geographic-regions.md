---
title: "Azure Traffic Manager 지리적 라우팅 유형에서 사용하는 국가/지역 계층 구조 | Microsoft Docs"
description: "이 문서에서는 Azure Traffic Manager 지리적 라우팅 유형에서 사용하는 국가/지역 계층 구조를 나열합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 45fa209b4417ce8b65d1fa4f2f59ff6c2d50f3a4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>지리적 트래픽 라우팅 메소드에 대해 Azure Traffic Manager에서 사용하는 국가/지역 계층 구조

이 문서에서는 Azure Traffic Manager의 **지리적** 트래픽 라우팅 메서드에서 사용하는 국가 및 지역을 나열합니다. [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 호출하여 이 정보를 프로그래밍 방식으로 가져올 수도 있습니다. 

- WORLD(전 세계)

    - GEO-EU(유럽)

        - AD(안도라)

        - AL(알바니아)

        - AT(오스트리아)

        - AX(올란드 제도)

        - BA(보스니아 헤르체고비나)

        - BE(벨기에)

        - BG(불가리아)

        - BY(벨라루스)

        - CH(스위스)

        - CY(키프로스)

        - CZ(체코 공화국)

        - DE(독일)

        - DK(덴마크)

        - EE(에스토니아)

        - ES(스페인)

        - FI(핀란드)

        - FO(페로 제도)

        - FR(프랑스)

        - GB(영국)

        - GG(건지)

        - GI(지브롤터)

        - GR(그리스)

        - HR(크로아티아)

        - HU(헝가리)

        - IE(아일랜드)

        - IM(맨 섬)

        - IE(아이슬란드)

        - IT(이탈리아)

        - JE(저지)

        - LI(리히텐슈타인)

        - LT(리투아니아)

        - LU(룩셈부르크)

        - LV(라트비아)

        - MC(모나코)

        - MD(몰도바)

        - ME(몬테네그로)

        - MK(마케도니아, FYRO)

        - MT(몰타)

        - NL(네덜란드)

        - NO(노르웨이)

        - PL(폴란드)

        - PT(포르투갈)

        - RO(루마니아)

        - RS(세르비아)

        - RU(러시아)

        - SE(스웨덴)

        - SI(슬로베니아)

        - SJ(스발바르)

        - SK(슬로바키아)

        - SM(산마리노)

        - UA(우크라이나)

        - VA (바티칸 시국)

        - XJ(얀마웬)

        - XK(코소보)

    - GEO-ME(중동)

        - AE(아랍에미리트)

        - BH(바레인)

        - IL(이스라엘)

        - IQ(이라크)

        - IR(이란)

        - JO(요르단)

        - KW(쿠웨이트)

        - LB(레바논)

        - OM(오만)

        - PS(팔레스타인 자치 정부)

        - QA(카타르)

        - SY(시리아)

        - SA(사우디아라비아)

        - TR(터키)

        - YE(예멘)

    - GEO-NA(북아메리카/중앙 아메리카/카리브 해)

        - AG(앤티가 바부다)

        - AI(앵귈라)

        - AW(아루바)

        - BB(바베이도스)

        - BL(세인트 바르텔레미)

        - BM(버뮤다)

        - BQ(보네르)

        - BS(바하마)

        - BZ(벨리즈)

        - CA(캐나다)

            - CA-AB(앨버타)

            - CA BC(브리티시 컬럼비아)

            - CA-MB(매니토바)

            - CA NB(뉴브런즈윅)

            - CA NL(뉴펀들랜드 및 래브라도 반도)

            - CA NS(노바스코샤)

            - CA NT(노스웨스트 준주)

            - CA-NU(누나부트)

            - CA-ON(온타리오)

            - CA PE(프린스에드워드 섬)

            - CA-QC(퀘벡)

            - CA-SK(서스캐처원)

            - CA YT(유콘 준주)

        - CR(코스타리카)

        - CU(쿠바)

        - CW(퀴라소)

        - DM(도미니카)

        - DO(도미니카 공화국)

        - GD(그레나다)

        - GL(그린란드)

        - GP(과들루프)

        - GT(과테말라)

        - HN(온두라스)

        - HT(아이티)

        - JM(자메이카)

        - KN(세인트 크리스토퍼 네비스)

        - KY(케이맨 제도)

        - LC(세인트루시아)

        - MF(세인트 마틴)

        - MQ(마르티니크)

        - MS(몬트세라트)

        - MX(멕시코)

        - NI(니카라과)

        - PA(파나마)

        - PM(생피에르앤드미클롱)

        - PR(푸에르토리코)

        - SV(엘살바도르)

        - SX(신트마르텐)

        - TC(터크스 케이커스 제도)

        - TT(트리니다드 토바고)

        - UM(미국 소수 외부 제도)

        - US(미국)

            - US-AK(알래스카)

            - US-AL(앨라배마)

            - US-AR(아칸소)

            - US-AZ(애리조나)

            - US-CA(캘리포니아)

            - US-CO(콜로라도)

            - US-CT(코네티컷)

            - US-DC(콜롬비아 특별구)

            - US-DE(델라웨어)

            - US-FL(플로리다)

            - US-GA(조지아)

            - US-HI(하와이)

            - US-IA(아이오와)

            - US-ID(아이다호)

            - US-IL(일리노이)

            - US-IN(인디애나)

            - US-KS(캔자스)

            - US-KY(켄터키)

            - US-LA(주이지애나)

            - US-MA(매사추세츠)

            - US-MD(메릴랜드)

            - US-ME(메인)

            - US-MI(미시간)

            - US-MN(미네소타)

            - US-MO(미주리)

            - US-MS(미시시피)

            - US-MT(몬타나)

            - US-NC(노스캐롤라이나)

            - US-ND(노스다코타)

            - US-NE(네브라스카)

            - US-NH(뉴햄프셔)

            - US-NJ(뉴저지)

            - US-NM(뉴멕시코)

            - US-NV(네바다)

            - US-NY(뉴욕)

            - US-OH(오하이오)

            - US-OK(오클라호마)

            - US-OR(오레곤)

            - US-PA(펜실베니아)

            - US-RI(로드아일랜드)

            - US-SC(사우스캐롤라이나)

            - US-SD(사우스다코타)

            - US-TN(테네시)

            - US-TX(텍사스)

            - US-UT(유타)

            - US-VA(버지니아)

            - US-VT(버몬트)

            - US-WA(워싱턴)

            - US-WI(위스콘신)

            - US-WV(웨스트버지니아)

            - US-WY(와이오밍)

        - VC(세인트 빈센트 그레나딘)

        - VG(영국령 버진 아일랜드)

        - VI(미국령 버진 아일랜드)

        - XE(신트외스타티우스)

        - XS(사바)

    - GEO-AS(아시아)

        - AF(아프가니스탄)

        - AM(아르메니아)

        - AZ(아제르바이잔)

        - BD(방글라데시)

        - BN(브루나이)

        - BT(부탄)

        - CC(코코스 제도)

        - CN(중국)

        - CX(크리스마스 섬)

        - GE(조지아)

        - HK(홍콩 특별 행정구)

        - ID(인도네시아)

        - IN(인도)

        - IO(영국령 인도양 식민지)

        - JP(일본)

        - KG(키르기스스탄)

        - KH(캄보디아)

        - KP(북한)

        - KR(대한민국)

        - KZ(카자흐스탄)

        - LA(라오스)

        - LK(스리랑카)

        - MM(미얀마)

        - MN(몽고)

        - MO(마카오 특별 행정구)

        - MV(몰디브)

        - MY(말레이시아)

        - NP(네팔)

        - PH(필리핀)

        - PK(파키스탄)

        - SG(싱가포르)

        - TH(태국)

        - TJ(타지키스탄)

        - TL(동티모르)

        - TM(투르크메니스탄)

        - TW(대만)

        - UZ(우즈베키스탄)

        - VN(베트남)

    - GEO-AF(아프리카)

        - AO(앙골라)

        - BF(부르키나파소)

        - BI(부룬디)

        - BJ(베냉)

        - BV(부베이 섬)

        - BW(보츠와나)

        - CD(콩고 민주 공화국)

        - CF(중앙 아프리카 공화국)

        - CG(콩고)

        - CI(코트디부아르)

        - CM(카메룬)

        - CV(카보베르데)

        - DJ(지부티)

        - DZ(알제리)

        - EG(이집트)

        - ER(에리트리아)

        - ET(에티오피아)

        - GA(가봉)

        - GH(가나)

        - GM(감비아)

        - GN(기니)

        - GQ(적도 기니)

        - GW(기니비사우)

        - KE(케냐)

        - KM(코모로)

        - LR(라이베리아)

        - LS(레소토)

        - LY(리비아)

        - MA(모로코)

        - MG(마다가스카르)

        - ML(말리)

        - MR(모리타니아)

        - MU(모리셔스)

        - MW(말라위)

        - MZ(모잠비크)

        - NA(나미비아)

        - NE(니제르)

        - NG(나이지리아)

        - RE(레위니옹)

        - RW(르완다)

        - SC(세이셸)

        - SD(수단)

        - SH(세인트 헬레나, 어센션 및 트리스탄 다 쿠나)

        - SL(시에라리온)

        - SN(세네갈)

        - SO(소말리아)

        - SS(남수단)

        - ST(상투메 프린시페)

        - SZ(스와질랜드)

        - TD(차드)

        - TF(프랑스 남쪽 영역)

        - TG(토고)

        - TN(튀니지아)

        - TZ(탄자니아)

        - UG(우간다)

        - YT(마요트)

        - ZA(남아프리카)

        - ZM(잠비아)

        - ZW(짐바브웨)

    - GEO-AN(남극 대륙)

        - AQ(남극 대륙)

    - GEO-SA(남아메리카)

        - AR(아르헨티나)

        - BO(볼리비아)

        - BR(브라질)

        - CL(칠레)

        - CO(콜롬비아)

        - EC(에콰도르)

        - FK(포클랜드 제도)

        - GF(프랑스령 기아나)

        - GS(사우스 조지아 및 사우스 샌드위치 제도)

        - GY(가이아나)

        - PE(페루)

        - PY(파라과이)

        - SR(수리남)

        - UY(우루과이)

        - VE(베네수엘라)

    - GEO-AP(오스트레일리아/태평양)

        - AS(미국령 사모아)

        - AU(오스트레일리아)

            - AU-ACT(오스트레일리아 수도 준주)

            - AU-NSW(뉴사우스웨일스)

            - AU NT(노던 준주)

            - AU-QLD(퀸즐랜드)

            - AU-SA(사우스오스트레일리아)

            - AU-TAS(태즈메이니아)

            - AU-VIC(빅토리아)

            - AU-WA(웨스턴오스트레일리아)

        - CK(쿡 제도)

        - FJ(피지)

        - FM(마이크로네시아)

        - GU(괌)

        - HM(허드 섬 및 맥도널드 제도)

        - KI(키리바시)

        - MH(마셜 제도)

        - MP(북마리아나 제도)

        - NC(뉴칼레도니아)

        - NF(노퍽 섬)

        - NR(나우루)

        - NU(니우에)

        - NZ(뉴질랜드)

        - PF(프랑스령 폴리네시아)

        - PG(파푸아뉴기니)

        - PN(핏케언 제도)

        - PW(팔라우)

        - SB(솔로몬 제도)

        - TK(토켈라우)

        - TO(통가)

        - TV(투발루)

        - VU(바누아투)

        - WF(월리스 푸투나)

        - WS(사모아)

## <a name="next-steps"></a>다음 단계

- [Azure Traffic Manager에서 지리적 트래픽 라우팅 메서드](traffic-manager-routing-methods.md#geographic)에 대해 자세히 알아봅니다.
