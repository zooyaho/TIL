220629 - TIL

# Shell Command

```
$ cd Documents/ // 폴더 이동
$ mkdir dev // 폴더 생성
$ cd .. // 상위경로 이동
$ pwd // 현대 위치 절대경로
$ touch readme.md // 파일 생성
$ mv readme.md bin/ // bin폴더로 이동
$ cp readme.md bin/ // bin폴더로 해당 파일 복사
$ mv readme.md ./README.txt // 파일 이름 변경
$ rm README.txt // 파일 삭제
$ rm -rf bin/ // 폴더 안 파일 포함해서 해당 폴더 삭제
$ cat readme.md // catnate, 출력
$ vi readme.md // vim으로 해당 파일 오픈
```

# Command mode

```
:q - quit 
:q! - quit discarding all changes
:w - write, 저장
:wq - write and quit
:{number} - jump to {number}th line. 해당 라인으로 이동
```

# Conventional Commits
1. commit의 제목은 하나의 구나 절로 완성하며 대문자로 시작
2. prefix 작성
- feat: 기능 개발 관련
- fix: 오류 개선 혹은 버그 패치 docs: 문서화 작업
- test: test 관련
- conf: 환경설정 관련
- build: 빌드 관련
- ci: Continuous Integration 관련
