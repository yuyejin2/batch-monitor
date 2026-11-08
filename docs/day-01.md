# Day 1 — Spring Boot 프로젝트와 첫 REST API

2026년 11월 8일 · Mac · IntelliJ IDEA Community Edition

오늘 목표는 Spring Boot 프로젝트를 만들고 `GET /api/health`에 요청을 보내 응답을 확인한 뒤, 작업을 Git에 저장하는 것이었다.

## 1. Spring Initializr에서 프로젝트 만들기

처음에는 명령줄에서 Spring Initializr에 접속했다. `Could not resolve host: start.spring.io` 오류가 나면서 사이트 주소를 IP 주소로 바꾸는 단계에서 멈췄다. 정확한 원인은 확인하지 못했다. 우선 Mac에 이미 내려받아 둔 라이브러리에 맞춰 임시 프로젝트를 만들었다. 이후 브라우저에서 같은 사이트가 정상적으로 열려 공식 ZIP을 받았다. 아래 실습은 그 ZIP에서 풀어낸 프로젝트를 기준으로 진행했다.

[Spring Initializr](https://start.spring.io/)에서 선택한 설정은 다음과 같다.

| 항목 | 선택값 | 역할 |
| --- | --- | --- |
| Project | Gradle - Groovy | 의존성을 관리하고 프로젝트를 빌드한다. |
| Language | Java | 애플리케이션을 작성할 언어다. |
| Spring Boot | 4.1.2 | 생성 당시 선택한 안정 버전이다. |
| Group | `com.example` | 프로젝트 그룹을 식별한다. |
| Artifact | `batch-monitor` | 프로젝트와 빌드 결과물 이름의 기준이다. |
| Package name | `com.example.batchmonitor` | Java 소스가 속할 기본 패키지다. |
| Packaging | Jar | 빌드 결과물을 JAR 형식으로 패키징한다. |
| Configuration | Properties | 설정을 `application.properties`에 기록한다. |
| Java | 21 | Mac에 설치된 Java 21을 사용한다. |
| Dependency | Spring Web | Spring MVC로 HTTP 요청을 처리하고 응답을 보낸다. |

**Explore**에서 생성될 파일을 확인하고 **Download**를 눌렀다. ZIP을 별도 폴더에 풀어 처음 만들었던 프로젝트를 보존했다.

| 생성된 파일 | 역할 |
| --- | --- |
| `build.gradle` | Java 버전과 Spring Boot 플러그인, 필요한 라이브러리를 지정한다. |
| `settings.gradle` | Gradle 프로젝트 이름을 정한다. |
| `gradlew`, `gradlew.bat`, `gradle/wrapper/` | 프로젝트에서 지정한 Gradle 9.8.2로 빌드할 수 있게 한다. |
| `BatchMonitorApplication.java` | `main()`에서 `SpringApplication.run(...)`을 호출해 Spring Boot를 시작한다. |
| `application.properties` | 애플리케이션 이름 등 실행 설정을 기록한다. |
| `BatchMonitorApplicationTests.java` | Spring이 애플리케이션 설정을 불러올 수 있는지 확인하는 기본 테스트다. |
| `.gitignore` | 빌드 결과물, Gradle 작업 데이터, IntelliJ 설정 등을 커밋에서 제외한다. |

## 2. IntelliJ에서 프로젝트 열기

처음 실행한 IntelliJ는 체험 기간이 만료됐다는 안내를 표시했다. Mac에 설치된 Community Edition을 열고 **File → Open**에서 ZIP을 풀어낸 `batch-monitor` 폴더를 선택했다. 공식 ZIP에서 받은 해당 프로젝트를 신뢰하도록 선택하고, 기존 강의 프로젝트를 열어 둔 상태에서 **New Window**로 열었다.

**Build → Sync** 창에서 Gradle 9.8.2와 Spring 라이브러리가 내려받아지는 과정과 `BUILD SUCCESSFUL`을 확인했다. 이 결과는 IntelliJ가 Gradle 프로젝트를 읽고 필요한 라이브러리를 가져왔다는 뜻이다. HTTP 요청이 들어오는지는 이후 서버를 실행해 확인했다.

## 3. `GET /api/health` 작성하기

`BatchMonitorApplication`과 같은 패키지에 [HealthController.java](../src/main/java/com/example/batchmonitor/HealthController.java)를 추가했다.

```java
package com.example.batchmonitor;

import java.util.Map;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HealthController {

    @GetMapping("/api/health")
    public Map<String, String> health() {
        return Map.of("status", "UP");
    }
}
```

1. `package` 선언은 클래스가 속한 패키지를 정한다. 시작 클래스와 같은 패키지에 있으므로 Spring Boot의 기본 탐색 범위에 들어간다.
2. `import`는 Java의 `Map`과 Spring의 두 애너테이션을 가져온다.
3. `@RestController`는 이 클래스를 요청 처리에 사용하고, 메서드가 반환한 값을 HTTP 응답 본문으로 보내도록 지정한다.
4. `@GetMapping("/api/health")`는 이 주소의 GET 요청을 `health()`에 연결한다.
5. `Map<String, String>`은 이름과 값이 모두 문자열인 자료 구조다. 여기서는 `status`라는 이름에 `UP`이라는 값을 넣었다. Spring이 이를 JSON으로 변환한다.

현재 `UP`은 코드에 지정한 고정값이다. 이 실습에서 확인한 범위는 애플리케이션이 HTTP 요청을 받아 응답할 수 있는지까지다.

## 4. 서버 실행과 HTTP 확인

IntelliJ의 첫 번째 터미널에서 실행했다.

```bash
JAVA_HOME=$(/usr/libexec/java_home -v 21) ./gradlew bootRun --args="--server.address=127.0.0.1 --server.port=8081"
```

- `JAVA_HOME=...`은 이 명령에서 Java 21을 선택한다.
- `./gradlew bootRun`은 Gradle을 통해 소스를 컴파일하고 Spring Boot를 실행한다.
- `127.0.0.1`은 현재 Mac을 가리킨다. 이번 실습에서는 포트를 `8081`로 지정했다.

첫 터미널에서 서버가 실행 중인 상태로 두고 두 번째 터미널에서 요청을 보냈다.

```bash
curl -i --max-time 10 http://127.0.0.1:8081/api/health
```

`curl`은 기본적으로 GET 요청을 보낸다. `-i`는 상태 줄과 헤더를 함께 보여주고, `--max-time 10`은 최대 대기 시간을 10초로 제한한다. 실제 응답에서 확인한 핵심 내용은 다음과 같다.

```http
HTTP/1.1 200
Content-Type: application/json

{"status":"UP"}
```

`200`은 요청이 성공했다는 뜻이다. `Content-Type`은 본문 형식이 JSON임을 알려준다. 본문의 값이 Java 코드에서 지정한 값과 같은 것을 확인했다.

## 5. Git에 첫 작업 기록하기

프로젝트 폴더에서 `git init -b main`으로 Git 기록을 시작했다. 소스와 Gradle 설정 등 검토한 12개 파일을 `git add`로 커밋 대상으로 골랐다. `git diff --cached --check`로 변경 사항을 점검한 뒤 다음 명령으로 저장했다.

```bash
git commit -m "Initialize batch monitor with health endpoint"
git status --short --branch
```

첫 커밋은 `f2249dc`다. 커밋 직후 `git status`에는 `main` 브랜치만 표시되어, 커밋할 변경 사항이 남아 있지 않음을 확인했다. 커밋은 작업 시점을 로컬 Git 기록에 남기고, 푸시는 그 기록을 GitHub 같은 원격 저장소로 보낸다.
