<aside>
💡 Union Type은 TypeScript가 가지는 타입 중 하나로 하나의 값이 여러개의 타입을 가지는 경우 사용한다. OR 연산자처럼 ‘A이거나 B이다’ 라는 의미를 갖는다.

</aside>

## 9.1 유니온타입 지정

파이프 문자를 추가해 여러 개의 타입을 지정해 줄 수 있다. OR 연산자와 같이 `A | B`로 표현이 된다.

> 예제 9-1

```tsx
let text: string | number = 22;
text = "22";
```

## 9.2 인터페이스 유니온

유니온 타입이 인터페이스를 연결했을 때 모든 타입의 공통 속성에만 접근할 수 있다.

> 예제 9-2

```tsx
interface Ujin {
  name: string;
  age: number;
}

interface Dabin {
  name: string;
  character: string;
}

function combine(person: Ujin | Dabin) {
  person.name; // 정상 동작
  person.age; // 타입오류
  person.character; // 타입오류
}
```

위 코드를 보면 `combine()` 함수 매개변수의 타입을 `Ujin`, `Dabin` 인터페이스의 유니온 타입으로 지정하였다. 매개변수의 타입이 `Ujin`도 될 수 있고 `Dabin`도 될 수 있으니 객체의 속성인 `name`, `age`, `character` 모두 사용할 수 있을 것이라 생각하지만, `combine()` 함수를 호출할 때 어떤 타입이 올지 알 수 없기 때문에 어떤 타입이 들어오든 오류가 안 나는 방향으로 타입을 추론하게 된다. 모든 타입의 공통적인 속성에만 접근할 수 있기 때문에 `person.name`은 정상 작동하지만, `person.age`와 `person.character`는 타입 오류가 난다. 이때 필요한 것이 유니온 타입가드이다.

## 9.3 유니온 타입 가드

유니온 타입으로 선언한 변수나 객체를 사용해야 할 경우 그대로 사용하면 오류가 날 수 있다. 타입스크립트는 유니온 타입을 이해할 뿐 유니온 타입 내에 무엇이 있는지는 분석하지 못한다. 이런 경우 런타임 타입 검사를 추가하여 타입이 어느 쪽에 해당하는지 판정을 해줘야한다. 이러한 과정을 타입 가드라고 한다.

### 9.3.1 원시 타입 식별

원시 타입은 `typeof` 연산자를 이용해 타입 검사를 할 수 있다.

> 예제 9-3

```tsx
function combine(input1: number | string, input2: number | string) {
	let result;
	if (typeof input1 === 'number' && typeof input2 === "number') {
		result = input1 + input2;
	} else {
		result = input1.toString() + input2.toString();
	}
	return result;
}

console.log(combine('hello', 'world')) // helloworld
console.log(combine(10, 10)) // 20
```

### 9.3.2 **클래스 객체 식별**

생성자 함수를 반환하는 class 객체는 `typeof`로 검사하면 ‘object’만을 반환하기 때문에 `instanceof` 연산자를 이용해 타입 검사를 한다. `instanceof` 연산자는 생성자의 프로토타입이 객체의 프로토타입 체인에 있는지 검사한다.

> 예제 9-4

```tsx
class Ujin { }
class Dabin { }

const combine(user: Ujin | Dabin) {
	if (user instanceof Ujin) {
		user.jjeu_jjeu()  // user가 Ujin 클래스의 객체
	} else {
		user.bong_gug()   // user가 Dabin 클래스의 객체
	}
}
```

### 9.3.3 일반 객체 식별

자바스크립트의 객체리터럴과 달리 타입스크립트에서는 객체 타입을 지정할 때 인터페이스를 사용하여 객체의 모양을 지정할 수 있다.

> 예제 9-5

```tsx
interface Cat {
  meow(): string;
}

interface Dog {
  bowwow(): string;
}
```

```tsx
function checkType(pet: Cat | Dog) {
  if ("meow" in pet) {
    (pet as Cat).meow();
  } else {
    (pet as Dog).bowwow();
  }
}
```

if 문의 조건을 통해서 pet 타입을 체크였지만 if 문 내부에서 as 문을 통해 한 번 더 어떤 객체인지 알려주어야 한다.

### 9.3.4 null 체크

문자열의 값이 존재하지 않는 경우에 사용한다. string | null이라고 유니온 타입을 지정했을 때 값이 null이어서 string으로 사용할 수 없거나 null이 아닌 경우 예외 처리를 하고 싶을 때 사용한다.

> 예제 9-6

```tsx
function nullCheck(val: string | null): number {
  if (val != null) {
    return val;
  } else {
    return 0;
  }
}
```
