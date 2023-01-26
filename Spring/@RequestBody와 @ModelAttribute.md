# @RequestBody와 @ModelAttribute


```java

@ToString
@Getter
@NoArgsConstructor
public class MemberDTO {

    String name;

    int number;

}
```

```java

@RestController
public class HomeController {

    @PostMapping("/modelattribute")
    public ResponseEntity<MemberDTO> createMember1(@ModelAttribute MemberDTO memberDTO) {
        return ResponseEntity.ok(memberDTO);
    }

    @PostMapping("/reqeustbody")
    public ResponseEntity<MemberDTO> createMember2(@RequestBody MemberDTO memberDTO) {
        return ResponseEntity.ok(memberDTO);
    }
}
```

## `1. @RequestBody`
<img width="600" alt="스크린샷 2023-01-26 오후 8 07 41" src="https://user-images.githubusercontent.com/101342145/214821336-8242a12d-7632-47cd-b90c-5c6bf24100f4.png">

`@RequestBody`의 역할은 클라이언트가 보내는 HTTP 프로토콜의 body에 있는 Json,xml을 `Java객체로 변환`해줍니다. 
요청 데이터는 `HttpMessageConverter`를 통해 알맞은 객체로 변환됩니다.

```java

    @Test
    void requestBody() throws Exception {
        MemberDTO memberDTO = new MemberDTO("req", 123);
        String requestBody = objectMapper.writeValueAsString(memberDTO);

        mockMvc.perform(post("/requestbody")
                        .contentType(MediaType.APPLICATION_JSON_VALUE)
                        .content(requestBody))
                .andExpect(status().isOk())
                .andExpect(jsonPath("name").value("req"))
                .andExpect(jsonPath("number").value("123"));
    }
```

setter와 필드를 포함한 생성자가 없어도 위에 테스트는 통과한다. 이유가 무엇일까?
@RequestBody는 `HttpMessageConverter를 통해 JSON값을 Java객체로 역직렬화`를 한다. `역직렬화는 생성자를 거치지 않고 리플렉션`을 통해 객체를 구성하는 메커니즘이다.
직렬화를 하기위해선 `기본 생성자가 필수`이고 따라서 MemberDTO에 `기본생성자를 정의 하지 않으면 바인딩에 실패`한다.

- RequestBody를 사용하면 Json,XML,Text 등의 데이터가 적합한 Java객체로 변환된다.
- RequestBody를 통해 생성될 객체는 바인딩할 생성자나 setter가 필요없다.(기본 생성자는 필요)


## `2. @ModelAttribute`

<img width="600" alt="스크린샷 2023-01-26 오후 9 19 20" src="https://user-images.githubusercontent.com/101342145/214833597-210ca466-d8f6-4c54-a2e5-3cb41008e975.png">

`@ModelAttribute`의 역할은 클라이언트가 보내는 URI 쿼리 메서드를 통해 Java 객체에 바인딩 한다.


```java
    @Test
    void modelAttribute() throws Exception {
        mockMvc.perform(post("/modelattribute")
                        .param("name", "req")
                        .param("number", "123"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("name").value("req"))
                .andExpect(jsonPath("number").value("123"));
    }
```

MemberDTO에는 `@AllArgsConstructor`와 `@Getter` 어노테이션만 추가하고 테스트를 진행하면 통과하게 된다.
만약 @ModelAttribute테스트를 @RequestBody 처럼 테스트하게 되면 바인딩이 되지 않는다. 이유는 @ModelAttribute는 Form 형식의 HTTP
데이터만을 인식해 맵핑한다.
이제 다시 `@AllArgsConstructor`을 삭제하고 테스트를 진행하면 MemberDTO에 제대로 바인딩이 되지 
않은것을 확인 할 수 있다. 그럼 다시 MemberDTO에 `@Setter를 추가하고 테스트를 진행하면 생성자가 있을때 처럼 성공`하게 된다.

- @ModelAttribute를 사용하면 URI 쿼리 메서드를 통해 Java 객체에 바인딩 할 수 있다.
- 객체의 바인딩을 위해서는 필드 생성자 또는 @Setter가 필요하다.