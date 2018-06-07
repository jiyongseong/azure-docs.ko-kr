---
title: "Azure Database for PostgreSQL에서 PostgreSQL 확장 사용"
description: "PostgreSQL용 Azure 데이터베이스에서 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 설명합니다."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0b4150fcd7d32c823173c3e2676e226634346a2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스의 PostgreSQL 확장
PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장을 통해 관련된 여러 SQL 개체를 단일 패키지에 번들로 묶을 수 있으며 단일 명령을 사용해서 데이터베이스에서 로드하거나 제거할 수 있습니다. 데이터베이스에 로드된 후에 확장은 기본 제공 기능으로 작동할 수 있습니다. PostgreSQL 확장에 대한 자세한 내용은 [관련 개체를 확장으로 패키지](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)를 참조하세요.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 확장을 사용하는 방법
PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 psql 도구에서 [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) 명령을 실행하여 패키지 개체를 데이터베이스에 로드합니다.

PostgreSQL용 Azure 데이터베이스는 현재 여기에 나열된 대로 주요 확장의 일부만 지원합니다. 나열되지 않은 확장은 지원되지 않습니다. PostgreSQL용 Azure 데이터베이스 서비스로 고유한 확장을 만들 수는 없습니다.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 지원하는 확장
다음 표에는 PostgreSQL용 Azure 데이터베이스에서 현재 지원하는 표준 PostgreSQL 확장이 나와 있습니다. 이 정보를 `pg\_available\_extensions`를 쿼리하여 얻을 수도 있습니다.

### <a name="data-types-extensions"></a>데이터 형식 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | 자동으로 암호화된 암호에 대한 데이터 형식을 제공합니다. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 대/소문자 구분 문자 문자열 형식을 제공합니다. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 다차원 큐브의 데이터 형식을 제공합니다. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 키/값 쌍 집합을 저장하기 위한 데이터 형식을 제공합니다. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 국제 제품 번호 매기기 표준에 대한 데이터 형식을 제공합니다. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 계층적 트리 구조에 대한 데이터 형식을 제공합니다. |

### <a name="functions-extensions"></a>함수 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 지구 표면의 대원 거리를 계산하는 방법을 제공합니다. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 문자열 간 유사성 및 거리를 확인하기 위한 몇 가지 함수를 제공합니다. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | null 없는 정수 배열을 조작하기 위한 함수 및 연산자를 제공합니다. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 암호화 함수를 제공합니다. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 시간 또는 ID로 분할된 테이블을 관리합니다. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | trigram 일치를 기준으로 영숫자 텍스트의 유사성을 확인하기 위한 함수 및 연산자를 제공합니다. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | 크로스탭을 비롯하여 전체 테이블을 조작하는 함수를 제공합니다. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | UUID(범용 고유 식별자)를 생성합니다. |

### <a name="full-text-search-extensions"></a>전체 텍스트 검색 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 정수에 대한 텍스트 검색 사전 템플릿을 제공합니다. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Lexemes에서 악센트(분음 기호)를 제거하는 텍스트 검색 사전입니다. |

### <a name="index-types-extensions"></a>인덱스 형식 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 특정 데이터 형식에 대해 B-트리 유사 동작을 구현하는 샘플 GIN 연산자 클래스를 제공합니다. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | B-트리를 구현하는 GiST 인덱스 연산자 클래스를 제공합니다. |

### <a name="language-extensions"></a>언어 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 로드 가능 절차 언어. |

### <a name="miscellaneous-extensions"></a>기타 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 공유 버퍼 캐시에서 일어나는 작업을 실시간으로 검사하기 위한 수단을 제공합니다. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 관계 데이터를 버퍼 캐시에 로드하는 방법을 제공합니다. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 행 수준 잠금 정보를 표시하는 방법을 제공합니다. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 튜플 수준 통계를 표시하는 방법을 제공합니다. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 외부 PostgreSQL 서버에 저장된 데이터에 액세스하는 데 사용되는 외부 데이터 래퍼입니다. |

### <a name="postgis-extensions"></a>PostGIS 확장

> [!div class="mx-tableFixed"]
| **확장** | **설명** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL에 대한 공간 및 지리적 개체입니다. |
| address\_standardizer, address\_standardizer\_data\_us | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. 지오코딩 주소 정규화 단계를 지원하는 데 사용됩니다. |
| [pgrouting](http://pgrouting.org/) | 지리 공간적 라우팅 기능을 제공하기 위해 PostGIS / PostgreSQL 지리 공간적 데이터베이스를 확장합니다. |

## <a name="next-steps"></a>다음 단계
사용하려는 확장이 표시되지 않으면 알려주세요. [고객 사용자 의견 포럼](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)에서 기존 요청에 투표하거나 새 사용자 의견 및 요청을 만드세요.
