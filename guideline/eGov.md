# eGov 프로젝트 개발 가이드라인

이 문서는 전자정부 프레임워크(eGov)를 기반으로 하는 본 프로젝트의 개발 표준을 정의합니다. Java, JSP, Javascript(jQuery 포함), Oracle 환경에 맞춘 코드 작성 규칙, 들여쓰기, SQL 작성 가이드라인을 포함하며, 회의를 통해 수정 및 확정할 예정입니다.

## 1. 코드 작성 기본 원칙

- **가독성**: 코드의 가독성을 우선으로 고려하며, 명확하고 이해하기 쉬운 코드를 작성합니다.
- **일관성**: 팀 내 모든 개발자가 동일한 규칙을 준수하여 코드 스타일의 통일성을 유지합니다.
- **유지보수성**: 코드의 재사용성과 확장성을 고려하여 설계 및 구현합니다.

## 2. Java 코드 작성 규칙

### 2.1. 들여쓰기 및 포맷

- 들여쓰기는 **Tab(1탭)** 을 사용합니다.
- 중괄호(`{}`)는 K&R 스타일을 따릅니다.

```java
public class SampleClass {
    public void sampleMethod() {
        if (condition) {
            // 코드
        } else {
            // 코드
        }
    }
}
```

- 한 줄은 최대 **120자**를 초과하지 않도록 합니다. 긴 줄은 적절히 분리합니다.

### 2.2. 네이밍 규칙

- 클래스명: **PascalCase** (예: `UserService`)
- 메서드명, 변수명: **camelCase** (예: `getUserInfo`, `userName`)
- 상수: **UPPER_SNAKE_CASE** (예: `MAX_RETRY_COUNT`)
- 패키지명: 소문자로, 도메인 기반 구조 사용 (예: `kr.go.example.module`)

### 2.3. 주석

- 모든 클래스와 메서드는 **Javadoc** 형식으로 문서화합니다.

```java
/**
 * 사용자 정보를 조회하는 서비스 클래스
 * @author 작성자
 * @since 2025-04-19
 */
public class UserService {
    /**
     * 사용자 ID로 사용자 정보를 조회
     * @param userId 사용자 ID
     * @return 사용자 정보 객체
     * @throws Exception 조회 실패 시
     */
    public User getUserById(String userId) throws Exception {
        // 구현
    }
}
```

- 구현 코드 내 주석은 필요한 경우에만 간결하게 작성합니다.

### 2.4. 예외 처리

- 예외는 구체적으로 처리하며, 일반적인 `Exception` catch는 피합니다.
- eGov 프레임워크의 `EgovException`을 활용하여 표준화된 예외 처리를 구현합니다.

```java
try {
    // 비즈니스 로직
} catch (EgovException e) {
    LOGGER.error("Error occurred: {}", e.getMessage());
    throw new EgovException("사용자 조회 실패", e);
}
```

## 3. JSP 작성 규칙

- **스크립틀릿 사용 최소화**: 스크립틀릿(`<% %>`) 대신 JSTL과 EL을 사용합니다.

```jsp
<% String name = (String) request.getAttribute("name"); %>

<%-- 잘못된 예 --%>
<%= name %>

<%-- 올바른 예 --%>
${name}
```

- JSP 파일은 **UTF-8** 인코딩을 사용하며, 상단에 다음 지시자를 포함합니다:

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
```

## 4. Javascript 코드 작성 규칙

### 4.1. 들여쓰기 및 포맷

- 들여쓰기는 \*\*Tab(1탭)\*\*을 사용합니다.
- 세미콜론(`;`)은 모든 문장 끝에 추가합니다.

```javascript
function fetchData() {
    $.ajax({
        url: '/api/data',
        method: 'GET',
        success: function(data) {
            console.log(data);
        }
    });
}
```

### 4.2. 네이밍 규칙

- 함수명, 변수명: **camelCase** (예: `fetchData`, `userData`)
- 상수: **UPPER_SNAKE_CASE** (예: `API_BASE_URL`)

### 4.3. 모듈화

- **jQuery**를 기본 라이브러리로 사용하며, 공통 기능은 별도의 `.js` 파일로 분리하여 관리합니다.
- eGov 프레임워크의 스크립트 로딩 방식을 따릅니다.

```javascript
$(document).ready(function() {
    $('#submitButton').on('click', function() {
        // 버튼 클릭 이벤트 처리
    });

    $(document).on('click', '#anotherButton', function() {
        // 버튼 클릭 이벤트 처리 2 - 동적
    });
});

