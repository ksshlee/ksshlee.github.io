---
title: Git branch
categories : [Git]
tags: [Git]
toc: true
toc_sticky: true
toc_label: "목차"
---


Git branch란?
--

![git1](/assets/img/programmingskill/2020_01_17/git1.png)

- git에서의 핵심기능
- master에서 실험해보고 테스트하다가 이상있으면 힘들어진다.
- 그러므로 다른 branch에서 수정, 테스트후 master branch에 merge)를 한다.



<br><br>


Git 기본 명령어(branch)
--

### branch 목록을 확인

```
git branch
```

**Default는 master이다.**

### branch 생성

```
git branch "branch name"
```

### branch 삭제

```
git branch -d
```

### branch 강제 삭제

```
git branch -D
```

### branch 전환

```
git checkout "branch name"
```

### branch 생성+전환

```
git checkout -b "branch name"
```

### branch merge

**B branch를 A branch로 병합할때**
  
```
branch checkout A

git merge B
```



<br><br>

Git branch 실습
--


![git2](/assets/img/programmingskill/2020_01_17/git2.png)


- 이런식으로 커밋이 되어있다고 가정하자.


```
git checkout -b "develop"
```

- develop 생성후 작업을 develop branch로 해두자.

![git3](/assets/img/programmingskill/2020_01_17/git3.png)

- master에서 branch를 뻗은 시점에서 develop 브렌치에도 같은 commit이 존재한다.

```
git commit -a -m "commit on branch"
```

![git4](/assets/img/programmingskill/2020_01_17/git4.png)

- master에는 branch commit이 존재하지 않는다.
- develop branch에서 commit했기 때문이다.

```
git checkout master

git commit -a -m "Second commit"
```

![git5](/assets/img/programmingskill/2020_01_17/git5.png)

- master로 전환후 commit을 했으므로 develop branch에는 Second 커밋이 없다.


```
git merge develop
```

![git6](/assets/img/programmingskill/2020_01_17/git6.png)

merge를 통해 develop에 있던 소스코드와 master에 있는 소스코드를 합쳐서 master에 commit 한다

**단 이때 master와 develop이 merge를 할때 충돌이 일어날수도 있다.**

### **merge 충돌**

master branch의 A파일은 im master 라는 내용을
<br>
develop branch의 A파일은 Im develop 이라는 서로 다른 내용을 갖고 있으면 merge할때 충돌이 일어난다.

```
Auto-merging test.txt CONFLICT (content): Merge conflict in test.txt Automatic merge failed; fix conflicts and then commit the result.
```

- 이런식으로 에러 메세지를 일으킨다. 자세보면 충돌된 파일이름이 보인다.
- 해당 파일을 들어가보면

```
<<<<<<< HEAD
현재내용
=======
변경된 내용
>>>>>>> issue3
```

- 이런식으로 나타나있는걸 볼수 있다.
- 둘중 하나를 선택, 두개 모두 선택할 수 있다.
- 현재 변경 사항을 선택하면 기존 내용 유지이고 수신 변경 사항을 선택하면 변경된 내용으로 바뀐다.

```
git add filename

git commit -m "merge something"
```
- 이런식으로 커밋을 해주면 merge와 함께 commit이 된다.
- 충돌 해결!

<br><br>


Git 응용 명령어(branch)
--

### branch 간의 비교

```
git diff "branch1" "branch2"
```

- branch1 과 branch2의 코드 차이를 확인할 수 있다.

### branch log graph로 확인

```
//여러줄로 구성
git log --branches --graph --decorate

//한눈에 알아보기 편함
git log --branches --graph --decorate --oneline
```


![git7](/assets/img/programmingskill/2020_01_17/git7.png)


- 이런식으로 확인할수 있다.
- 단 커밋 메세지는 절대로 저렇게 남기면 안된다.

출처
--

[https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html](https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html)


[https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

[https://www.opentutorials.org/module/2676/15260](https://www.opentutorials.org/module/2676/15260)


<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
