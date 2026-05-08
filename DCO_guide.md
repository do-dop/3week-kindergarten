# DCO Guide

## DCO란?

DCO는 Developer Certificate of Origin의 약자입니다.
코드를 기여하는 사람이 해당 코드를 제출할 권리가 있음을 커밋 메시지에 명시하는 방식입니다.

오픈소스 프로젝트에서는 외부 기여자가 많기 때문에 다음을 확인해야 합니다.

- 이 코드를 직접 작성했는가
- 다른 코드를 가져왔다면 해당 라이선스상 제출할 권리가 있는가
- 회사나 조직에서 작성한 코드라면 공개 프로젝트에 제출할 권한이 있는가

DCO는 별도의 계약서를 받는 방식이 아니라, 커밋마다 `Signed-off-by` 줄을 남겨 기여자가 이를 확인했다는 기록을 남깁니다.

## Signed-off-by란?

DCO가 적용된 프로젝트에서는 커밋 메시지 마지막에 다음과 같은 줄을 추가합니다.

```text
Signed-off-by: 이름 <이메일>
```

예시:

```text
Add self introduction

Signed-off-by: Alice Kim <alice@example.com>
```

이 줄은 단순한 서명이 아니라, "나는 이 코드를 제출할 권리가 있다"는 확인 기록입니다.

## 커밋에 DCO 붙이기

Git은 DCO 서명을 자동으로 붙이는 `-s` 옵션을 제공합니다.

```bash
git commit -s -m "Add self introduction"
```

위 명령은 Git 설정의 `user.name`, `user.email` 값을 사용해서 커밋 메시지 끝에 `Signed-off-by` 줄을 추가합니다.

현재 Git 사용자 정보를 확인하려면 다음 명령을 사용합니다.

```bash
git config user.name
git config user.email
```

설정이 없다면 다음처럼 등록합니다.

```bash
git config user.name "Alice Kim"
git config user.email "alice@example.com"
```

모든 저장소에 공통으로 적용하려면 `--global` 옵션을 붙입니다.

```bash
git config --global user.name "Alice Kim"
git config --global user.email "alice@example.com"
```

## 이미 만든 커밋에 DCO 붙이기

마지막 커밋 하나에 DCO를 추가하려면 다음 명령을 사용합니다.

```bash
git commit --amend -s --no-edit
```

이미 원격 브랜치에 push한 커밋을 수정했다면 다시 push할 때 다음 명령을 사용합니다.

```bash
git push --force-with-lease
```

여러 커밋에 DCO가 빠져 있다면 interactive rebase로 각 커밋을 수정해야 합니다.

```bash
git rebase -i HEAD~3
```

수정할 커밋을 `pick`에서 `edit`으로 바꾼 뒤, 멈출 때마다 다음 명령을 실행합니다.

```bash
git commit --amend -s --no-edit
git rebase --continue
```

## DCO bot이 하는 일

DCO bot은 Pull Request의 커밋 메시지를 검사합니다.
커밋에 올바른 `Signed-off-by` 줄이 없으면 PR check를 실패로 표시합니다.

일반적인 흐름은 다음과 같습니다.

1. 기여자가 PR을 엽니다.
2. DCO bot이 PR 안의 커밋을 검사합니다.
3. 모든 커밋에 올바른 `Signed-off-by`가 있으면 check가 통과합니다.
4. 누락된 커밋이 있으면 check가 실패합니다.
5. 기여자는 커밋을 amend 또는 rebase로 수정한 뒤 다시 push합니다.

DCO bot은 코드를 고쳐 주지는 않습니다.
대신 어떤 커밋에 서명이 빠졌는지 알려 주고, 프로젝트가 DCO를 강제할 수 있도록 check 결과를 제공합니다.

## GitHub에 DCO bot 붙이기

GitHub에서는 DCO를 검사하는 GitHub App을 설치해서 사용할 수 있습니다.
대표적으로 DCO2 앱을 사용할 수 있습니다.

```text
https://github.com/apps/dco-2
```

설치 절차:

1. GitHub App 페이지에서 `Install`을 선택합니다.
2. 조직 또는 개인 계정을 선택합니다.
3. 전체 저장소 또는 특정 저장소만 선택합니다.
4. 설치 후 PR을 열어 DCO check가 생성되는지 확인합니다.
5. 반드시 통과해야 merge할 수 있게 하려면 branch protection에서 required status check로 설정합니다.

## Branch protection에 연결하기

DCO bot을 설치해도 기본적으로는 check 결과만 표시됩니다.
merge를 막으려면 branch protection에 required check로 등록해야 합니다.

GitHub 저장소에서 다음 메뉴로 이동합니다.

```text
Settings
→ Branches
→ Branch protection rules
→ 대상 브랜치 규칙 선택
→ Require status checks to pass before merging
→ DCO 관련 check 선택
```

이 설정을 켜면 DCO check가 실패한 PR은 merge할 수 없습니다.

## 자주 발생하는 문제

### 커밋에 Signed-off-by가 없음

해결:

```bash
git commit --amend -s --no-edit
git push --force-with-lease
```

### Signed-off-by 이메일과 커밋 author 이메일이 다름

DCO bot은 보통 커밋 author와 `Signed-off-by`의 이메일이 일치하는지 확인합니다.
Git 설정을 확인하고 필요한 경우 커밋을 다시 작성합니다.

```bash
git config user.email
```

### 여러 커밋 중 일부만 실패함

실패한 커밋 각각에 `Signed-off-by`를 추가해야 합니다.
이때 `git rebase -i`를 사용합니다.

## 수업용 요약

- DCO는 "이 코드를 제출할 권리가 있다"는 기여자의 확인입니다.
- 커밋 메시지에 `Signed-off-by: 이름 <이메일>`을 남깁니다.
- 새 커밋을 만들 때는 `git commit -s`를 사용합니다.
- 이미 만든 마지막 커밋은 `git commit --amend -s --no-edit`으로 수정합니다.
- DCO bot은 PR의 커밋을 검사하고, DCO가 빠진 커밋이 있으면 check를 실패시킵니다.
