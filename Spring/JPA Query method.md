# 쿼리 메서드 살펴보기
~~~
리포지토리는 `JpaRepository`를 상속받는 것만으로도 다양한 CRUD 메서드를 제공하지만 기본 메서드들은 식별자 기반으로 생성되기 때문에
결국 별도의 메서드를 정의해서 사용하기 위해 쿼리 메서드를 사용한다.
~~~

## 쿼리 메서드 생성
쿼리 메서드는 동작을 경정하는 `주제`와 `서술어`로 구분한다. find...By , exist...By 와 같은 키워드로 쿼리의 주제를 정하며 `By`는 서술어의 시작을 나타내는 구분자 역할을 한다.
서술어 부분은 검색 및 정렬 조건을 저징하는 영역이다.
<br> <br>

### `exists ... By`
- 특정 데이터가 존재하는지 확인하는 키워드
~~~
// exists...By
boolean existsBynumber(Long number);
~~~

<br>

### `count...By`
- 조회 쿼리를 수행한 후 쿼리 결과로 나온 레코드의 개수를 리턴
~~~
// count...By
long countByName(String name);
~~~

<br>

### `delete...By , romove...By`
- 삭제 쿼리를 수행함, 리턴 타입이 없거나 삭제한 횟수를 리턴
~~~
// delete...By , remove...By
void deleteByNumber(Long number);
long removeByName(String name);
~~~

<br>

### `...First<Number>... , ...Top<Number>...`
- 한 번의 동작으로 여러 건을 조회할 때 사용하며 단 건으로 조회하기 위해서는 `Number`를 생략
~~~
// ...First<Number>... , ...Top<Number>...
List<Product> findFirst5ByName(String name);
List<Product> findTop10ByName(String name);
~~~

<br>

## 조건자 키워드

### `Is`
- 값의 일치를 조건으로 사용하는 조건자 키워드, 생략되는 경우가 많고 `Equals`와 동일한 기능을 수행
~~~
// findByNumber 메서드와 동일하게 동작
Product findByNumberIs(Long number);
Product findByNumberEquals(Long number);
~~~

<br>

### `(Is)Not`
- 값의 불일치를 조건으로 사용하는 조건자
~~~
Product findByNumberIsNot(Long number);
Product findByNumberNot(Long number);
~~~

<br>

### `(Is)Null, (Is)NotNull`
~~~
List<Product> findByUpdateAtNull();
List<Product> findByUpdatedAtIsNull();
List<Product> findByUpdatedAtNotNull();
List<Product> findByUpdatedAtIsNotNull();
~~~

<br>

### `True, False`
- boolean 타입으로 지정된 컬럼값을 확인하는 키워드
~~~
Product findByisActiveTrue();
Product findByisActiveFalse();
~~~

<br>

### `And,Or`
- 여러 조건을 묶을 때 사용
~~~
Product findByNumberAndName(Long number, String name);
Product findByNumberOrName(Long number, String name);
~~~

<br>

### `GreaterThan, LessThan, Between`
- 숫자나 datetime 컬럼을 대상으로 한 비교 연산에 사용할 수 있는 키워드 경곗값을 포함 하려면 `Equal`키워드를 추가
~~~
List<Product> findByPriceGreaterThan(Long price);
List<Product> findByPriceGreaterThanEqual(Long price);
List<Product> findByPriceBetwenn(Long lowPrice, Long  highPrice);
~~~

<br>

### `StartingWith(==StartsWith), EndingWith(==EndsWith), Containing(==Contains), Like`
- 컬럼값에서 일부 일치 여부를 확인하는 조건자 키워드, SQL 쿼리문에서 값의 일부를 포함하는 값을 추출할 대 사용하는 
~~~
List<Product> findByNameLike(String name);
List<Product> findByNameContains(String name); // %word%
List<Product> findByNmaeStartsWith(String nmae); // %word
List<Product> findByNameEndsWith(String name); // word%
~~~