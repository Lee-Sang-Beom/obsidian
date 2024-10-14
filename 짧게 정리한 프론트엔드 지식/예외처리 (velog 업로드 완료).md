
#### 1. 예외 처리

- **예외 처리**는 프로그래밍 언어가 정해놓은 규칙에서 벗어난 오류가 발생했을 때의 행동을 정의하는 것을 의미한다.
	- 예외 처리는 프로그램에서 발생할 수 있는 오류를 적절히 처리하여 프로그램이 중단되지 않고 계속 실행되도록 하는 방법이다.
	- 자바스크립트에서는 `try...catch` 문을 사용하여 예외를 처리할 수 있다.
```ts
try {
    // 오류가 발생할 가능성이 있는 코드
    let result = someFunction();
    console.log(result);
} catch (error) {
    // 오류가 발생했을 때 실행될 코드
    console.error("오류가 발생했습니다:", error);
}
```

#### 2. 예외 전달

- **예외 전달**은 한 함수에서 발생한 예외를 호출한 함수로 전달하는 것을 말한다.
	- 자바스크립트에서는 예외를 명시적으로 전달하지 않으면 기본적으로 상위 호출 스택으로 전달된다.
	- 이를 통해 예외가 발생한 지점에서 직접 처리하지 않고 상위 호출자에게 예외 처리를 맡길 수 있다.

- `throw` 명령문을 통해 예외를 발생시킬 수 있으며, 당시의 콜스택 정보를 갖고 있는 `error`객체를 보내주면 어느 파일의 어느 부분에서 에러가 발생했는지를 확인할 수 있다.
```ts
function innerFunction() {
    throw new Error("내부 함수에서 오류 발생");
}

function outerFunction() {
    try {
        innerFunction();
    } catch (error) {
        console.error("외부 함수에서 오류 처리:", error);
    }
}

outerFunction();
```

#### 3. try-catch-finally 예외 관리 예시

- `try-catch-finally`에 대한 대략적인 설명은 아래와 같다.
	1. **`try` 구문 내**에서는, 예외가 발생할 수 있는 동작을 정의한다.
	2. **`catch` 구문 내**에서는 try구문 내에서 예외 발생 시, 어떻게 처리할 것인지를 처리한다.
	3. **`finally` 구문 내**에서는 try구문 코드의 성공/실패 여부에 상관없이 항상 실행하는 구문을 정의한다. 
	    - 해당 구문은 리소스 해제, 정리 작업, 로그 기록, 외부 연결 종료 등의 상황에서 특히 중요하게 사용될 수 있다.
		    - 예) 자바에서 파일같은 것을 열었을 때, 프로그램 종료 직전 꼭 닫아주는 로직을 예시로 들 수 있다.

```ts
// 사용자 입력을 숫자로 변환하는 함수
function parseNumber(input) {
    const number = parseFloat(input);
    if (isNaN(number)) {
        throw new Error("입력 값이 숫자가 아닙니다.");
    }
    return number;
}

// 두 숫자를 더하는 함수
function addNumbers(a, b) {
    return a + b;
}

// 메인 함수: 사용자 입력을 받아서 더하고 결과를 출력
function main() {
    try {
        const input1 = prompt("첫 번째 숫자를 입력하세요:");
        const input2 = prompt("두 번째 숫자를 입력하세요:");

        const number1 = parseNumber(input1);
        const number2 = parseNumber(input2);

        const result = addNumbers(number1, number2);
        console.log(`결과: ${result}`);
    } catch (error) {
        console.error(`오류가 발생했습니다: ${error.message}`);
    } finally {
        console.log("프로그램이 종료되었습니다.");
    }
}

// 프로그램 실행
main();
```


> 예 : 파일 닫기 
```ts
function readFile(filePath) {
    let file;
    try {
        file = openFile(filePath); // 파일을 엽니다
        const content = file.read();
        console.log(content);
    } catch (error) {
        console.error("파일을 읽는 동안 오류가 발생했습니다:", error);
    } finally {
        if (file) {
            file.close(); // 파일을 닫습니다
            console.log("파일이 닫혔습니다.");
        }
    }
}
readFile('example.txt');
```