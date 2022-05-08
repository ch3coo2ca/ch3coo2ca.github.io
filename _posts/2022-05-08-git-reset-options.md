---
layout: post
title: "Git reset 옵션 (hard, mixed, soft)"
description : "커밋을 취소시키는 git reset 옵션에 대해서 알아보자"
tags: [Git]
---

`git reset` 명령어에는 3가지 옵션이 존재한다. 각 옵션을 어떻게 주느냐에 따라서 활용도가 달라지는데, 로컬 커밋을 아예 제거해버리거나 로컬 커밋을 취소하면서 커밋한 파일들을 다시 작업 디렉토리에 돌려놓을 수 있다.

![git reset flow](https://user-images.githubusercontent.com/38097442/167263780-a45d758e-6ad6-40c6-b489-e11007fa2074.png)
_git reset 옵션별 흐름_

## —hard

> **작업 디렉토리, 스테이징 영역, 커밋 히스토리** 모두 특정 커밋 기준으로 변경된다.
> 

가장 위험하고 강력한 옵션 중 하나이다. 특정 커밋 기준으로 커밋 히스토리가 변경되고, 작업 디렉토리의 내용도 해당 커밋 기준으로 업데이트된다. 이때 작업 디렉토리에서 **저장되지 않은 변경사항이 모두 날아갈 수 있으니 주의해서 사용**해야 한다. 

<br>
바로 직전 커밋에 `--hard` 옵션을 주면 어떻게 되는지 확인해보자. 

먼저 hello.md 파일을 생성하고 커밋한다. 

```bash
git add .
git commit -m 'Create hello.md'
```
<br>
hello.md 파일을 수정 → test.md 파일을 생성 → test.md 파일만 커밋한다. 

```bash
git add ./test.md 
git commit -m 'Create test.md'
```
<br>
`git log --oneline` 으로 커밋 히스토리를 확인한다. 

```bash
$ git log --oneline
f6a2524 (HEAD -> feature) Create test.md
89e73e1 Create hello.md
ebefc40 Initial commit
```
<br>
`git reset --hard HEAD^` 명령어로 이전 커밋을 취소시키고, 커밋 히스토리를 확인한다. 

```bash
$ git reset --hard HEAD^   # 바로 직전 커밋을 취소시킨다.
HEAD is now at 89e73e1 Create hello.md

$ git log --oneline        # 이전 커밋이 히스토리에서 제거된다.
89e73e1 (HEAD -> feature) Create hello.md
ebefc40 Initial commit
```
<br>
`git status` 로 스테이징 영역을 확인한다. 

```bash
$ git status               # 이전 커밋에서 새로 추가한 test.md 파일이 제거된다.
On branch feature
nothing to commit, working tree clean
```
<br>

**`--hard` 옵션을 주면 다음과 같은 변경 사항이 일어난다.**

- 커밋하지 않은 hello.md 파일의 변경 사항은 싹 날아가고, `89e73e1` 커밋할 때의 hello.md 파일 내용으로 변경된다.
- test.md 파일이 작업 디렉토리, 스테이징 영역 어디에도 존재하지 않고 완전히 **삭제**된다.

<br>

예전에 reset 명령어와 익숙하지 않을 때 —hard 옵션 잘못 쳤다가 로컬 파일 수정사항이 싹 날아간 적이 있다🤯 다행히 수정사항이 많지 않아서 다시 돌려놨지만.. 엄청 멘붕했던 기억이 있다. 참고로 `--hard` 옵션으로 삭제된 파일은 `git reflog` 명령어를 사용하면 되돌릴 수 있다. 

이 옵션은 주로 필요없는 로컬 커밋을 날려버리는 용으로 자주 쓰게 되는 것 같다. 꼭 주의해서 사용하자!

<br>

## —mixed

> **스테이징 영역, 커밋 히스토리**가 특정 커밋 기준으로 변경된다.

디폴트 옵션으로 아무 옵션도 주지 않으면 `--mixed` 가 적용된다. 특정 커밋 기준으로 커밋 히스토리가 변경되고, **커밋 된 파일들을 다시 작업 디렉토리로 돌려놓는다.** 즉, stage에 올리기전 상태로 돌아온다.

<br>
바로 직전 커밋에 `--mixed` 기본 옵션을 주면 어떻게 되는지 확인해보자. 

먼저 hello.md 파일을 생성하고 커밋한다. 

```bash
git add .
git commit -m 'Create hello.md'
```
<br>
hello.md 파일을 수정 → test.md 파일을 생성 → test.md 파일만 커밋한다. 

```bash
git add ./test.md 
git commit -m 'Create test.md'
```
<br>
`git log --oneline` 으로 커밋 히스토리를 확인한다. 

```bash
$ git log --oneline
645ebe3 (HEAD -> feature) Create test.md
89e73e1 Create hello.md
ebefc40 Initial commit
```
<br>
`git add ./hello.md` 로 hello.md 파일을 스테이징 영역에 올린다. 

```bash
$ git add ./hello.md
```

<br>
`git reset HEAD^` 명령어로 이전 커밋을 취소시키고, 커밋 히스토리를 확인한다. 

```bash
$ git reset HEAD^   # 바로 직전 커밋을 취소시킨다.
Unstaged changes after reset:
M       hello.md

$ git log --oneline
89e73e1 (HEAD -> feature) Create hello.md
ebefc40 Initial commit
```
<br>
`git status` 로 스테이징 영역을 확인한다. 

```bash
$ git status
On branch feature
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)  
        test.md

no changes added to commit (use "git add" and/or "git commit -a")
```

<br>

**기본 `--mixed` 옵션을 주면 다음과 같은 변경 사항이 일어난다.**

- 커밋하지 않은 hello.md 파일의 변경 사항은 그대로 작업 디렉토리에 존재한다.
- test.md 파일이 다시 작업 디렉토리로 돌아온다.
- 스테이징 영역에 있었던 hello.md 가 unstage 상태로 변경된다.

<br>

## —soft

> 커밋 히스토리만 특정 커밋 기준으로 변경된다. 커밋된 파일들을 커밋하기 전 상태인 **staging 단계로 돌려놓는다**.

<br>
바로 직전 커밋에 `--soft` 옵션을 주면 어떻게 되는지 확인해보자. 

먼저 hello.md 파일을 생성하고 커밋한다. 

```bash
git add .
git commit -m 'Create hello.md'
```
<br>
hello.md 파일을 수정 → test.md 파일을 생성 → test.md 파일만 커밋한다. 

```bash
git add ./test.md 
git commit -m 'Create test.md'
```
<br>
`git log --oneline` 으로 커밋 히스토리를 확인한다. 

```bash
$ git log --oneline
b4ba6c6 (HEAD -> feature) Create test.md
89e73e1 Create hello.md
ebefc40 Initial commit
```
<br>
`git add ./hello.md` 로 hello.md 파일을 스테이징 영역에 올린다. 

```bash
$ git add ./hello.md
```
<br>
`git reset --soft HEAD^` 명령어로 이전 커밋을 취소시키고, 스테이징 영역과 커밋 히스토리를 확인한다. 

```bash
$ git reset --soft HEAD^   # 바로 직전 커밋을 취소시킨다.

$ git status               # staging 상태를 확인한다.
On branch feature
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.md    # hello.md 파일이 그대로 스테이징 영역에 있다.
        new file:   test.md     # test.md 파일이 스테이징 영역으로 돌아온다.

$ git log --oneline        # 커밋 히스토리를 확인한다.
89e73e1 (HEAD -> feature) Create hello.md
ebefc40 Initial commit
```
<br>

**`--soft` 옵션을 주면 다음과 같은 변경 사항이 일어난다.**

- hello.md 파일은 그대로 스테이징 영역에 유지된다.
- test.md 파일이 스테이징 영역으로 돌아온다.

<br>
가장 많이 사용하는 옵션 중 하나인데, 주로 마지막 커밋 되돌려서 누락된 파일이나 라인 추가할 때 유용하게 사용하고 있다.

<br>

## 요약

- `--hard` : 커밋을 취소하면서 해당 커밋 기준으로 커밋 히스토리와 작업 디렉토리가 변경된다.
    
    즉, **로컬 파일 변경 사항이 싹 날아갈 수 있으니 주의해서 사용**한다. 
    
- `--mixed` : 기본 옵션이다. 커밋을 취소하면서 변경 사항을 다시 작업 디렉토리로 돌려놓는다.
- `--soft` : 커밋을 취소하면서 변경 사항을 staging 상태로 돌려놓는다.

<br>

## 참고

- [Git reset](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)
- [[Git] git reset --hard 되돌리기 (feat. git reflog)](https://da-nyee.github.io/posts/git-git-reset-git-reflog/)