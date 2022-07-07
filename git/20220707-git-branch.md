220707 - TIL

# Branch

> 분기점을 생성하여 독립적으로 코드를 변경할 수 있도록 도와주는 모델

💡 브랜치명: 이 브랜치에서 어떤 일을 할것인지 설명하는 이름으로 설정

## commands

- `$ git branch` : Show available local branch
- `$ git branch -r` : Show available remote branch
- `$ git branch -a` : Show available All branch
- `$ git branch 브랜치명`: 브랜치 생성
- `$ git branch switch`: 브랜치 이동
- `$ git switch -b 브랜치명`: Create & switch branch
- `$ git merge 브랜치명`: merge는 당기는 느낌으로, 머지할 브랜치로 이동 후 머지를 해야함.
- `$ git branch -D 브랜치명`: 브랜치 삭제, 기능 완료 후 머지한 브랜치는 바로 삭제하는 것이 좋음.
- 🔥 `$ git push -u origin 브랜치명`: github에 ‘처음’으로 브랜치를 push할 경우
  -u플래그 작성!! (main은 제외)
  - -u : upstream set
- `$ git fetch origin main`
  - remote에 최신의 데이터를 fetch_head라는 임시 브랜치에 임시로 저장되어 확인할 수 있음.
  - $ git merge FETCH_HEAD를 해야 로컬에 받아짐.
- 🔥 `$ git pull origin main`
  - remote에 최신의 데이터를 local에 당김(받아 옴)
  - pull이 fetch와 merge를 합친거와 같은 로직임!!

## 1. 파일 이름 변경하기

### ● Worst

`$ mv style.css login.css` : 기존 파일(style.css)가 삭제 되고 내용이 같은 새로운 파일(login.css)이 생성되는 것!

👉🏻 `$ mv bye.md hello.md` : 실수로 git을 작성하지 않고 바꿨으면 다시 되돌리면 됨!

### ● 💡 Best

`$ git mv style.css login.css` : git을 붙이면 파일명만 바뀌게 됨!!

## 2. 파일 제어하기 restore

- `$ git restore [file name]` : 수정 전 상태(HEAD상태)로 돌아감.
- `$ git restore .` : 변경된 모든 파일 수정 전 상태로 돌아감.
- `$ git restore --source [commit hash] [file name]`: 특정 파일을 특정 commit으로 복구함.
- `$ git restore --staged [file name]` : add하기 전 상태(unstaging)로 돌아감.

> 참고 https://kotlinworld.com/281

## 3. 최신 커밋의 메세지 수정

`$ git commit --amend` : (직전의)최신 커밋의 메세지를 수정하는 방법, push후에는 수정할 수 없음.

## 4. Revert를 사용한 커밋 되돌리기

`$ git revert --no-commit HEAD~3..` : 3개의 커밋 전으로 돌아감.

- --no-commit : 마지막에 한번만 커밋메세지를 작성하는 것.
