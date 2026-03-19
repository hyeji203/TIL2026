# 스프링 웹 MVC 프로젝트의 구조

## 1. 스프링 웹 MVC 개요
스프링 웹 MVC는 스프링이 제공하는 웹 애플리케이션 개발 전용 프레임워크이다.
- MVC(Model-View-Controller) 패턴을 사용한다.
- 모델, 뷰, 컨트롤러 사이의 의존 관계 및 처리 흐름을 스프링 컨테이너가 관리한다.

### 주요 구성 요소
* **디스패처서블릿 (DispatcherServlet):** 클라이언트의 요청을 전달받아 컨트롤러에 전달하고, 반환된 결과를 뷰에 전달하는 등 모든 흐름(프런트 컨트롤러)을 담당한다.
* **핸들러 매핑 (HandlerMapping):** 요청 URL을 처리할 컨트롤러를 결정한다.
* **핸들러 어댑터 (HandlerAdapter):** 결정된 컨트롤러를 호출한다.
* **컨트롤러 (Controller):** 클라이언트 요청을 처리하고 결과를 반환(비즈니스 로직 수행)한다.
* **모델 & 뷰 (ModelAndView):** 처리 결과 정보와 뷰 선택 정보를 담는 객체이다.
* **뷰 리졸버 (ViewResolver):** 컨트롤러의 처리 결과를 보여줄 뷰를 결정한다.
* **뷰 (View):** 컨트롤러의 처리 결과 화면(JSP, HTML 등)을 생성하여 클라이언트에 전송한다.

---

## 2. 스프링 웹 MVC 동작 흐름
1. 클라이언트(웹 브라우저)가 URL로 처리 요청을 보낸다.
2. **DispatcherServlet**이 **HandlerMapping**을 통해 요청 URL과 매핑되는 컨트롤러를 검색하여 반환받는다.
3. **HandlerAdapter**를 통해 해당 **Controller**에 처리를 요청한다.
4. Controller는 요청을 처리한 후 결과(Model)와 뷰 이름을 담은 **ModelAndView**를 반환한다.
5. DispatcherServlet이 **ViewResolver**를 통해 응답을 생성할 View를 검색한다.
6. 검색된 **View**가 모델 데이터를 바탕으로 화면(응답)을 생성하여 클라이언트에 반환한다.

---

## 3. 웹 프로젝트 환경 설정 파일

### `web.xml`
- 웹 프로젝트의 배치 설명자(Deployment Descriptor) 역할을 한다.
- 서블릿 컨텍스트 시작 시 전달할 파라미터(루트 컨텍스트 위치), DispatcherServlet 등록 및 URL 매핑(`/*` 또는 `/`), 인코딩 필터(UTF-8) 등을 설정한다.

### `root-context.xml`
- 뷰와 관련 없는 공통 빈(Service, Repository/DAO, DB 연동, 로그 등)을 설정하여 모든 웹 컴포넌트가 공유하도록 한다.

### `servlet-context.xml`
- 웹 요청을 직접 처리하는 컨트롤러 및 뷰와 관련된 빈(Bean) 객체를 설정한다.
- `<annotation-driven/>`: `@Controller`, `@RequestMapping` 등 어노테이션 기반 처리를 활성화한다.
- `<context:component-scan>`: 지정된 패키지 하위의 클래스들을 검색하여 Bean을 자동 등록한다.
- `<resources>`: 이미지, JS, CSS 등 정적 리소스의 경로를 매핑한다.
- **ViewResolver 설정 (`InternalResourceViewResolver`):** 접두사(`prefix: /WEB-INF/views/`)와 접미사(`suffix: .jsp`)를 설정하여 최종 뷰 경로를 완성한다.

---

## 4. Maven 빌드 설정 (`pom.xml`)
- 프로젝트의 종속성(라이브러리) 및 빌드 방법을 관리하는 파일이다.
- **주요 속성:**
  - `groupId`, `artifactId`, `version`, `packaging` (예: `war`) 등을 정의한다.
  - `<dependencies>` 태그 내에 사용할 라이브러리(spring-webmvc, jakarta.servlet-api 등)를 명시하면 중앙 저장소에서 자동 다운로드된다.
- **종속성 Scope:**
  - `compile` (기본), `provided` (런타임에는 제외), `runtime`, `test` 등으로 라이브러리 적용 범위를 제한할 수 있다.

---

## 5. 컨트롤러와 뷰 구현 예시

### HomeController.java
```java
package com.springmvc.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class HomeController {

    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String home() {
        return "home"; // ViewResolver에 의해 /WEB-INF/views/home.jsp 로 매핑된다.
    }
}

```

@Controller: 이 클래스가 컨트롤러 역할을 수행함을 명시한다.

@RequestMapping: 브라우저 요청 URL(/)과 메서드를 매핑한다.
