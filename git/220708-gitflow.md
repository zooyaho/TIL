# git flow

> git branch model ์ค ํ๋

![](https://velog.velcdn.com/images/zooyaho/post/16d0c110-dd9a-433d-8d6a-7baf6afe38d6/image.png)

```
git switch develop
git merge --no-ff release/1.0
git switch master
git merge --no-ff release/1.0
git tag 1.0
git switch develop
```

๐๐ป git flow๋ฅผ ์ฌ์ฉํ๋ฉด ์ ์์๋ค์ `git flow release finish 1.0` ํ์ค๋ก ๋๋ผ ์ ์์.

## git flow cli

`$ git flow` : ์ฌ์ฉํ  ์ ์๋ git flow cli๋ฅผ ์ ์ ์์.

### โ $ git flow init

>

- git flow์ ์ฅ์๊ฐ ๋ง๋ค์ด์ง.
- ๊ธฐ๋ณธ์ ์ผ๋ก develop๋ธ๋์น๋ก ์ด๋ํจ.

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

๐๐ป git flow์ ๊ด๋ จ๋ ์ค์ ๋ค์ด setting๋จ.

### โ git flow feature start ๋ธ๋์น๋ช

๐พ git flow feature start login
![](https://velog.velcdn.com/images/zooyaho/post/f123111f-d14f-420c-80ba-06a9d604ef4b/image.png)
๐๐ป feature/login ๋ธ๋์น ์์ฑ ํ ์ ํ

### โ git push --set-upstream origin develop

- $ git remote add origin ์๊ฒฉ์ ์ฅ์
- $ git push --set-upstream origin develop
  : ์๊ฒฉ์ develop๊ณผ ๋ก์ปฌ์ develop์ ํ๋ฒ์ ์ฐ๊ฒฐ ์์ผ ์ค.

### โ git flow feature publish ๋ธ๋์น๋ช

: ์๊ฒฉ์ ์ฅ์ feature์ ํด๋น ๋ธ๋์น๋ฅผ ๊ฐ์ํจ

### โ git flow feature finish ๋ธ๋์น๋ช

: develop๋ธ๋์น์ ํด๋น feature๋ธ๋์น๋ฅผ ๋ณํฉํจ.
: ์๋์ผ๋ก ํด๋น feature๋ธ๋์น๋ ์ญ์ ๋จ.

### โ git flow release start RELEASE[BASENAME]

: ์ถ์ ์ ์ฐจ
: develop๋ธ๋์น์ ๋ด์ฉ์ด release๋ธ๋์น์ ๋ค์ด๊ฐ???

- `$ git flow release start 1.0` : release/1.0 ๋ธ๋์น ์์ฑ

### โ git flow release finish RELEASE

1. release๋ธ๋์น๋ฅผ master๋ธ๋์น์ ๋ณํฉ -> ์ปค๋ฐ๋ฉ์ธ์ง ์์ฑ
2. release๋ธ๋์น๋ฅผ ๋ฆด๋ฆฌ์ค ์ด๋ฆ์ผ๋ก ํ๊ทธ์ค์  -> ๋ฒ์  ์์ฑ
3. release๋ธ๋์น๋ฅผ develop๋ธ๋์น๋ก ์ฌ ๋ณํฉ(back-merge) -> ์ปค๋ฐ๋ฉ์ธ์ง ์์ฑ
   : release ๋ธ๋์น ์ญ์ 
   ๐ `git push --tags` : ๋ฆด๋ฆฌ์ค๋ฅผ ๋ฆด๋ฆฌ์ค ์ด๋ฆ์ผ๋ก ํ๊ทธ

### โ git flow hotfix start VERSION[BASENAME]

: ์ด๋ฏธ ์ถ์๋ master์์ ๋ฌธ์ ๊ฐ ๋๋ ์ฝ๋๋ง ๊ณ ์น๋ ์์์ hotfix๋ธ๋์น์์ ๋ค๋ฃธ.
: ์ถ์๋ master๋ธ๋์น๋ฅผ ๋ณต์ ํ hotfix๋ธ๋์น๋ฅผ ์์ฑ ํ ์์

### โ git flow hotfix finish VERSION

: ํซํฝ์ค๋ฅผ ์ข๋ฃํ๋ฉด develop, master๋ธ๋์น๋ก ๋ณํฉ๋จ -> ์์ฐจ์ ์ผ๋ก master ์ปค๋ฐ๋ฉ์ธ์ง ์์ฑ, ํ๊ทธ ์์ฑ, develop ์ปค๋ฐ๋ฉ์ธ์ง ์์ฑ์ผ๋ก ์งํ
: master์ ๋ณํฉ ๋ถ๋ถ์ **ํซํฝ์ค ๋ฒ์ ์ผ๋ก ํ๊ทธ๋จ!!**

> ์ฐธ๊ณ 
> https://www.youtube.com/watch?v=w2F8O9J1keM
> https://danielkummer.github.io/git-flow-cheatsheet/index.ko_KR.html
> Megabyte School: ํ๋ก ํธ์๋ ๊ฐ๋ฐ ์ทจ์ ์ฐ๊ณ ๊ณผ์ , ์ต์ฐ์
