# 레디스 기본 개념

## 레디스 자료 구조
### string
- 최대 512MB 문자열 데이터 저장 가능
- 모든 종류 문자열이 'binary-safe'하게 처리됨 (ex. 바이트 값, HTTP 응답값 가능)
- SET/GET
- INCR/INCRBY: 원자적 조정 가능(여러개중 하나의 커맨드만 반영)

### list
- 순서를 가지는 문자열의 목록, 최대 42억여개 저장 가능
- 인덱스 이용으로 직접 접근. (ex. 스택, 큐)
- LPUSH(head,왼쪽 추가), RPUSH(tail,오른쪽 추가)
- LRANGE: 시작,끝 구간 내 아이템 출력
- LPOP: 앞에서부터 반환 및 삭제
- LTRIM: 지정한 범위내 아이템 삭제(반환x) -> 고정된 길이의 큐 유지 가능
- LSET, LINDEX

### hash
- 하나의 'key'에 field-value 아이템 집합
- 필드 동적 추가 용이
- HGET, HMGET, HGETALL

### Set
- 정렬되지 않은 문자열의 모음, 중복값이 저장되지 않음
- SADD,SMEMBERS, SREM, SPOP
- 합집합(SUNION), 교집합(SINTER), 차집합(SDIFF)

### sorted Set
- '스코어' 값에 따라 정렬되는 고유 문자열 집합
- list와의 비교: list는 O(n), sorted Set은 O(log(n))으로 인덱스 이용시 더 효율적
- ZADD,ZRANGE
- 조회방식
  1. 인덱스 - ZRANGE
     (모든 데이터 조회: 0, -1)
  2. 스코어 - ZRANGE + BYSCORE
     (모든 데이터 조회: -inf, +inf)
  3. 사전순 - BYLEX
 
### 비트맵
- 저장공간을 줄이고, 대규모 데이터 집계 가능
- SETBIT, GETBIT, BITFIELD, BITCOUNT

### Hyperloglog
- 집합의 원소 개수인 카디널리티 추정
- 중복되지 않는 고유한 값 집계시 유용
- 저장된 데이터를 기억하지 않고 자체적 방식으로 변경 처리, 데이터 개수에 구애받지 않고 일정 메모리량 유지 가능
- PFADD, PFCOUNT

### Geospatial
- 경도, 위도 데이터 쌍의 집합
- 내부적으로 sorted set으로, 키는 중복저장 불가
- GEOADD, GEOPOS(조회), GEODIST(아이템 사이 거리반환)
- GEOSEARCH(특정 위치 기준 내 아이템 검색) + BYRADIUS(반경기준), BYBOX(직사각형 기준)

### stream
- 메시지 브로커로 사용하는 자료구조
- 카프카 영향을 받아 만들어졌으며, 소비자 그룹개념 사용
- append-only 방식으로 계속해서 데이터를 추가

## 레디스에서 키를 관리하는 법
### 키 자동 생성&삭제
1. 키 존재X, 아이템 삽입 전 '빈 자료 구조' 생성
2. 모든 아이템 삭제시, '키'도 자동 삭제 (stream 예외)
3. 키 존재X, '읽기' 시도시 에러 대신 '아이템이 없는 것 처럼' 동작

### 키 관련 커맨드
- 조회
  - EXISTS: 키 존재시 1, 미존재시 0
  - KEYS: 키가 많아질수록 사용시 주의해야 함
  - SCAN: 커서가 기반의 특정 범위의 키를 조회, (*MATCH&TYPE: 전체 스캔 후 반환 직전 필터링 하는 방식으로 오래걸릴 수 있음)
  - SORT: 키 내부 아이템 정렬
  - RENAME/RENAMENX: 키 이름 변경
  - COPY: 키를 지정된 destination에 복사, (*REPLACE: 이미 존재 할 경우 미리 삭제 후 복사)
  - OBJECT: 키 상세정보 반환
 
- 삭제
  - FLUSHALL: 저장된 모든 키 삭제
    - ASYNC: 백그라운드 실행&도중 생성된 키는 미삭제
    - SYNC: 다른 처리 불가
  - DEL: 키와 값을 삭제
  - UNLINK: 백그라운드에서 다른 스레드에서 처리. 키와 데이터간의 연결을 끊고 삭제
    - lazyfree-lazy-user-del: yes(기본 no) -> DEL 커맨드는 UNLINK로 대체
   
- 만료
  - EXPIRE: 키 만료 시간 '초'단위 정의
  - EXPIREAT: 만료될 시간을 '타임스탬프' 정의
  - EXPIRETIME: 키가 삭제되는 타임스탬프를 '초'로 반환
  - TTL: 키가 '몇 초 뒤' 만료되는지 반환
