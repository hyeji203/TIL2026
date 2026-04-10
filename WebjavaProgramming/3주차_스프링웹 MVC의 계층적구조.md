# 📘 스프링 웹 MVC 계층적 구조 복습 정리

---

## 🎯 학습 목표
- 스프링 웹 MVC의 계층 구조 이해
- 각 계층의 역할과 흐름 파악
- MVC와 계층 구조의 관계 이해
- 실습 예제를 통한 전체 구조 이해

---

# 1️⃣ 계층적 구조 (Layered Architecture)

## 📌 개념
- 애플리케이션을 **기능별로 계층(Layer)으로 분리한 구조**
- 각 계층은 독립적으로 동작하며 느슨하게 결합됨

👉 유지보수성과 확장성을 높이기 위한 구조  

---

## 📌 계층 구조 필요성

❌ 한 곳에 모든 기능 구현 시 문제  
- 코드 복잡성 증가  
- 유지보수 어려움  
- 확장성 부족  
- 중복 코드 발생  

✔ 계층 분리 시 장점  
- 역할 분리  
- 코드 재사용성 증가  
- 유지보수 용이  

---

# 2️⃣ 스프링 MVC 계층 구조

## 📌 전체 구조

~~~
[브라우저]
     ↓
[프레젠테이션 계층]
     ↓
[서비스 계층]
     ↓
[퍼시스턴스 계층]
     ↓
[DB]
~~~

---

## 📌 계층 구성

### 1. 프레젠테이션 계층 (Presentation Layer)
- 사용자와 직접 연결되는 계층
- 요청/응답 처리

구성:
- Controller
- View (JSP)
- Model

---

### 2. 서비스 계층 (Service Layer)
- 비즈니스 로직 처리
- Controller와 Repository 연결

---

### 3. 퍼시스턴스 계층 (Persistence Layer)
- 데이터 접근 담당 (DAO)
- DB와 직접 연결

---

### 4. 도메인 객체 (Domain Object)
- 데이터 저장 객체
- 계층 간 데이터 전달 역할

---

# 3️⃣ 계층 구조 흐름

## 📌 처리 흐름

~~~
Controller → Service → Repository → DB
DB → Repository → Service → Controller → View
~~~

---

## 📌 개발 순서

1. 도메인 객체 정의
2. 퍼시스턴스 계층 구현
3. 서비스 계층 구현
4. 컨트롤러 → 뷰 구현

---

# 4️⃣ 실습 예제 구조 (도서 쇼핑몰)

## 📌 전체 구조

~~~
domain        → Book.java
repository    → BookRepository
service       → BookService
controller    → BookController
view          → books.jsp
~~~

---

# 5️⃣ 도메인 객체

## 📌 Book.java
~~~java
public class Book {
    private String bookId;
    private String name;
    private int unitPrice;

    public Book() {}

    public Book(String bookId, String name, int unitPrice) {
        this.bookId = bookId;
        this.name = name;
        this.unitPrice = unitPrice;
    }
}
~~~

---

# 6️⃣ 퍼시스턴스 계층

## 📌 Repository 인터페이스
~~~java
public interface BookRepository {
    List<Book> getAllBookList();
}
~~~

## 📌 구현 클래스
~~~java
@Repository
public class BookRepositoryImpl implements BookRepository {
    private List<Book> listOfBooks = new ArrayList<>();

    @Override
    public List<Book> getAllBookList() {
        return this.listOfBooks;
    }
}
~~~

---

# 7️⃣ 서비스 계층

## 📌 Service 인터페이스
~~~java
public interface BookService {
    List<Book> getAllBookList();
}
~~~

## 📌 구현 클래스
~~~java
@Service
public class BookServiceImpl implements BookService {

    @Autowired
    private BookRepository bookRepository;

    @Override
    public List<Book> getAllBookList() {
        return bookRepository.getAllBookList();
    }
}
~~~

---

# 8️⃣ 프레젠테이션 계층

## 📌 Controller
~~~java
@Controller
public class BookController {

    @Autowired
    private BookService bookService;

    @RequestMapping(value="/books", method=RequestMethod.GET)
    public String requestBookList(Model model) {
        List<Book> list = bookService.getAllBookList();
        model.addAttribute("bookList", list);
        return "books";
    }
}
~~~

---

## 📌 View (JSP)
~~~jsp
<c:forEach items="${bookList}" var="book">
    <h3>${book.name}</h3>
    <p>${book.author}</p>
</c:forEach>
~~~

---

# 9️⃣ 핵심 어노테이션 정리

| 어노테이션 | 역할 |
|------------|------|
| @Controller | 컨트롤러 |
| @Service | 서비스 계층 |
| @Repository | 데이터 접근 |
| @Autowired | 자동 주입 |

---

# 🔟 핵심 요약

✔ Controller → Service → Repository 흐름  
✔ 계층 분리 = 유지보수 용이  
✔ 도메인 객체 = 데이터 전달 역할  
✔ 스프링 핵심 = 의존성 분리 구조  

---

# 💡 한줄 요약
👉 스프링 구조 = "Controller → Service → Repository → DB 흐름"
