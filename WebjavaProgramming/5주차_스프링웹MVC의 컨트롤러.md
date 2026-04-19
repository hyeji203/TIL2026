# 📘 스프링 웹 MVC 컨트롤러 복습 정리 (직접 정리)

---

## 🎯 이번 단원에서 배운 것
이번 단원에서는 스프링 MVC에서 **컨트롤러의 역할과 요청 처리 방식**을 배웠다.  
특히 URL 요청이 어떻게 컨트롤러 메서드로 연결되는지 흐름 이해가 중요하다.

---

# 1️⃣ 컨트롤러란?

## 📌 개념
컨트롤러는 사용자의 요청을 받아서 처리하고, 그 결과를 뷰로 전달하는 역할을 한다.

👉 쉽게 말하면  
"사용자 요청을 처리하는 중간 관리자"

---

## 📌 역할 정리

- 사용자 요청 받기
- 서비스 호출 (비즈니스 로직)
- 결과 데이터를 모델에 저장
- 뷰 반환

👉 MVC에서 핵심 역할 담당 :contentReference[oaicite:0]{index=0}  

---

# 2️⃣ 컨트롤러 동작 흐름

~~~
사용자 요청 → DispatcherServlet → Controller → Service → Repository → DB
                                              ↓
                                      Model에 데이터 저장
                                              ↓
                                           View 반환
~~~

👉 흐름 이해 중요 (시험 자주 나옴)

---

# 3️⃣ 컨트롤러 구현 과정

## 📌 순서

1. 컨트롤러 클래스 정의  
2. 요청 URL 매핑  
3. 요청 처리 메소드 작성  
4. 모델 + 뷰 반환  

---

## 📌 기본 구조

~~~java
@Controller
public class BookController {

    @RequestMapping(value="/books", method=RequestMethod.GET)
    public String requestBookList(Model model) {
        List<Book> list = bookService.getAllBookList();
        model.addAttribute("bookList", list);
        return "books";
    }
}
~~~

---

# 4️⃣ @Controller

## 📌 개념
- 해당 클래스가 컨트롤러 역할을 한다는 것을 의미

---

## 📌 특징

✔ 스프링 빈으로 등록됨  
✔ 요청 처리 메서드 포함  

---

# 5️⃣ 컨트롤러 등록 방식

## 📌 자동 등록

~~~xml
<context:component-scan base-package="com.springmvc.*" />
~~~

👉 @Controller 붙은 클래스 자동 등록

---

## 📌 수동 등록 (거의 안씀)

~~~xml
<bean class="com.springmvc.controller.BookController" />
~~~

---

# 6️⃣ @RequestMapping (중요)

## 📌 개념
- URL 요청과 메서드를 연결하는 어노테이션

---

## 📌 기본 형태

~~~java
@RequestMapping(value="/books", method=RequestMethod.GET)
~~~

---

## 📌 주요 속성

| 속성 | 의미 |
|------|------|
| value | URL |
| method | HTTP 방식 |
| params | 요청 파라미터 |
| headers | 요청 헤더 |

---

# 7️⃣ 클래스 vs 메서드 매핑

## 📌 클래스에 적용

~~~java
@Controller
@RequestMapping("/books")
public class BookController {
}
~~~

---

## 📌 메서드에 적용

~~~java
@RequestMapping("/all")
~~~

---

## 📌 최종 URL

~~~
/books/all
~~~

👉 클래스 + 메서드 결합

---

# 8️⃣ @RequestMapping 단순화

## 📌 최근 방식

| 어노테이션 | 의미 |
|------------|------|
| @GetMapping | GET |
| @PostMapping | POST |
| @PutMapping | PUT |
| @DeleteMapping | DELETE |

---

## 📌 예시

~~~java
@GetMapping("/all")
~~~

👉 가독성 좋아짐

---

# 9️⃣ 요청 처리 메소드 (핵심)

## 📌 Handler Method
- 실제 요청을 처리하는 메서드

---

## 📌 기본 형태

~~~java
public String methodName(Model model)
~~~

---

## 📌 역할

- 데이터 처리
- 모델에 데이터 저장
- 뷰 이름 반환

---

# 🔟 Model (중요)

## 📌 개념
- 컨트롤러 → 뷰로 데이터를 전달하는 객체

---

## 📌 사용 방법

~~~java
model.addAttribute("key", value);
~~~

---

## 📌 의미
- key = 변수 이름
- value = 데이터

👉 JSP에서 ${key}로 사용

---

# 1️⃣1️⃣ ModelMap

## 📌 특징
- Map 형태로 데이터 저장

👉 Model과 거의 동일

---

# 1️⃣2️⃣ ModelAndView

## 📌 개념
- 모델 + 뷰를 동시에 관리하는 객체

---

## 📌 예시

~~~java
ModelAndView mav = new ModelAndView();
mav.addObject("bookList", list);
mav.setViewName("books");
return mav;
~~~

---

## 📌 특징

✔ 데이터 + 뷰 함께 처리  
✔ 구조 명확  

---

# 1️⃣3️⃣ 핵심 흐름 정리 (시험 필수)

1. 사용자가 URL 요청  
2. DispatcherServlet이 컨트롤러 찾음  
3. 컨트롤러 메서드 실행  
4. Model에 데이터 저장  
5. View 반환  

---

# 🔥 시험 핵심 포인트

✔ @Controller = 컨트롤러 정의  
✔ @RequestMapping = URL 매핑  
✔ @GetMapping = 단순화  
✔ Model = 데이터 전달  
✔ ModelAndView = 데이터 + 뷰  

---

# 💡 내가 이해한 핵심

👉 컨트롤러는 "요청을 받아서 결과를 화면으로 넘기는 역할"  
👉 핵심은 URL → 메서드 연결 구조  