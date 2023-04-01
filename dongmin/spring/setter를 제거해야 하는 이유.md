# 상품 생성 코드에서 setter 제거하기!

`❌ bad`

- `setter`를 무분별하게 사용하는 코드는 데이터 변경에 취약하기 때문에 제거하고 메서드를 만들어서 사용하는 것이 좋다.
- 함께 개발을 진행하는 경우 다른 개발자가 setter를 사용해서 값을 변경할 위험이 있다.
- 강의에서는 편의성을 위해 setter를 사용해서 값을 세팅했지만, setter는 무조건 제거하는 것이 좋다는 영한님 말을 듣고 코드를 고쳐봤다.

```java
@Controller
@RequestMapping("/items")
@RequiredArgsConstructor
public class ItemController {

    private final ItemService itemService;

    @PostMapping("/new")
    public String create(BookForm form) {
        Book book = new Book();
        book.setName(form.getName());
        book.setPrice(form.getPrice());
        book.setStockQuantity(form.getStockQuantity());
        book.setAuthor(form.getAuthor());
        book.setIsbn(form.getIsbn());

        itemService.saveItem(book);
        return "redirect:/";
    }
}
```

`✨ cool`

- 엔티티 클래스 안에 **`정적 팩토리 메서드`** 를 만들어서 Book 객체를 생성하도록 변경
- 컨트롤러에서는 정적 팩토리 메서드로 Book 객체를 생성하도록 변경
- Book 객체의 setter 접근 레벨은 **`PRIVATE`** 로 설정해서 외부에서는 setter 사용을 못하도록 막아놨다.
- Item 객체의 setter 접근 레벨은 **`PROTECTED`** 로 설정했다. **`(Book에서는 접근해야 하기 때문에!)`**

**`Book.java`**

```java
@Entity
@Getter
👉🏻 @Setter(AccessLevel.PRIVATE)
@DiscriminatorValue("B")
@AllArgsConstructor
@NoArgsConstructor
@SuperBuilder
public class Book extends Item {
    private String author;
    private String isbn;

    public static Book createBook(BookForm form) {
        Book book = new Book();
        book.setName(form.getName());
        book.setPrice(form.getPrice());
        book.setStockQuantity(form.getStockQuantity());
        book.setAuthor(form.getAuthor());
        book.setIsbn(form.getIsbn());

        return book;
    }
}
```

**`Item.java`**

```java
@Entity
@Getter
👉🏻 @Setter(AccessLevel.PROTECTED)
@NoArgsConstructor
@AllArgsConstructor
@Inheritance(strategy = SINGLE_TABLE)
@DiscriminatorColumn(name = "dtype")
@SuperBuilder
public abstract class Item extends BaseEntity {

    private String name;
    private int price;
    private int stockQuantity;

    @ManyToMany(mappedBy = "items")
    private List<Category> categories = new ArrayList<>();
}
```

**`ItemController.java`**

```java
@Controller
@RequestMapping("/items")
@RequiredArgsConstructor
public class ItemController {

    private final ItemService itemService;

    @PostMapping("/new")
    public String create(BookForm form) {
        👉🏻 Book book = Book.createBook(form);
        itemService.saveItem(book);
        return "redirect:/";
    }
}
```
