# 레디스 시작하기

## 레디스 환경 구성
### 서버 환경 설정 변경
1. Open files 확인
- 기본 `maxclients:10000` & `레디스 프로세스 예약 파일 디스크립터 수:32`
- 서버의 파일 디스크립터 수를 `maxclients+32` 이상으로 설정해야 최대 클라이언트 수를 지정값만큼 가능

2. THP 비활성화
- THP(Transparent Huge Pages): 대용량 메모리 시스템에서 TLB(Translation Lookaside Buffer) 조회 오버헤드를 줄이는 기능
- 레디스에서는 THP 활성화시 퍼포먼스 감소, 레이턴시 증가 -> THP 비활성화 추천
(*ElastiCache에서 THP 비활성화 하는 방법에 대해서는 찾지 못함)

3. vm.overcommit_memory=1
- 순간적으로 메모리 초과 할당 사용이 가능하도록 설정 (기본값은 0으로 메모리 초과 할당 제한)

4. somaxconn & syn_backlog
- tcp-backlog 기본값 511 < (somazconn, syn_backlog)
- tcp-backlog 값보다 더 크도록 설정 필요

### 레디스 설정 파일 변경
1. port: 지정된 포트로 커넥션 접속 허용
2. bind: 외부에서 레디스 접속 허용가능한 ip
3. protected-mode(y): 패스워드 설정으로만 레디스 접속 가능
4. requirepass/masterauth: 레디스 서버 접속에 필요한 패스워드
5. daemonize: 데몬실행 여부


### 레디스 접속하기
어느 위치에서든 redis-cli 바로 접근하는 방법
```
$ export PATH=$PATH:/home/centos/redis/bin # 레디스 설치위치
$ redis-cli -h <ip주소> -p <port> -a <password>
```
