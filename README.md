# Validation
bean을 사용하지않은 Validation과 bean을 사용한 Validation


bean을 사용하지않은 Validation<br/>

참고: 클라이언트 검증, 서버 검증<br/>
클라이언트 검증은 조작할 수 있으므로 보안에 취약하다.<br/>
서버만으로 검증하면, 즉각적인 고객 사용성이 부족해진다.<br/>
둘을 적절히 섞어서 사용하되, 최종적으로 서버 검증은 필수<br/>
API 방식을 사용하면 API 스펙을 잘 정의해서 검증 오류를 API 응답 결과에 잘 남겨주어야 함<br/>
![image](https://user-images.githubusercontent.com/69129562/204004881-d6f6e777-11e0-4f83-8ae2-85b1a2b40bfd.png)
![image](https://user-images.githubusercontent.com/69129562/204005206-5bf2339e-65d6-4f0e-b280-10446b97778a.png)
![image](https://user-images.githubusercontent.com/69129562/204008106-80fb2d8b-7f5d-40b0-8d76-7bbb44f6b429.png)
주의<br/>
BindingResult bindingResult 파라미터의 위치는 @ModelAttribute Item item 다음에 와야 한다.<br/>
![image](https://user-images.githubusercontent.com/69129562/204008249-10ce2209-79cc-4680-b322-343cbc105a8a.png)
![image](https://user-images.githubusercontent.com/69129562/204008396-4548cda2-1b2f-4cc9-8495-3aa06b9a1e49.png)
![image](https://user-images.githubusercontent.com/69129562/204008810-9116efbf-7d00-45c3-9b4e-3e3a736c19d0.png)
타임리프 스프링 검증 오류 통합 기능<br/>
타임리프는 스프링의 BindingResult 를 활용해서 편리하게 검증 오류를 표현하는 기능을 제공한다.<br/>
#fields : #fields 로 BindingResult 가 제공하는 검증 오류에 접근할 수 있다.<br/>
th:errors : 해당 필드에 오류가 있는 경우에 태그를 출력한다. th:if 의 편의 버전이다.<br/>
th:errorclass : th:field 에서 지정한 필드에 오류가 있으면 class 정보를 추가한다.<br/>
<br/>
예) @ModelAttribute에 바인딩 시 타입 오류가 발생하면?<br/>
BindingResult 가 없으면 400 오류가 발생하면서 컨트롤러가 호출되지 않고, 오류 페이지로
이동한다.<br/>
BindingResult 가 있으면 오류 정보( FieldError )를 BindingResult 에 담아서 컨트롤러를
정상 호출한다.<br/>
<br/>
BindingResult에 검증 오류를 적용하는 3가지 방법<br/>
@ModelAttribute 의 객체에 타입 오류 등으로 바인딩이 실패하는 경우 스프링이 FieldError 생성해서
BindingResult 에 넣어준다.<br/>
개발자가 직접 넣어준다.<br/>
Validator 사용<br/>
<br/>
타입 오류 확인<br/>
숫자가 입력되어야 할 곳에 문자를 입력해서 타입을 다르게 해서 BindingResult 를 호출하고
bindingResult 의 값을 확인해보자.<br/>
<br/>
BindingResult와 Errors<br/>
org.springframework.validation.Errors<br/>
org.springframework.validation.BindingResult<br/>
BindingResult 는 인터페이스이고, Errors 인터페이스를 상속받고 있다.<br/>
실제 넘어오는 구현체는 BeanPropertyBindingResult 라는 것인데, 둘다 구현하고 있으므로
BindingResult 대신에 Errors 를 사용해도 된다.<br/> Errors 인터페이스는 단순한 오류 저장과 조회
기능을 제공한다.<br/> BindingResult 는 여기에 더해서 추가적인 기능들을 제공한다.<br/> addError() 도
BindingResult 가 제공하므로 여기서는 BindingResult 를 사용하자. 주로 관례상 BindingResult 를
많이 사용한다.<br/>
<br/>
![image](https://user-images.githubusercontent.com/69129562/204011896-f4b6dcbe-092a-4846-bc6b-7c16eff5042e.png)
![image](https://user-images.githubusercontent.com/69129562/204011974-de7da2ff-e08f-4994-b81d-bf8fab490ba1.png)
<br/>
오류 발생시 사용자 입력 값 유지<br/>
new FieldError("item", "price", item.getPrice(), false, null, null, "가격은 1,000 ~
1,000,000 까지 허용합니다.")<br/>
사용자의 입력 데이터가 컨트롤러의 @ModelAttribute 에 바인딩되는 시점에 오류가 발생하면 모델
객체에 사용자 입력 값을 유지하기 어렵다.<br/> 예를 들어서 가격에 숫자가 아닌 문자가 입력된다면 가격은
Integer 타입이므로 문자를 보관할 수 있는 방법이 없다.<br/> 그래서 오류가 발생한 경우 사용자 입력 값을
보관하는 별도의 방법이 필요하다.<br/> 그리고 이렇게 보관한 사용자 입력 값을 검증 오류 발생시 화면에 다시
출력하면 된다.<br/>
FieldError 는 오류 발생시 사용자 입력 값을 저장하는 기능을 제공한다.<br/>
여기서 rejectedValue 가 바로 오류 발생시 사용자 입력 값을 저장하는 필드다.<br/>
bindingFailure 는 타입 오류 같은 바인딩이 실패했는지 여부를 적어주면 된다. 여기서는 바인딩이
실패한 것은 아니기 때문에 false 를 사용한다.<br/>
<br/>
타임리프의 사용자 입력 값 유지<br/>
th:field="*{price}"<br/>
타임리프의 th:field 는 매우 똑똑하게 동작하는데, 정상 상황에는 모델 객체의 값을 사용하지만, 오류가
발생하면 FieldError 에서 보관한 값을 사용해서 값을 출력한다.<br/>
스프링의 바인딩 오류 처리<br/>
타입 오류로 바인딩에 실패하면 스프링은 FieldError 를 생성하면서 사용자가 입력한 값을 넣어둔다.<br/>
그리고 해당 오류를 BindingResult 에 담아서 컨트롤러를 호출한다.<br/> 따라서 타입 오류 같은 바인딩
실패시에도 사용자의 오류 메시지를 정상 출력할 수 있다.<br/>

public FieldError(String objectName, String field, @Nullable Object
rejectedValue, boolean bindingFailure, @Nullable String[] codes, @Nullable
Object[] arguments, @Nullable String defaultMessage)<br/>
파라미터 목록<br/>
objectName : 오류가 발생한 객체 이름<br/>
field : 오류 필드<br/>
rejectedValue : 사용자가 입력한 값(거절된 값)<br/>
bindingFailure : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값<br/>
codes : 메시지 코드<br/>
arguments : 메시지에서 사용하는 인자<br/>
defaultMessage : 기본 오류 메시지<br/>
FieldError , ObjectError 의 생성자는 codes , arguments 를 제공한다. 이것은 오류 발생시 오류
코드로 메시지를 찾기 위해 사용된다<br/>
ex)<br/>
![image](https://user-images.githubusercontent.com/69129562/204523818-c7332069-0b58-4772-b3f2-62b34ee6c141.png)
<br/>
BindingResult 가 제공하는 rejectValue() , reject() 를 사용하면 FieldError , ObjectError 를
직접 생성하지 않고, 깔끔하게 검증 오류를 다룰 수 있다.<br/>
rejectValue() , reject() 를 사용해서 기존 코드를 단순화해보자.<br/>
![image](https://user-images.githubusercontent.com/69129562/204524845-0aa2a7ec-cedb-485d-876f-f0d84434de95.png)
실행<br/>
오류 메시지가 정상 출력된다. 그런데 errors.properties 에 있는 코드를 직접 입력하지 않았는데
어떻게 된 것일까?<br/>
<br/>
![image](https://user-images.githubusercontent.com/69129562/204525199-5d390adb-b2e6-4c82-960f-e90cff2a41d0.png)\
오류 코드를 만들 때 다음과 같이 자세히 만들 수도 있고,
required.item.itemName : 상품 이름은 필수 입니다.<br/>
range.item.price : 상품의 가격 범위 오류 입니다.<br/>
또는 다음과 같이 단순하게 만들 수도 있다.<br/>
required : 필수 값 입니다.<br/>
range : 범위 오류 입니다.<br/>
단순하게 만들면 범용성이 좋아서 여러곳에서 사용할 수 있지만, 메시지를 세밀하게 작성하기 어렵다.<br/>
반대로 너무 자세하게 만들면 범용성이 떨어진다.<br/> 가장 좋은 방법은 범용성으로 사용하다가, 세밀하게
작성해야 하는 경우에는 세밀한 내용이 적용되도록 메시지에 단계를 두는 방법이다.<br/>
예를 들어서 required 라고 오류 코드를 사용한다고 가정해보자.<br/>
다음과 같이 required 라는 메시지만 있으면 이 메시지를 선택해서 사용하는 것이다<br/>
required: 필수 값 입니다<br/>
그런데 오류 메시지에 required.item.itemName 와 같이 객체명과 필드명을 조합한 세밀한 메시지
코드가 있으면 이 메시지를 높은 우선순위로 사용하는 것이다.<br/>
#Level1<br/>
required.item.itemName: 상품 이름은 필수 입니다.<br/>
#Level2<br/>
required: 필수 값 입니다.<br/>
물론 이렇게 객체명과 필드명을 조합한 메시지가 있는지 우선 확인하고, 없으면 좀 더 범용적인 메시지를
선택하도록 추가 개발을 해야겠지만, 범용성 있게 잘 개발해두면, 메시지의 추가 만으로 매우 편리하게 오류
메시지를 관리할 수 있을 것이다.<br/>
스프링은 MessageCodesResolver 라는 것으로 이러한 기능을 지원한다.<br/>

![image](https://user-images.githubusercontent.com/69129562/204526310-16a9ce36-4a24-449c-b930-f64d1afd328b.png)

![image](https://user-images.githubusercontent.com/69129562/204526697-fce93838-8e9a-4dbd-a4cc-d1abdd127dca.png)


 bean을 사용한 Validation<br/>
 검증 애노테이션
@NotBlank : 빈값 + 공백만 있는 경우를 허용하지 않는다.
@NotNull : null 을 허용하지 않는다.
@Range(min = 1000, max = 1000000) : 범위 안의 값이어야 한다.
@Max(9999) : 최대 9999까지만 허용한다.

검증 순서
1. @ModelAttribute 각각의 필드에 타입 변환 시도
1. 성공하면 다음으로
2. 실패하면 typeMismatch 로 FieldError 추가
2. Validator 적용
바인딩에 성공한 필드만 Bean Validation 적용
BeanValidator는 바인딩에 실패한 필드는 BeanValidation을 적용하지 않는다.
생각해보면 타입 변환에 성공해서 바인딩에 성공한 필드여야 BeanValidation 적용이 의미 있다.
(일단 모델 객체에 바인딩 받는 값이 정상으로 들어와야 검증도 의미가 있다.)
@ModelAttribute 각각의 필드 타입 변환시도 변환에 성공한 필드만 BeanValidation 적용
예)
itemName 에 문자 "A" 입력 타입 변환 성공 itemName 필드에 BeanValidation 적용
price 에 문자 "A" 입력 "A"를 숫자 타입 변환 시도 실패 typeMismatch FieldError 추가
price 필드는 BeanValidation 적용 X
 




