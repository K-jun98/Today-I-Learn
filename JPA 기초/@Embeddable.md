# @Embeddable

 <img width="550" alt="스크린샷 2023-01-21 오후 3 45 42" src="https://user-images.githubusercontent.com/101342145/213847488-bd1d6e4f-5ab9-4ff6-8b2c-f1ceb5540d5f.png">

- 엔티티가 아닌 타입을 한 개 이상의 필드와 매핑할 때 사용
  - Address
- 엔티티의 한 속성으로 @Embeddable 적용 타입 사용


<img width="550" alt="스크린샷 2023-01-21 오후 3 49 27" src="https://user-images.githubusercontent.com/101342145/213847725-ae43dd56-8b41-4371-becf-6b1e62cad040.png">

```java
Address address = new Address("주소1","주소2","주소3");
Hotel hotel = new Hotel("H00",address);
jpa.persist(hotel);
```
실행하게 되면 

<img width="400" alt="스크린샷 2023-01-21 오후 3 55 15" src="https://user-images.githubusercontent.com/101342145/213847808-4a55e51d-b8f9-445e-bc3c-ba0cdb53cf79.png">

hotel_info 테이블에 위 예시처럼 컬럼과 값이 입력 됨. 


### 조회

```java
Hotel totel = jpa.find(Hotel.class, "H00");
logger.info("주소: {}" , hotel.getAddress());
// 출력
주소: Address{address1 = "주소1", address = "주소2", zipcode="주소3"}
```

### null

```java
Address address = null;
Hotel hotel = new Hotel("H00",address);
jpa.persist;
```

<img width="400" alt="스크린샷 2023-01-21 오후 4 01 03" src="https://user-images.githubusercontent.com/101342145/213848059-8b6ca920-0f85-4b26-9ce9-099a4b99f574.png">

```java
Hotel hotel = jpa.find(Hotel.class, "H00");
boolean nullAddr = hotel.getAddress() == null; // true
```

### 같은 @Embeddable 타입 팔드가 두 개면?

```java
@Entity
public class Employee {
@Id
private String id;
@Embedded private Address homeAddress;
@Embedded private Address workAddress;
// MappingException 발생
```

- @AttributeOverride으로 설정 재정의
```java
@Entity
public class Employee {
    @Id
    private String id;
    @Embedded
    private Address homeAddress;
    @AttributeOverrides (
        @AttributeOverride(name = "addressI", column = @Column (name = "waddr1")),
        @AttributeOverride(name = "address2", column = @Column(name = "waddr2")),
        @AttributeOverride(name = "zipcode", column = @Column(name = "wzipcode"))
    })
    @Embedded
    private Address workAddress;
```

### 정리
- @Embeddable을 사용하면 모델을 더 잘 표한할 수 있음
  - 개별 속성을 모아서 이해 -> 타입으로 더 쉽게 이해
    - (addr1, addr2, zipcode)를 모아서 '이게 주소구나' -> '주소'네


### `Referense`
[최범균님 YouTube](https://www.youtube.com/watch?v=WtS5IszIueA&list=PLwouWTPuIjUi9Sih9mEci4Rqhz1VqiQXX&index=6)