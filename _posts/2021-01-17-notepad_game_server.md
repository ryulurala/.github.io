---
title: "MMO 게임 서버 개발 메모장 by C#"
excerpt: "dotnet framework 사용 by C# + VSCode(CLI)"
category: 서버 공부
tags: [Game Server, C#, CLI, dotnet framework]
toc: true
toc_sticky: true
---

## 개념 정리

### 동기 vs 비동기

|                                    | 동기 | 비동기 |
| :--------------------------------: | :--: | :----: |
|     요청과 결과가 동시에 발생      |  O   |   X    |
| 함수의 결과가 호출한 함수에서 처리 |  O   |   X    |

### Blocking vs Non-blocking

- Blocking  
  : 내가 일을 하고 있을 때, 내가 제어권을 갖는다.

- Non-blocking  
  : 내가 일을 하고 있을 때, 날 호출한 쪽이 제어권을 갖는다.

### Call-back(콜백 함수)

- 어떤 이벤트가 발생했을 때, 수행해야 하는 함수

#### Example 1: delegate(대리자) 이용

- `=`, `+=`, `-=` 로 참조 가능
  - `=`: `new` 대신 사용
  - `+=` / `-=`: delegate chain `등록` / `취소`

```cs
using System;

namespace DelegateExample
{
    class Program
    {
        delegate void MyDelegate();   // 대리자 정의

        static void Main(string[] args)
        {

            MyDelegate myDelegate;    // 대리자 변수 선언

            myDelegate = A;     // A 함수 등록, 처음에만 '=' 연산자 오버로딩 or new 키워드
            myDelegate += B;    // B 함수 등록
            myDelegate += delegate ()
            {
                Console.WriteLine("Anonymous()");   // 익명 함수 등록
            };
            myDelegate += () =>     // 람다 함수 등록
            {
                Console.WriteLine("Lambda()");
            };

            myDelegate();   // A() -> B() -> Anonymous() -> Lambda() 출력

            // A(), B(), 익명 함수, 람다 함수 모두 콜백 함수로 등록했다.(콜백 체인)
        }

        static void A()
        {
            Console.WriteLine("A()");
        }

        static void B()
        {
            Console.WriteLine("B()");
        }
    }
}
```

#### Example 2: event 이용

- 객체 상태 변화, 이벤트 발생 용도로 사용

- event 는 외부에서 직접 사용이 불가능하다.  
  (static 키워드를 사용하지 않는다면)

- 이벤트 만들기(과정)
  1. delegate 한정자로 델리게이트 선언(클래스 안밖 상관없음)
  2. 델리게이트의 인스턴스를 event 한정자로 선언
  3. 이벤트 핸들러 작성(델리게이트와 일치하는 메소드)
  4. 이벤트 핸들러에 등록(`+=` / `-=`으로 `등록` / `취소` 가능)

```cs
using System;

namespace DelegateExample
{
    class Program
    {
        delegate void MyDelegate();   // 대리자 정의
        static event MyDelegate MyEvent;    // 이벤트 선언

        static void Main(string[] args)
        {
            MyEvent = A;     // A 함수 등록, 처음에만 '=' 연산자 오버로딩
            MyEvent += B;    // B 함수 등록
            MyEvent += delegate ()
            {
                Console.WriteLine("Anonymous()");   // 익명 함수 등록
            };
            MyEvent += () =>     // 람다 함수 등록
            {
                Console.WriteLine("Lambda()");
            };

            MyEvent();   // A() -> B() -> Anonymous() -> Lambda() 출력

            // A(), B(), 익명 함수, 람다 함수 모두 콜백 함수로 등록했다.(콜백 체인)
        }

        static void A()
        {
            Console.WriteLine("A()");
        }

        static void B()
        {
            Console.WriteLine("B()");
        }
    }
}
```

#### Exmaple 3: Action 이용

- Action\<T, T, T, ...>  
  : Action 변수 명: 매개 변수 없음
  : Action\<T1>: 매개 변수 T1  
  : Action\<T1, T2>: 매개 변수 T1, T2  
  : Action\<T1, T2, T3>: 매개 변수 T1, T2, T3

- 무조건 반환 타입은 void 형이다.

- 반환 형이 없이 확실히 없을 때 사용.

```cs
using System;

namespace ActionExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Action myAction;

            myAction = A;     // A 함수 등록, 처음에만 '=' 연산자 오버로딩 or new 키워드
            myAction += B;    // B 함수 등록
            myAction += delegate ()
            {
                Console.WriteLine("Anonymous()");   // 익명 함수 등록
            };
            myAction += () =>     // 람다 함수 등록
            {
                Console.WriteLine("Lambda()");
            };

            myAction();   // A() -> B() -> Anonymous() -> Lambda() 출력

            // A(), B(), 익명 함수, 람다 함수 모두 콜백 함수로 등록했다.(콜백 체인)
        }

        static void A()
        {
            Console.WriteLine("A()");
        }

        static void B()
        {
            Console.WriteLine("B()");
        }
    }
}
```

#### Example 4: Func 이용

- Func\<T, T, T, ..., return type>  
  : Func\<T1>: return T1, 매개 변수 없음  
  : Func\<T1, T2>: 매개 변수 T1 / return T2  
  : Func\<T1, T2, T3>: 매개 변수 T1, T2 / return T3  
  : Func\<T1, T2, T3, T4>: 매개 변수 T1, T2, T3 / return T4

- 반환 타입이 존재해야 함.

- 반환 형이 있고 반환 타입을 알고 있을 때 사용.

```cs
using System;

namespace FuncExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Func<int> myFunc;     // 반환 타입이 있어야 함, <return type>

            myFunc = A;     // A 함수 등록, 처음에만 '=' 연산자 오버로딩 or new 키워드
            myFunc += B;    // B 함수 등록
            myFunc += delegate ()
            {
                Console.WriteLine("Anonymous()");   // 익명 함수 등록
                return 1;       // int 반환
            };
            myFunc += () =>     // 람다 함수 등록
            {
                Console.WriteLine("Lambda()");
                return 1;       // int 반환
            };

            myFunc();   // A() -> B() -> Anonymous() -> Lambda() 출력

            // A(), B(), 익명 함수, 람다 함수 모두 콜백 함수로 등록했다.(콜백 체인)
        }

        static int A()
        {
            Console.WriteLine("A()");
            return 1;       // int 반환
        }

        static int B()
        {
            Console.WriteLine("B()");
            return 1;       // int 반환
        }
    }
}
```

#### 콜백 함수(C# 문법) 정리

|                         |  delegate   |            event            |        Action        |         Func\<T>          |
| :---------------------: | :---------: | :-------------------------: | :------------------: | :-----------------------: |
| delegate 함수 형식 지정 |    필수!    |       delegate에 한정       |          X           |             X             |
|   반환 타입 명시 여부   |    가능     |       delegate에 한정       |  불가능(Only. void)  | 가능(<T, T, return type>) |
|          용도           | type 불명확 | 객체 상태 변화 or 사건 발생 | 반환 type 없음(명확) |   반환 type 있음(명확)    |

---

## 서버 개요

### 서버

- 다른 컴퓨터에서 연결이 가능하도록 대기 상태로 상시 실행 중인 프로그램

### 서버 종류

#### Web Server vs Game Server

|           Web Server            |          Game Server           |
| :-----------------------------: | :----------------------------: |
|            Stateless            |            Stateful            |
|       실시간 상호 작용 X        |       실시간 상호 작용 O       |
| 질의(Query) / 대답(Answer) 형태 | 요청(Request) / 응답(Response) |

#### 1. Web Server(aka. HTTP Server)

- 질의(Query) / 대답(Answer) 형태
- 실시간 상호 작용 X
- `Stateless`: 서버가 (현재 클라이언트의)상태를 모름
- 프레임워크 종류
  - `ASP.NET(C#)`
  - `Spring(Java)`
  - `NodeJS(JavaScript)`
  - `Django`
  - `Flask(Python)`
  - `PHP`
  - ...

#### 2. Game Server(aka. TCP Server, Binary Server, Stateful Server ...)

- 요청(Request) / 응답(Response) 형태
- 실시간 상호 작용 O
- `Stateful`: 서버가 (현재 클라이언트의)상태를 알고 접근도 가능.
- 게임 장르에 따라 요구 사항이 천차만별.
- 최적의 프레임워크라는 것이 존재하기 힘들다.

### Game Server : 식당

|   게임 서버    |               식당               |
| :------------: | :------------------------------: |
| 동시 접속자 수 |             손님 수              |
|   게임 장르    |             인테리어             |
|    직원 수     |            쓰레드 수             |
|   게임 로직    |              요리사              |
|    네트워크    |            서빙 직원             |
|  데이터베이스  |           장부 및 결제           |
|  쓰레드 모델   | 요리사 / 서빙 / 결제 직원들 비율 |
| 네트워크 모델  |          주문하는 방법           |

---

## (CLI)환경 설정

### 설치

- `VSCode(Visual Studio Code)` 다운로드 및 설치
- `C# Extension(VSCode Extension)` 다운로드 및 설치
- `.NET Core SDK(64bits)` 다운로드 및 설치

### 솔루션 및 프로젝트 생성

- `.sln` 파일: 여러 프로젝트를 빌드 용도
- `.csproj` 파일: 프로젝트에 관한 정보로 프로젝트 실행 가능하도록 함.

1. 솔루션 디렉토리(새 폴더) 생성
2. 솔루션 디렉토리에서 Terminal 실행 및 명령어 입력
   - `dotnet new sln` : 솔루션 파일 생성(여러 프로젝트 빌드)
3. 프로젝트 디렉토리(새 폴더) 생성
4. 프로젝트 디렉토리에서 Terminal 실행 및 명령어 입력
   - `dotnet new console` : 콘솔 기반 프로젝트 생성
5. 솔루션 디렉토리에서 Terminal 실행 및 명령어 입력
   - `dotnet sln add "프로젝트명.csproj"` : 솔루션 파일에 프로젝트 등록
     > dotnet sln add aaa/bbb/ccc/test.csproj
   - 부모 디렉토리에서 `dotnet sln add *` 으로 등록가능
     (대신 등록 불가한 것에 대한 에러 조금 발생)
   - `dotnet sln list` 로 등록 여부 확인

### 실행(CLI)

- 프로젝트 파일로 실행

  - 프로젝트 디렉토리에서 Terminal 실행 및 명령어 입력  
    `dotnet run`

- 솔루션 파일로 실행

  - 솔루션 디렉토리에서 Terminal 실행 및 명령어 입력  
    `dotnet run --project "프로젝트명"`
    > dotnet run --project test

### Example

1. 최상위 디렉토리(`Server-Example`) 생성
2. `Server-Example` 하위 디렉토리로 `Server`, `ServerCore`, `DummyClient` 디렉토리를 생성

   - `Server` : 실질적인 서버 - Main() 실행
   - `ServerCore` : Server가 사용할 API 모음
   - `DummyClient` : 많은 Client 접속 예제 - Main() 실행

3. `dotnet new sln`을 통해서 최상위 디렉토리(Server-Example)에 솔루션 파일(`.sln`) 생성
4. `dotnet sln add *`로 해당 폴더부터 하위 폴더 모두를 검사하여 프로젝트 파일(`.csproj`)를 등록
5. `.sln` 파일 위치에서 `dotnet run --project "프로젝트명"` 입력하여 실행

---