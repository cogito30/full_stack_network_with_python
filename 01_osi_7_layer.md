# OSI 7 Layer 실습

## OSI 7 Layer (Encapsulation/Decapsulation)
- L7(Application): 데이터가 JSON 형태로 포장. HTTP Post 요청의 Body에 담김
- L6(Presentation): JSON 데이터가 네트워크 전송에 적합한 문자열(UTF-8)로 인코딩. TLS로 암호화 처리
- L5(Session): 브라우저와 백엔드(port:8000)간의 논리적 연결
- L4(Transport): 데이터가 Segment로 나누어짐. TCP 헤더가 추가되며, 출발지와 도착지의 포트가 기록
- L3(Network): Segment에 IP 헤더가 붙어 Packet이 됨. 출발지와 도착지의 IP가 추가
- L2(Data Link): Packet에 MAC 주소가 포함된 Ethernet 헤더와 Trailer가 붙어 Frame이 됨. 공유기나 스위치로 향하는 물리적 주소
- L1(Physical): Frame잉 0과 1의 전기 신호나 무선 전파로 변환되어 서버가 있는 곳으로 전송

## 커스텀 TCP Protocol
- 수신한 데이터를 가공한 뒤, 타켓 서버로 보내기 위해 새로운 TCP 커넥션을 맺음
- L7 커스텀 설계: HTTP 헤더 대신, 직접 정의한 규칙 사용. 데이터를 직접 Byte 단위로 조립
- L4-L1 Encapsulation 재진행: 조립된 커스텀 바이트 데이터는 Socket 인터페이스를 통해 L4로 ㄴ내려가 TCP 헤더가 붙고 L3, L2를 거쳐 타켓 서버로 전송
- 타켓 서버의 Decapsulation: 타겟 서버는 L4까지 역캡슐화하여 TCP 스트림을 읽은 뒤, L7 커스텀 룰에 따라 데이터를 끊어서 해석

## 
| 계층/구분 | 추천 기술 스택| 선택 이유 | 
| :--: | :--: | :--: | 
| 프론트엔드 | React (Vite) + TypeScript,"빠르고 가벼운 개발 환경. Mac 환경의 Node.js 생태계와 완벽히 호환되며, 네트워크 지연 상태를 UI로 상태(State) 관리하기 가장 좋습니다." | 
| 백엔드 (API) | Python 3.13 + FastAPI | "비동기 I/O 처리에 최적화되어 있습니다. 프론트엔드의 HTTP/WebSocket 요청을 병목 없이 처리하며, Pydantic을 통해 데이터 유효성 검사를 자동으로 해줍니다." | 
| 백엔드 (TCP) | Python 3.13 내장 asyncio | 커스텀 TCP 소켓 통신을 직접 제어할 때 별도의 라이브러리 없이 asyncio.start_server와 open_connection만으로 고성능 논블로킹 TCP 서버/클라이언트를 짤 수 있습니다. | 
| 네트워크 도구 | Wireshark / tcpdump (Mac 내장) | "Mac 터미널에서 tcpdump를 쓰거나 Wireshark를 설치하여, 커스텀 TCP 프로토콜이 우리가 설계한 헤더 구조대로 정확히 캡슐화되어 나가는지 눈으로 확인(패킷 스니핑)해야 합니다." | 
| 환경 구성 | Homebrew + Docker Desktop | "Mac의 필수 패키지 매니저(Brew)와, 타겟 서버를 로컬에서 격리된 환경으로 띄워 통신 테스트를 하기 위한 용도입니다." | 