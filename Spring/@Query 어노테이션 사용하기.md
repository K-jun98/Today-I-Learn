## @Query 어노테이션 사용하기
~~~
@Query 어노테이션을 사용해 직접 JPQL을 작성하면 JPA구현체에서 자동으로 쿼리 문장을 해석하고 실행한다.
만약 데이베이스를 다른 베이터베이스로 변경할 일이 없다면 직접 해당 데이터베이스에 특화된 SQL을 작성할 수 있다.
~~~

### @Queru 어노테이션을 사용하는 메서드
~~~
@Query("SELECT p FROM Product AS p WHERE p.name = :name")
List<Product> findByName(@Param("name") String name);
~~~
`Query`를 사용하면 엔티티 타입이 아니라 원하는 칼럼의 값만 추출 가능
~~~
@Query("SELECT p.name, p.price, p.stock FROM Product p WHERE p.name = :name")
List<Object[]> findByNameParam2(@Param("name") String name);
~~~

### 문제점
- 메서드의 이름을 기반으로 생성하는 JPQL의 한계는 `@Query`를 통해 대부분 해소 가능하지만,<br>
`직접 문자열`을 입력하기 때문에 컴파일 시점에 에러를 잡지 못하고 `런타임 에러`가 발생할 수 있음.


### `Referense`
[스프링부트 핵심가이드](http://www.yes24.com/Product/Goods/110142898)