---
layout: post
title: "Git 커밋 되돌리기 (checkout, revert, reset)"
description : "Git에서 커밋을 되돌리는 방법을 알아보자"
tags: [Git]
---

가끔 개발하다 보면 실수로 커밋을 잘못하거나, 커밋에 파일을 누락시키거나, 커밋을 되돌려야 하는 상황이 온다. 커밋을 되돌릴 수 있는 방법 3가지와 각각의 차이에 대해서 알아보자.

![현재 커밋 히스토리](https://user-images.githubusercontent.com/38097442/166206755-9cf6cc87-1ba4-4186-814f-f551cec547e3.png)
_현재 커밋 히스토리_

<br>

## Git checkout

> 원하는 시점으로 돌아가 새로운 브랜치를 따서 새롭게 작업하는 방법이다.
> 기존 브랜치를 버리고 새로운 브랜치에서 작업을 하는 방법이어서 main 같은 공용 브랜치에는 적합하지 않다.

<br>

`79aacb30 : Try something crazy` 커밋을 하기 전으로 되돌려보자. `git checkout` 명령어로 이전 커밋인 `bcbadb77`으로 체크아웃하면 이전 상태로 돌아갈 수 있다. 특정 커밋으로 체크아웃하게 되면 레포지토리가 `detached HEAD` 상태가 되는데, 현재 아무 브랜치에도 속해있지 않다는 의미이다.

detached 상태에서는 커밋을 한다 해도 다시 작업 브랜치로 돌아갈 경우 git의 garbage collector에 의해서 제거된다. 따라서 이전 커밋 상태로 돌아가서 새로운 작업을 이어 나가고 싶은 경우 다음과 같이 터미널에 입력한다.

```bash
git checkout bcbadb77         # 변경사항 이전 커밋으로 돌아간다.
# 또는
git checkout HEAD~2

git checkout -b new-branch    # 새로운 브랜치를 생성하고 체크아웃한다.
```
<br>
위 명령어를 실행하면 `bcbadb77`커밋 기준으로 새로운 브랜치 new-branch가 생성된다. 이제 없애고 싶었던 `79aacb30`이후 커밋이 없는 새로운 커밋 히스토리에서 작업하면 된다.

![특정 커밋으로 돌아가 새로운 브랜치를 생성](https://user-images.githubusercontent.com/38097442/166206769-da0aa542-2eac-45d6-ad2d-9790b5cdc4ec.png)
_특정 커밋으로 돌아가 새로운 브랜치를 생성_

<br>
이 방법은 **이전 브랜치를 버리고 새로운 브랜치에서 작업을 진행**하기 때문에, 이전 브랜치가 계속 필요하거나 **main 같은 공용 브랜치일 경우 checkout을 이용해서 커밋을 되돌리는 방법은 적합하지 않다**. 다른 방법도 알아보자.

<br>

## Git revert

> 커밋을 되돌리는 새로운 커밋을 생성하고, 기존 브랜치에서 계속해서 작업할 수 있다. <br>
공용 브랜치에서 작업을 되돌릴 때 적합한 방법이다.
> 

<br>

이번에는 revert를 사용해서  `bcbadb77`로 커밋을 되돌려보자. 터미널에 다음과 같이 입력한다.

```bash
git checkout feature   # feature 브랜치로 이동한다.
git revert 0518b876    # "Continue doing crazy things" 커밋을 되돌린다.
git revert 79aacb30    # "Try something crazy..!" 커밋을 되돌린다.
```
<br>
feature 브랜치에 revert된 새로운 커밋이 추가된 모습은 다음과 같다.

![되돌리고 싶은 커밋을 revert한 히스토리](https://user-images.githubusercontent.com/38097442/166206804-c20076e2-9750-48cd-be3a-a3772fdd1c8b.png)
_되돌리고 싶은 커밋을 revert한 히스토리_

<br>
제거하려고 한 `79aacb30` 이후 커밋은 그대로 히스토리에 남아있지만, 위에서 본 checkout하는 방법과는 다르게 **기존의 feature 브랜치를 계속해서 사용**할 수 있다.

revert는 여러 사람과 공유하는 레포지토리에서 커밋을 되돌릴 수 있는 이상적인 방법이다. revert를 하게 되면 되돌린 파일이 커밋에 남아있기 때문에, 다른 작업자가 pull 받아서 revert 커밋이 어떤 작업을 되돌리는지 확인할 수 있어서 더 안전하다.

<br>

## Git reset
> 특정 시점으로 커밋을 되돌리고 이후 커밋들은 사라져서 커밋 히스토리가 깔끔해진다. <br>
로컬에서 되돌릴 때 적합한 방법이고, 공용 레포지토리에서 사용할 땐 주의해야 한다.
> 

<br>
이번에는 reset을 사용해서 `bcbadb77` 커밋으로 돌아가 보자. 터미널에 다음과 같이 입력한다.

```bash
git reset bcbadb77   # bcbadb77 이후에 생성된 커밋을 취소한다.
# 또는
git reset HEAD~2  
```

<br>
reset은 변경사항을 되돌릴 수 있는 복잡하지만 다재다능한 명령어다. 다양한 옵션이 있는데 아무 옵션 없이 사용하면 디폴트로 `--mixed` 옵션이 적용된다. 위 명령어를 실행하면 히스토리가 다음과 같이 변경된다.

![특정 커밋 이후의 히스토리를 초기화](https://user-images.githubusercontent.com/38097442/166964478-d57b743a-10d4-4362-9d1c-05a3b6d5b061.png)
_특정 커밋 이후의 히스토리를 초기화_

<br>
feature 브랜치에 `79aacb30 : Try something crazy` 이후 커밋이 히스토리에 더 이상 존재하지 않는 걸 확인할 수 있다. reset으로 되돌리는 방법은 커밋 히스토리를 가장 깔끔하게 만들어준다.

로컬 변경 사항에 reset을 적용하는 건 문제없지만, 공유하는 원격 레포지토리에서는 주의해야 할 점이 있다. 만약 `79aacb30` 커밋이 원격 레포지토리에 푸쉬된 상태이고, 로컬에서 `79aacb30` 를 reset 시킨 상태로 푸쉬하려고 하면 어떻게 될까?

Git은 내가 push 하려는 브랜치가 최신 상태가 아닌 것으로 판단하고 에러가 발생한다. 이런 경우라면, `git revert` 를 사용해서 되돌리는 게 적합하다.


<br>

## Git HEAD 상대참조

위의 예시에서 커밋 해쉬 값을 사용한 명령어도 있고 `git reset HEAD~2` 같이 이상하게 생긴 명령어도 있었다. 매번 커밋 해쉬 값을 입력하는 건 정말 불편하고 가끔 손으로 직접 칠 때 눈이 빠질 것만 같은 경험이 있다 😵‍💫.  HEAD를 이용하면 정말 편하게 커밋 간 이동이 가능하다! 

각 커밋을 HEAD로 표현하면 다음과 같다. 

![git commit heads](https://user-images.githubusercontent.com/38097442/166239881-f23a86c3-3593-41fa-99e4-5a615401553a.png)
_커밋을 HEAD 상대참조로 표현_


### 상대참조 캐럿 (^)

`^` 연산자는 부모를 의미한다.  ^의 개수 만큼 부모를 거슬러 올라간다. 

```bash
git checkout HEAD^   # Try something crazy..! 커밋으로 이동

git checkout HEAD^^  # Add important feature 커밋으로 이동

git checkout HEAD^^^ # Create hello.md 커밋으로 이동

git reset HEAD^      # 바로 직전 커밋을 되돌린다. (HEAD^ 이후 커밋이 초기화 됨)
```

### 상대참조 틸트 (~)

`~` 틸트 연산자는 올라가고 싶은 부모의 개수를 지정할 때 사용한다. 
HEAD~1은 부모, HEAD~2는 조부모를 가리킨다.

```bash
git checkout HEAD~1   # Try something crazy..! 커밋으로 이동

git checkout HEAD~2   # Add important feature 커밋으로 이동
```

<br>

## 요약
- `git checkout` 으로 커밋 히스토리를 탐색할 수 있다.
- `git revert` 는 공용 레포지토리에서 커밋을 되돌릴 때 적합하다.
- `git reset` 은 로컬 커밋을 되돌릴 때 적합하다.

<br>
## 참고

- [Undoing Commits & Changes](https://www.atlassian.com/git/tutorials/undoing-changes)