$(function() {
    // document ready 축약형
});
```

## 5. SQL 작성 규칙 (Oracle)

### 5.1. 포맷팅

- SQL 키워드, 테이블명, 컬럼명 등 모든 요소는 **대문자**로 작성합니다.
- 들여쓰기는 *<u>작성 안(5.4)</u*>에 맞게 탭(Tab) 혹은 스페이스(Space Bar)를 사용합니다.
- 긴 쿼리는 가독성을 위해 적절히 줄바꿈합니다.

### 5.2. 네이밍 규칙

- 테이블명: **대문자** (예: `USERS`, `ROLES`)
- 컬럼명: **대문자, SNAKE_CASE** (예: `USER_ID`, `CREATE_DATE`)\
  (HashMap으로 반환 시, camelCase로 자동 변환. 예: userId, createDate)
- 인덱스: `IDX_테이블명_컬럼명` (예: `IDX_USERS_USER_ID`)

### 5.3. 작성 원칙

- **바인드 변수**는 MyBatis의 `#{}` 형식을 사용하며, 변수명은 **camelCase**로 작성합니다 (예: `#{userId}`).

```sql
SELECT USER_ID, USER_NAME FROM USERS WHERE USER_ID = #{userId}
```

- 불필요한 `SELECT *` 사용을 피하고, 필요한 컬럼만 명시합니다.
- 조인은 **ANSI 조인** 구문(`INNER JOIN`, `LEFT JOIN` 등)을 사용합니다.

### 5.4. 작성 안

```sql
-- 첫 번째 (들여쓰기를 Tab으로 처리)
SELECT
    U.USER_ID,
    R.ROLE_NAME
FROM
    USERS U
INNER JOIN
    ROLES R
        ON U.ROLE_ID = R.ROLE_ID
WHERE
    U.USER_ID LIKE '%TEST%'

-- 두 번째 (중앙에 맞추는 형태이며, space로 공백을 처리)
SELECT U.USER_ID,
       R.ROLE_NAME
  FROM USERS U
 INNER JOIN ROLES R
    ON U.ROLE_ID = R.ROLE_ID
 WHERE U.USER_ID LIKE '%TEST%'
```

- **논의 필요**: 두 가지 작성 안이나 혹은 추가적인 안이 있으면 고려하여 선택.

## 6. 로그 작성

- eGov 프레임워크의 로그 설정을 따르며, **SLF4J**를 사용합니다.
- 로그는 다음 두 가지 방식 중 하나를 사용하여 작성합니다:
  1. **전통적인 방식**: 클래스에 `private static final Logger LOGGER = LoggerFactory.getLogger(클래스명.class);`를 선언합니다.
  2. **Lombok** `@Slf4j`: `@Slf4j` 어노테이션을 사용하며, `lombok.config` 파일에 `lombok.log.fieldName = LOGGER`를 설정하여 로그 필드명을 `LOGGER`로 통일합니다.
- `lombok.config` 설정 예:

```
lombok.log.fieldName = LOGGER
# 혹은
lombok.log.slf4j.fieldName=LOGGER
```

- 로그 레벨은 적절히 설정하며, 디버깅용 로그는 `DEBUG`로 설정합니다.
- 로그 작성 예:

```java
// 전통적인 방식
public class ExampleController {
    private static final Logger LOGGER = LoggerFactory.getLogger(ExampleController.class);

    public void exampleMethod() {
        LOGGER.info("User login attempt: {}", userId);
        LOGGER.error("Failed to process request: {}", e.getMessage(), e);
    }
}
```

```java
// Lombok @Slf4j 방식
@Slf4j
public class ExampleController {
    public void exampleMethod() {
        LOGGER.info("User login attempt: {}", userId);
        LOGGER.error("Failed to process request: {}", e.getMessage(), e);
    }
}
```

## 7. 협업

- 모든 코드는 **SVN**을 통해 관리하며, 커밋 메시지는 명확하게 작성합니다.
  - 예: `# 2025.04.21 XXX - 기능 추가`
- 코드 리뷰는 프로젝트 일정 상 특별한 상황(예: 주요 기능 변경, 버그 수정 검증)이 아닌 경우 진행하지 않습니다. 대신, 단위 테스트 작성과 실행을 통해 코드 품질을 보장합니다.

## 8. 기타

- 필수값(class=required), 입력값 검증
- SQL 인젝션 방지
- 로그인, 세션, 권한
- 코드, 프로그램, 메뉴 등 관리
- 공통 함수
- 그리드(SBGrid)

## 9. 논의 및 수정 제안

- 본 가이드라인은 초안이며, 피드백을 반영하여 수정될 예정입니다.
- 다음 회의에서 논의할 주요 항목:
  - SQL 쿼리 작성 규칙
  - jQuery 외 추가 Javascript 라이브러리 사용 여부
  - 추가적인 규칙
