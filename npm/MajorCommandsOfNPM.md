## npm의 주요 명령어

**`npm run`**
- `npm run`뒤에 오는 명령어를 `package.json`에서 찾아서 실행하는 역할을 한다.
```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```
- 만약 scripts에 특정 인수를 전달하고 싶다면 `--`를 사용하면 된다.

```shell
$ npm run start -- --port=3000
 node index.js --port=3000
```
- `npm run`을 직접 실행하는 과정에서는 보이지 않지만 `node_modules`가 PATH에 추가된다. 따라서 `npm run lint`같은 명령어는 다음과 같이 실행된다고 볼 수 있다.
```json
{
  "scripts": {
    "lint": "node_modules/.bin/eslint"
  }
}
```

**`npm install`과 `npm ci`**
- 의존성 설치 명령어, 둘다 `package.json`에 선언된 의존성을 설치한다는 공통점이 있지만 차이가 있다.
- `npm install`은 `package-lock.json`의 존재 유무와 관계없이 모두 실행할 수 있다. `package-lock.json`이 없는 경우에는 `package.json`에 선언되어 있는 버전에 맞춰 설치한 다음, 이 내용을 담은 `package-lock.json`을 새롭게 생성한다.
- 반대로 `package-lock.json`이 이미 있는 경우에는 이 내용에 따라 설치하며, 다음과 같은 경우에만 `package-lock.json`을 수정한다.
    - `package-lock.json`에 없는 패키지를 설치할 때
    - `package-lock.json`의 `package.json` 내용과 맞지 않는 경우
- `npm ci`는 `package-lock.json`이 있는 경우에만 실행할 수 있으며, `package-lock.json`의 내용 그대로 의존성을 설치한다.
- 어떤 경우에도 `package-lock.json`을 수정하지 않으며, `package-lock.json`이 없거나 `package.json`의 내용과 맞지 않는 경우 에러를 발생시킨다.

**`npm update`**
- `package.json`에 명시된 버전을 기준으로 의존성을 업데이트 한다.
- `npm update` 이후에는 CI를 통해 서비스 빌드의 무결성을 확인하는 등의 최소한의 절차를 갖추는 것이 좋다.

**`npm dedupe`(중복 제거)**
- 현재 패키지트리를 기반으로 의존성을 단순화 하는 명령어이다.
- 패키지 트리 간에 해결할 수 있는 의존성이 있다면 `npm dedupe`는 이를 단순화 한다.
- `node_modules`나 `package-lock.json`을 업데이트하기 전에 어떻게 변경되는지 미리 알고 싶다면 `--dry-run`인수를 추가로 지정하면 된다.
```shell
$ npm dedupe --dry-run

added 30 packages, removes 41 packages, and changed 269 packages in 2m
```
- `npm install`을 실행할 때 이 명령어도 함께 실행하고 싶다면 `npm config set perfer-dedupe true`로 설정하거나 `npm install --prefer-dedupe`커맨드로 실행하면 된다.

**`npm ls`**
- `npm ls <패키지명>`은 `package.json`내에서 `<패키지>`가 설치되어 있는 모든 의존성을 보여준다.

**`npm explain` or `npm why`**
- `npm explain`은 반드시 패키지명을 지정해야 한다.
- `npm explain`은 대상 의존성이 정확히 왜 설치되었는지에 대한 정보까지 나타낸다.

**`npm audit`**
- npm에서 제공하는 보안 취약점을 검사하는 명령어다.
- 현재 의존하고 있는 패키지에서 취약점으로 알려진 패키지가 있는지 검사한다.

**`npm publish`**
- 현재 패키지를 레지스트리에 업로드하는 명령어다. 이렇게 업로드된 패키지는 레지스트리에 등록되어 다른 사용자가 `npm install`로 설치해서 사용할 수 있게 된다.
- 다만 해당 프로젝트에 있는 모든 파일이 업로드 되는 것은 아니고, 다음의 규칙에 해당하는 파일이 업로드 된다.
  - `package.json, README.md, LICENSE`와 같이 패키지 설치 및 배포에 필수적으로 필요한 파일들은 자동으로 업로드된다. 
  - `.gitignore`와 `.npmignore`가 있다면 해당 파일 내에 명시된 파일이나 폴더는 업로드 되지 않는다. 만약 두 파일 모두 존재한다면 `.npmignore`가 우선되며, `.gitignore`는 무시된다.
  - `node_modules`를 비롯해 `.DS_Store, .svn`같은 파일도 무시된다.
  - 만약 `package.json`에 `files`가 선언된 경우 해당 파일만 업로드된다.

**`npm deprecate`**
- 업로드 되어 있는 특정 패키지에 대해 사용자에게 경고 메세지를 보여주는 명령어다.
```shell
$ npm deprecate my-package@"< 0.1.0" "보안 취약점이 발견되어 0.1.0 미만 버전은 모두 deprecate되었습니다. 0.1.0 이상 버전을 사용해주세요."

$ npm deprecate my-package "이 패키지는 더 이상 유지보수되지 않습니다. 사용을 지양해 주세요."
```
- 만약 지원 중단 처리를 되돌리고 싶다면 메시지에 빈문자열을 지정하면 된다.
```shell
$ npm deprecate my-package ""
```

**`npm outdated`**
- 현재 설치된 패키지 중 현재 시간 기준 최신 버전이 아닌, 업데이트가 가능한 패키지를 볼 수 있는 명령어이다.
- 현재 설치된 패키지 중 가장 최신 버전과 비교해서 어떤 패키지가 업데이트 가능한지 확인할 수 있다.

**`npm view`**
- 특정 패키지의 정보를 확인할 수 있다.


**<a href="https://docs.npmjs.com/cli/v10/commands/npm">더 많은 명령어</a>**