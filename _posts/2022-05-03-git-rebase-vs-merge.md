---
layout: post
title: "Git rebase vs. merge 차이"
description: "Git rebase와 merge 차이에 대해서 알아보자"
tags: [Git]
---

브랜치를 합치는 방법은 두 가지가 있는데 rebase와 merge의 동작 흐름과 차이에 대해서 알아보자. 알고 있는 개념이긴 하지만 이번 기회에 헷갈리는 부분들을 명확히 하고자 정리한다. 이제 헷갈려서 자꾸 구글링에 시간 쓰는 일이 없기를 🙏

아래 그림은 master 브랜치의 `M2` 커밋에서 `feature` 브랜치를 따서 F1, F2 커밋을 한 흐름이다.

![git commit history](https://user-images.githubusercontent.com/38097442/166154027-f166a1d6-169d-492a-9d3b-6c64276aa0a1.png)
_master와 feature 브랜치 커밋 히스토리_


위와 같은 상황에서, 협업하는 동료가 master 브랜치에 새로운 작업 사항 `M3` 커밋을 푸쉬했다고 가정해보자. 만약 feature 브랜치에서 master의 변경 사항을 가져와야 하는 경우 `rebase` 또는 `merge` 명령어를 사용할 수 있다.

<br>

## Git merge

터미널에서 다음과 같이 merge 명령어를 입력한다.

```bash
git checkout feature  # feature 브랜치로 이동한다.
git merge master      # master의 작업 사항을 feature에 머지한다.
```

두 브랜치의 작업 사항을 합치는 **merge 커밋**이 feature 브랜치에 생성된다. 다음과 같은 흐름으로 master가 feature에 합쳐지면서 `F3` merge 커밋이 생성된다.

![merge master into feature](https://user-images.githubusercontent.com/38097442/166154042-9598cc12-bb13-4e1d-b263-19b2108cd318.png)
_master를 feature 브랜치에 merge_

merge의 장점은 브랜치가 합쳐진 이후에도 각각의 커밋이 남아 있어서, 해당 브랜치의 작업 내역을 추적하기 쉽다. 

하지만 브랜치를 합칠 때 마다 불필요한 merge 커밋이 남게되어 로그가 복잡해질 수 있다. 다음은 추가 커밋을 생성하지 않고 브랜치를 합칠 수 있는 rebase에 대해서 알아보자.

<br>

## Git rebase

`rebase`는 이름대로 base를 재설정한다는 의미이다. 브랜치를 딴 시작 지점이 base가 되는데,  `M2` 가 feature 브랜치의 base가 된다.

터미널에서 다음과 같이 rebase 명령어를 입력한다.

```bash
git checkout feature  # feature 브랜치로 이동한다.
git rebase master     # feature 브랜치에서 master를 rebase 한다.
```

rebase 작업은 master 브랜치의 변경 사항을 모두 가져와서 feature 브랜치를 master 끝부분에서 시작하도록 한다. 그리고 항상 변경 사항부터 기존의 feature 커밋이 모두 **새로운 커밋으로 생성**된다.

rebase의 가장 큰 장점은 **커밋 히스토리가 깔끔**해진다는 것인데, **불필요한 merge 커밋이 생성되지 않기 때문**에 다음과 같이 선형의 히스토리를 가지게 된다.

![rebase feature with master](https://user-images.githubusercontent.com/38097442/166154079-81413b17-d752-4440-8012-709a430011b0.png)
_feature 브랜치에서 master를 rebase_


### The Golden Rule of Rebasing

rebase할 때 주의해야 할 점이 있는데, **public 브랜치에서는 rebase를 하지 말아야 한다**는 것이다. 예를 들어 위의 예시와 반대로 master 브랜치에서 feature 브랜치를 rebase하는 경우를 가정해보자.

![rebase master with feature](https://user-images.githubusercontent.com/38097442/166154085-cc1c5f5f-d81a-4d4c-8771-bf78065c3905.png)
_master 브랜치에서 feature를 rebase_

rebase를 하게 되면 위와 같이 master의 모든 커밋이 feature의 끝부분에 위치하게 된다. 여기서 문제점은 다른 팀원들이 master에서 작업을 하는 경우이다. rebase를 하면 새로운 커밋을 생성하기 때문에 다른 팀원들과 master의 이력이 달라지는 상황이 생긴다.

서로 다른 두 브랜치를 동기화하려면 merge를 해야 하는데 역시나 불필요한 merge 커밋이 생기게 되고, 같은 변경 사항을 가진 커밋이 두 세트씩 생성된다. (원본 master 브랜치와 rebase한 master 브랜치)

꼭 rebase를 하기 전에 *“이 브랜치를 바라보고 있는 다른 작업자가 있는가?”* 를 생각하고 public 브랜치를 rebase할 때 신중하게 선택해야 한다.

<br>

## Merge vs. Rebase

- Merge
    - **불필요한 merge 커밋**이 남게 되어 커밋 히스토리가 복잡해진다.
    - 브랜치를 합친 이후에도 각 브랜치의 작업 내역을 추적할 수 있다.
- Rebase
    - merge 커밋이 남지 않아서 **깔끔한 커밋 히스토리**를 만들 수 있다.
    - **작업내역을 새로운 커밋으로 덮어 쓰기 때문에 여러 명이 함께 작업하는 브랜치라면 항상 주의**해야 한다!

<br>

## 참고
- [Merging vs. Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing)
- [Git Merge와 Rebase의 차이, 아름다운고 깔끔한 Git History 만들기](https://firework-ham.tistory.com/12)