220712 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# 유의적 버전(Semantic Versioning, SemVer)

> 의미가 있다. 버전에 의미를 부여해서 해석 가능함.

## 💡Major.Minor.Path

ex) `12.14.1`

- Major: 기존 버전과 호환되지 않는 새로운 버전
- Minor: 기존 버전과 호환되는 새로운 기능이 추가된 버전
- Path: 기존 버전과 호환되는 버그 및 오타 등이 수정된 버전

## 💡^Major.Minor.Path

- `^` : 'npm update 패키지'할 경우 Major버전 안에서 가장 최신 버전으로 업데이트 하겠다는 의미
- ^을 붙이지 않으면 update명령어를 무시하고 패키지가 업데이트 되지 않음!
- 현재 Major버전의 상위버전 설치 시 `npm i 패키지@Major.Minor.Path` 명령어 사용
- 🔥 **자동으로 업데이트가 될 수 있는 단점이 있음!!**

ex) ^12.14.1

- `npm update lodash` 실행 시, 12버전에서 Minor, Path버전이 가장 최신으로 업데이트 됨.

> 참고 06. 유의적 버전(SemVer) - Ch 1. Node.js
