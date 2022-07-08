# git flow

> git branch model 중 하나

![](https://velog.velcdn.com/images/zooyaho/post/16d0c110-dd9a-433d-8d6a-7baf6afe38d6/image.png)

```
git switch develop
git merge --no-ff release/1.0
git switch master
git merge --no-ff release/1.0
git tag 1.0
git switch develop
```

👉🏻 git flow를 사용하면 위 작업들을 `git flow release finish 1.0` 한줄로 끝낼 수 있음.

## git flow cli

`$ git flow` : 사용할 수 있는 git flow cli를 알 수 있음.

### ● $ git flow init

>

- git flow저장소가 만들어짐.
- 기본적으로 develop브랜치로 이동함.

- `$ cat .git/config`

```
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[gitflow "branch"]
        master = master
        develop = develop
[gitflow "prefix"]
        feature = feature/
        bugfix = bugfix/
        release = release/
        hotfix = hotfix/
        support = support/
        versiontag =
```

👉🏻 git flow와 관련된 설정들이 setting됨.

### ● git flow feature start 브랜치명

👾 git flow feature start login
![](https://velog.velcdn.com/images/zooyaho/post/f123111f-d14f-420c-80ba-06a9d604ef4b/image.png)
👉🏻 feature/login 브랜치 생성 후 전환

### ● git push --set-upstream origin develop

- $ git remote add origin 원격저장소
- $ git push --set-upstream origin develop
  : 원격의 develop과 로컬의 develop을 한번에 연결 시켜 줌.

### ● git flow feature publish 브랜치명

: 원격저장소 feature에 해당 브랜치를 개시함

### ● git flow feature finish 브랜치명

: develop브랜치에 해당 feature브랜치를 병합함.
: 자동으로 해당 feature브랜치는 삭제됨.

### ● git flow release start RELEASE[BASENAME]

: 출시 절차
: develop브랜치의 내용이 release브랜치에 들어감???

- `$ git flow release start 1.0` : release/1.0 브랜치 생성

### ● git flow release finish RELEASE

1. release브랜치를 master브랜치에 병합 -> 커밋메세지 작성
2. release브랜치를 릴리스 이름으로 태그설정 -> 버전 작성
3. release브랜치를 develop브랜치로 재 병합(back-merge) -> 커밋메세지 작성
   : release 브랜치 삭제
   📎 `git push --tags` : 릴리스를 릴리스 이름으로 태그

### ● git flow hotfix start VERSION[BASENAME]

: 이미 출시된 master에서 문제가 되는 코드만 고치는 작업을 hotfix브랜치에서 다룸.
: 출시된 master브랜치를 복제한 hotfix브랜치를 생성 후 작업

### ● git flow hotfix finish VERSION

: 핫픽스를 종료하면 develop, master브랜치로 변합됨 -> 순차적으로 master 커밋메세지 작성, 태그 작성, develop 커밋메세지 작성으로 진행
: master의 병합 부분은 **핫픽스 버전으로 태그됨!!**

> 참고
> https://www.youtube.com/watch?v=w2F8O9J1keM
> https://danielkummer.github.io/git-flow-cheatsheet/index.ko_KR.html
> Megabyte School: 프론트엔드 개발 취업 연계 과정, 최우영
