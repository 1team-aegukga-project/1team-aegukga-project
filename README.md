# 🇰🇷 Aegukga Project (애국가 프로젝트)

> **Aegukga Project**는 팀원 4명이 같은 파일의 같은 위치를 동시에 수정하면서 Git 충돌(Merge Conflict)을 일부러 만들어보고, 이를 직접 해결하는 과정을 경험하는 실습입니다. 애국가 1절 가사를 한 소절씩 나눠 맡아 코드를 완성해보세요!



## 1. 프로젝트 소개

이 프로젝트는 `System.out.println`으로 애국가 1절을 한 줄씩 출력하는 아주 단순한 Java 프로그램입니다. 코드 자체는 어렵지 않지만, **네 사람이 한 파일의 같은 지점을 동시에 고치고 순서대로 push하면서 충돌 → pull → 해결 → 재push의 흐름을 몸으로 익히는 것**이 이 실습의 진짜 목적입니다.

지금까지는 개인 Fork 저장소에서 혼자 작업했다면, 이번에는 **팀원 전체가 하나의 원격 저장소(main 브랜치)를 함께 사용**합니다. 그래서 협업 중 가장 흔하게 겪는 상황, 즉 "누가 push하기 전에 내가 먼저 pull을 안 했다"는 상황을 안전하게 재현해볼 수 있습니다.



## 2. 실습 목표

- `git init` → `git add` → `git commit` → `git push`로 새 프로젝트를 원격 저장소에 올리는 흐름 복습
- `git clone`으로 같은 저장소를 여러 명이 함께 사용하는 방법 익히기
- **동일한 파일의 동일한 위치**를 여러 명이 수정했을 때 발생하는 push 거절과 merge conflict 경험하기
- 충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`)를 직접 읽고, 내용을 이해한 뒤 수동으로 해결하기
- 충돌 해결 후 다시 커밋 → push하고, 나머지 팀원들도 최신 상태로 pull하며 동기화하기



## 3. Sing.java 살펴보기

팀장이 사용할 시작 코드는 아래와 같습니다. (`src/main/java/com/ohgiraffers/aegukga/Sing.java`)

```java
package com.ohgiraffers.aegukga;

public class Sing {

    public static void main(String[] args) {

        // 1절 출력부

        // 후렴구 출력부
        System.out.println("무궁화 삼천리 화려강산" +
                "대한사람 대한으로 길이 보전하세");
    }
}
```

- 후렴구는 이미 완성되어 있습니다.
- **7번째 줄 `// 1절 출력부` 주석 바로 아래**가 우리가 채워야 할 자리입니다. 팀원 4명이 각자 맡은 소절을 이 자리에 `System.out.println(...)`으로 한 줄씩 추가하게 됩니다.
- 애국가 1절 가사(정답 순서):
  1. 동해물과 백두산이
  2. 마르고 닳도록
  3. 하느님이 보우하사
  4. 우리나라 만세



## 4. 사전 준비: 팀 구성과 순서 정하기

4인 1팀을 구성하고, 아래 두 가지를 **가위바위보나 제비뽑기 등으로 랜덤하게** 정합니다.

### 4-1. 소절 배정

4가지 소절을 팀원 4명이 한 개씩, 중복 없이 나눠 맡습니다.

| 담당자 | 소절 |
|---|---|
| 팀원 A | 동해물과 백두산이 |
| 팀원 B | 마르고 닳도록 |
| 팀원 C | 하느님이 보우하사 |
| 팀원 D | 우리나라 만세 |

### 4-2. Push 순서 정하기

1번 ~ 4번까지 **push할 순서**를 정합니다. 이 순서가 바로 "누가 충돌 없이 넘어가고, 누구부터 충돌을 만나는지"를 결정합니다.

> 💡 **왜 커밋 순서가 아니라 push 순서가 중요할까요?** 커밋은 각자의 로컬 저장소 안에서만 일어나는 일이라, 누가 먼저 커밋했는지는 원격 저장소에 아무 영향을 주지 않습니다. 반면 push는 실제로 원격 저장소(모두가 공유하는 main)의 상태를 바꾸는 행동이기 때문에, **push한 순서만이 충돌 발생 여부를 결정**합니다. 그래서 이 실습에서는 커밋은 각자 편한 시점에 하되, push는 정해진 순서를 반드시 지켜주세요.



## 5. 진행 방법

### 5-1. 팀장: 로컬 Git 저장소 만들고 GitHub에 올리기

