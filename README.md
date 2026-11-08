# Batch Monitor

배치 실행 이력 관리 기능을 만들기 위한 Spring Boot 학습 프로젝트입니다. Day 1에서는 서버를 실행하고 응답을 확인할 수 있는 첫 REST API를 구현했습니다.

## 개발 환경

- Java 21
- Spring Boot 4.1.2
- Gradle Wrapper 9.8.2
- Spring Web MVC

## Mac에서 실행하기

Java 21이 설치된 상태에서 프로젝트 폴더를 열고 다음 명령을 실행합니다.

```bash
JAVA_HOME=$(/usr/libexec/java_home -v 21) ./gradlew bootRun --args="--server.address=127.0.0.1 --server.port=8081"
```

다른 터미널에서 다음 요청을 보냅니다.

```bash
curl -i http://127.0.0.1:8081/api/health
```

정상 응답은 HTTP `200`이며, 응답 본문은 다음과 같습니다.

```json
{"status":"UP"}
```

서버를 종료하려면 실행한 터미널에서 `Ctrl+C`를 누릅니다.

[Day 1 실습 기록](docs/day-01.md)
