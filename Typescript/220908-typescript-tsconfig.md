## npm 실행

- npm init -y : 해당 폴더가 node 프로젝트가 되면서 package.json 파일 생성
- npm i typescript: ts 컴파일러 설치, tsc명령어 사용가능해짐.
- npx tsc --init: tsconfig.json 파일이 생기면서 파일 관리 프로젝트를 타입스크립트가 관리하게 됨.
- npx tsc --noEmit: 에디터를 사용하지 않을경우 수동으로 검사를 해야함. 검사해주는 커맨드.
- npx tsc : ts파일을 js파일로 코드변환해줌. (코드변환과 코드검사는 별개로 작동함!!)
- tsconfig.json 설정
- “allowJs”: true 활성화 : 그래야 js랑 ts를 동시에 사용할수 있음.
- “strict” : true 활성화
- “forceConsistentCasingInFileNames” : true 활성화 : 윈도우에서 import시 파일 대소문자 구별하지 않아 배포 시 문제가 될수 있음.(맥에서는 구별하기 때문) 그래서 대소문자를 지켜야만 import할 수 있게 설정해줌.
- “skipLibCheck” : true 활성화 : 라이브러리 checking을 건너뜀. 라이브러리 다운 시 해당 라이브러리의 타입을 정리한 파일(\*.d.ts)은 검사하지 않게 함.(실제로 사용하는 것만 검사 함)

## ● watch모드(-w)

- 해당 파일의 코드 변경이 감지되면 자동으로 컴파일한다.
- `tsc test.ts -w`
- 단점으로 파일을 구체적으로 지정해야 한다.

## ● tsc --init

- 프로젝트를 타입스크립트 프로젝트라고 처음에 지정하면서 한번만 실행하면 됨.
- 이 커맨드가 실행되는 폴더의 모든 항목을 알려주는 역할을 한다.
- tsconfig.json파일이 생성되어 프로젝트를 타입스크립트가 관리하게 됨.
- `tsc -w` 모든 타입스크립트 파일에 관찰 모드가 적용됨.

## ● 파일 포함 및 제외하기

### 제외 - exclude

- tsconfig.json파일에서 `“exclude”: [ 컴파일 제외할 파일, ... ]` 작성
- `“exclude”: [ node_modules ]` node_modules폴더는 기본 설정상 자동으로 제외된다.

### 포함 - include

- tsconfig.json파일에서 `“include”: [ 컴파일 포함할 파일, ... ]`
  : 컴파일 과정에 포함시킬 파일을 타입스크립트에 알려서 여기에 포함되지 않은 어떤 것도 컴파일되지 않도록 한다.

### 포함 - files

- tsconfig.json파일에서 `“files”: [ 컴파일 포함할 파일, ... ]`
  : 컴파일하고자 하는 개별 파일만을 지정할 수 있다.

## ● 컴파일되는 방식을 관리하는 방법

- `“compilerOptions”`
  : 어떤 파일을 컴파일할지, 그리고 컴파일 되는 파일이 타입스크립트로 어떻게 처리되어야 하는지를 설정할 수 있다.