1. 팀장은 강사가 배포한 시작 프로젝트(`aegukga-project` 폴더)를 받아 로컬에 준비합니다.
2. 해당 폴더에서 Git 저장소를 초기화하고 첫 커밋을 남깁니다.
   ```bash
   $ cd aegukga-project
   $ git init
   $ git add .
   $ git commit -m "Initial commit"
   ```
3. GitHub Organization(`20260728-saltlux-llm-agent-service-1st`) 안에 팀별 원격 저장소를 새로 생성합니다. (저장소 이름 규칙은 강사 안내를 따르세요. 예: `team1-aegukga-project`)
4. 로컬 저장소와 원격 저장소를 연결하고 push합니다.
   ```bash
   $ git remote add origin https://github.com/20260728-saltlux-llm-agent-service-1st/team1-aegukga-project.git
   $ git branch -M main
   $ git push -u origin main
   ```

### 5-2. 팀장: 팀원 초대 (필요한 경우)

Organization 멤버라도 저장소별 접근 권한은 따로 부여해야 할 수 있습니다. push가 안 되는 팀원이 있다면 팀장이 아래 방법으로 초대해주세요.

1. 방금 만든 저장소 페이지에서 **Settings** 탭으로 이동합니다.
2. 왼쪽 메뉴에서 **Collaborators and teams**(또는 Collaborators)를 클릭합니다.
3. **Add people** 버튼을 눌러 팀원 3명의 GitHub 아이디를 검색해 초대합니다.
4. 팀원들은 이메일 또는 GitHub 알림으로 받은 초대를 수락합니다.

### 5-3. 팀원: Clone

나머지 팀원 3명은 팀장이 알려준 저장소 주소로 clone합니다.

```bash
$ git clone https://github.com/20260728-saltlux-llm-agent-service-1st/team1-aegukga-project.git
$ cd team1-aegukga-project
```

### 5-4. 각자 자신의 소절 작성 및 커밋

`Sing.java`를 열어 `// 1절 출력부` 바로 아래에 자신이 맡은 소절을 추가합니다. 예를 들어 "마르고 닳도록"을 맡았다면:

```java
        // 1절 출력부
        System.out.println("마르고 닳도록");

        // 후렴구 출력부
```

작성 후 커밋합니다. (커밋은 push 순서와 상관없이 아무 때나 해도 됩니다)

```bash
$ git add src/main/java/com/ohgiraffers/aegukga/Sing.java
$ git commit -m "1절 가사 추가 (마르고 닳도록)"
```

### 5-5. 정해진 순서대로 Push

4-2에서 정한 순서대로, **한 명씩 차례로만** push를 진행합니다. (동시에 여러 명이 push를 시도하지 않도록 주의)

**1번 순서 팀원**은 원격 저장소에 아직 아무도 손대지 않았으므로 충돌 없이 바로 성공합니다.

```bash
$ git push origin main
# Everything up-to-date 또는 정상적으로 push 완료
```

**2번 순서 팀원부터**는 1번 팀원이 이미 push해둔 상태이므로, 그대로 push를 시도하면 아래처럼 거절당합니다.

```bash
$ git push origin main
# ! [rejected]        main -> main (fetch first)
# error: failed to push some refs to '...'
# hint: Updates were rejected because the remote contains work that you do not have locally.
```

당황하지 말고, 아래 5-6 순서대로 pull → 충돌 해결 → 재push를 진행하면 됩니다.

### 5-6. 충돌 해결하기

1. 원격의 최신 내용을 받아옵니다.
   ```bash
   $ git pull origin main
   ```
   *(만약 "hint: You have divergent branches..." 같은 안내와 함께 merge/rebase 방식을 선택하라는 메시지가 뜬다면, 아래 명령어로 merge 방식을 기본값으로 설정한 뒤 다시 pull하세요. 이 실습에서는 rebase가 아닌 **merge**로 진행합니다.)*
   ```bash
   $ git config pull.rebase false
   $ git pull origin main
   ```
2. 같은 위치(`// 1절 출력부` 바로 아래)를 서로 다르게 수정했기 때문에 자동 병합에 실패하고, `Sing.java` 안에 아래와 같은 **충돌 마커**가 나타납니다.
   ```java
           // 1절 출력부
   <<<<<<< HEAD
           System.out.println("마르고 닳도록");
   =======
           System.out.println("동해물과 백두산이");
   >>>>>>> origin/main

           // 후렴구 출력부
   ```
   - `<<<<<<< HEAD` ~ `=======` : 내가 로컬에서 작성한 내용
   - `=======` ~ `>>>>>>> origin/main` : 원격 저장소(다른 팀원이 먼저 push한 내용)
