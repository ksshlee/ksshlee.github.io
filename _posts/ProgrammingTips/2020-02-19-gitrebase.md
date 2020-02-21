---
title: Git merge,Rebase
categories : [Git]
tags: [Git]
toc: true
toc_sticky: true
toc_label: "목차"
---


Merge
--

![git](/assets/img/programmingskill/2020-02-21/git.png)

- 브렌치를 병합할때는 2가지 방법이 있다.
- 2 way merge (비추) 3 way merge
- 기본적으로 merge를 하게 된다면 3 way merge로 하게 될것이다.
- 2 way merge는 마지막 커밋 2개 즉 m2, f2만 비교를 한다.
- 3 way merge는 3개의 커밋 Base, m2, f2를 비교한다.

### 2 way merge
- **이 방법은 쓰지 말자 그냥 알고만 있자**
- Me는 m2 Other은 f2 Base는 Base라고 생각하자

![git](/assets/img/programmingskill/2020-02-21/git2.png)

- A,B,C,D는 파일이라고 생각하자
- D와 D'은 서로 다른 파일이다.
- **2 way merge는 Base Commit을 비교하지 않는다.**
- 양쪽이 같은 B 파일은 머지가 된다.
- 나머지 파일은 base commit이 없어 원래 파일이 어떤 파일이였는지 알수가 없다.
- 그러므로 **충돌**이 일어난다.


### 3 way merge

- Me는 m2 Other은 f2 Base는 Base라고 생각하자

![git](/assets/img/programmingskill/2020-02-21/git3.png)

- **3 way merge는 Base commit과 함께 비교한다.**
- Base commit을 기준으로 파일을 비교하여 merge를 한다.
- A 같은 경우는 Me와 Base는 A지만 다른 branch에서 변동이 있었으므로 A'이 merge된다.
- B 같은 경우는 모두 같으므로 B가 merge된다.
- C 같은 경우는 Base, Other은 C이다. 하지만 Me는 C파일을 삭제했으므로 merge를 하면 파일은 삭제된다. Me 와 Base는 C를 갖고 있지만 Other가 C를 삭제했으면 merge를 해도 C파일은 삭제가 된다.
- D 같은 경우는 Base, Other, Me 모두가 다르므로 충돌(conflict)이 발생한다.


<br><br>


Rebase
--



![git](/assets/img/programmingskill/2020-02-21/git6.png)

- rebase는 말그대로 re-base다 즉 base commit의 위치를 바꾸는 것이다.
- 현재의 Base가 base이다. rebase는 m2 커밋을 base로 바꾸는 작업이다.


### 사용법
>1

![git](/assets/img/programmingskill/2020-02-21/git.png)

```java
git checkout (branch name)

ex)
git checkout feature
```

- 현재 작업 branch를 rebase 할 branch로 변경을 한다.

>2

```java
git rebase (branch name)

ex)
git rebase master
```

- rebase할 branch 명을 넣는다.
- 이 과정에서 충돌이 발생할수도 있다.

#### conflict


```java
CONFLICT (content): Merge conflict in (file name)
error: Failed to merge in the changes.
Patch failed at 0001 (file name) (commit message)
hint: Use 'git am --show-current-patch' to see the failed patch
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort"
```
- file name을 들어가보면 충돌이 난 것을 확인할 수 있다.

> git rebase 충돌 해결 후

```java
1. git add <conflicted file>
2. git rebase --continue
```



> git rebase 취소하기

```java
git rebase --abort
```

> git rebase 충돌 건너뛰기

```java
git rebase --skip
```

- 해당 명령어를 사용하면 master branch에서의 수정사항이 덮어쓰기 된다.

>3

![git](/assets/img/programmingskill/2020-02-21/git5.png)

- 그러면 이 단계 까지 왔을 것이다.
- 하지만 아직 master branch에선 f1, f2 커밋이 적용이 되지 않았다.


```java
git merge (branch name)

ex)
git merge feature
```

>success

![git](/assets/img/programmingskill/2020-02-21/git7.png)

- rebase에 성공한것을 확인할 수 있다.

<br><br>


Merge vs Rebase
--

- **Merge**
  - merge를 하게되면 merge 커밋이 추가로 남게 된다.(로그가 복잡해진다.)
  - 어떤 브렌치를 사용했고 어떤 작업을 했는지 상세하게 남게 된다.

- **Rebase**
  - rebase를 하게되면 merge 커밋은 남지 않는다.(로그가 깔끔해진다.)
  - 어떤 브렌치를 사용했는지 모르게 된다.


>로그 history를 깔끔하게 -> Rebase <br>
>모든 기록 상세하게 확인 -> merge <br>
>어떤 방식을 사용해도 상관이 없다. **각각 상황에 맞는** 병합 방식을 채택하여 사용하자



<br><br>



출처
--
[https://www.opentutorials.org/module/2676/15307](https://www.opentutorials.org/module/2676/15307)

[https://velog.io/@godori/Git-Rebase](https://velog.io/@godori/Git-Rebase)


<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