3. 파일을 직접 열어 **충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`)를 모두 지우고**, 두 내용을 모두 살리되 **애국가 가사 순서에 맞게** 줄을 정렬합니다.
   ```java
           // 1절 출력부
           System.out.println("동해물과 백두산이");
           System.out.println("마르고 닳도록");

           // 후렴구 출력부
   ```
   > IntelliJ IDEA를 사용 중이라면 충돌이 발생한 파일에서 우클릭 → **Git → Resolve Conflicts**를 선택하면 좌우 비교 화면으로 더 편하게 병합할 수 있습니다.
4. 해결한 내용을 저장하고, 병합 커밋을 만듭니다.
   ```bash
   $ git add src/main/java/com/ohgiraffers/aegukga/Sing.java
   $ git commit -m "Merge branch 'main' - 충돌 해결 (마르고 닳도록 순서 반영)"
   ```
5. 이제 다시 push하면 성공합니다.
   ```bash
   $ git push origin main
   ```

### 5-7. 남은 순서 반복

3번, 4번 순서 팀원도 각자 자기 차례가 되면 **5-5 ~ 5-6과 동일한 과정**(push 시도 → 거절 → pull → 충돌 해결 → commit → push)을 반복합니다. 다만 pull 받는 시점의 원격 저장소에는 이미 이전 사람들의 가사가 순서대로 쌓여있으므로, **자신의 소절을 가사 순서상 올바른 위치에 끼워 넣는 것**이 핵심입니다.

예를 들어 3번째 팀원("하느님이 보우하사" 담당)이 충돌을 해결한다면:

```java
        // 1절 출력부
        System.out.println("동해물과 백두산이");
        System.out.println("마르고 닳도록");
        System.out.println("하느님이 보우하사");

        // 후렴구 출력부
```

### 5-8. 전체 팀원 동기화 및 확인

모든 push가 끝나면, 아직 최신 상태를 받지 못한 팀원(예: 1번으로 일찍 push하고 기다리기만 한 팀원)은 마지막으로 한 번씩 pull을 실행해 최종본을 맞춥니다.

```bash
$ git pull origin main
```

이후 각자 `Sing.java`의 `main` 메서드를 실행해, 애국가 1절 네 소절이 올바른 순서로, 이어서 후렴구까지 출력되는지 확인합니다.



## 6. 완성 코드 예시

모든 팀원의 push와 충돌 해결이 끝나면 최종적으로 아래와 같은 모습이 됩니다.

```java
package com.ohgiraffers.aegukga;

public class Sing {

    public static void main(String[] args) {

        // 1절 출력부
        System.out.println("동해물과 백두산이");
        System.out.println("마르고 닳도록");
        System.out.println("하느님이 보우하사");
        System.out.println("우리나라 만세");

        // 후렴구 출력부
        System.out.println("무궁화 삼천리 화려강산" +
                "대한사람 대한으로 길이 보전하세");
    }
}
```

실행 결과:

```
동해물과 백두산이
마르고 닳도록
하느님이 보우하사
우리나라 만세
무궁화 삼천리 화려강산대한사람 대한으로 길이 보전하세
```



## 7. 진행 팁 / 자주 겪는 실수

- **충돌은 실수가 아닙니다.** 오히려 이 실습에서 가장 배워야 할 순간이니, 충돌이 뜨면 당황하지 말고 천천히 마커를 읽어보세요.
- 충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`) 중 하나라도 지우지 않고 커밋하면 코드가 깨집니다. `git add` 전에 파일을 한 번 더 열어 마커가 모두 지워졌는지 확인하세요.
- 병합이 꼬여서 되돌리고 싶다면, 커밋하기 전까지는 아래 명령어로 병합을 취소하고 pull 이전 상태로 돌아갈 수 있습니다.
  ```bash
  $ git merge --abort
  ```
- push는 반드시 정해진 순서를 지켜서 **한 명씩** 진행하세요. 여러 명이 동시에 push를 시도하면 누가 어떤 충돌을 해결해야 하는지 헷갈릴 수 있습니다.
- 자신의 차례가 아닌데 미리 push해버리면 뒷사람이 겪어야 할 충돌 지점이 바뀌어 버립니다. 순서를 지키는 것 자체가 이 실습의 일부라는 점을 기억해주세요.
