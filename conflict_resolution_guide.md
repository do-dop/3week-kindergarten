# Conflict Resolution Guide

이 문서는 Pull Request에서 conflict가 발생했을 때 원인을 확인하고, 로컬에서 conflict를 해결한 뒤 다시 push하는 방법을 설명합니다.

## conflict란?

conflict는 Git이 두 변경사항을 자동으로 합칠 수 없을 때 발생합니다.
보통 같은 파일의 같은 부분을 여러 사람이 다르게 수정했을 때 생깁니다.

예시:

```text
main branch: README.md의 3번째 줄을 A로 수정
내 branch: README.md의 3번째 줄을 B로 수정
```

Git은 A와 B 중 어떤 내용을 남겨야 하는지 스스로 결정할 수 없기 때문에 사용자에게 직접 선택하라고 알립니다.

## conflict가 자주 발생하는 상황

- 오래된 branch에서 작업한 뒤 PR을 열었을 때
- 여러 사람이 같은 파일의 같은 위치를 수정했을 때
- PR을 열어둔 사이 원본 저장소의 `main` branch가 변경되었을 때
- 자동 생성 파일이나 설정 파일을 여러 branch에서 동시에 수정했을 때

## 1. 현재 작업 branch 확인하기

먼저 내 작업 branch에 있는지 확인합니다.

```bash
git branch --show-current
git status
```

예시 branch:

```text
docs/add-introduction-issue-3
```

아직 commit하지 않은 변경사항이 있다면 먼저 commit하거나 임시 저장합니다.

```bash
git add .
git commit -s -m "Docs: save current work (#3)"
```

또는 아직 commit하고 싶지 않다면 stash를 사용할 수 있습니다.

```bash
git stash
```

## 2. 원본 저장소 최신 내용 가져오기

원본 저장소가 `upstream`으로 등록되어 있는지 확인합니다.

```bash
git remote -v
```

없다면 추가합니다.

```bash
git remote add upstream https://github.com/inpyu/kindergarten.git
```

원본 저장소의 최신 내용을 가져옵니다.

```bash
git fetch upstream
```

## 3. main 최신 내용을 내 branch에 합치기

작업 branch에서 원본 `main`을 merge합니다.

```bash
git merge upstream/main
```

conflict가 없다면 merge commit이 만들어지거나 바로 합쳐집니다.
conflict가 있다면 Git이 어떤 파일에서 충돌이 났는지 알려 줍니다.

상태를 확인합니다.

```bash
git status
```

출력 예시:

```text
both modified: README.md
```

## 4. conflict 표시 읽기

conflict가 난 파일을 열면 다음과 같은 표시가 있습니다.

```text
<<<<<<< HEAD
내 branch에 있던 내용
=======
upstream/main에서 들어온 내용
>>>>>>> upstream/main
```

각 표시의 의미는 다음과 같습니다.

```text
<<<<<<< HEAD
현재 내 branch의 내용
=======
비교 대상 branch의 내용
>>>>>>> upstream/main
```

conflict를 해결하려면 이 표시들을 모두 지우고 최종으로 남길 내용만 정리해야 합니다.

## 5. conflict 해결하기

예를 들어 conflict가 다음과 같다면:

```text
<<<<<<< HEAD
이름: Alice
=======
이름: Alice Kim
>>>>>>> upstream/main
```

최종 파일은 다음처럼 정리합니다.

```text
이름: Alice Kim
```

중요한 점은 conflict 표시인 `<<<<<<<`, `=======`, `>>>>>>>`가 파일에 남아 있으면 안 된다는 것입니다.

수정 후 파일을 다시 확인합니다.

```bash
git diff
```

## 6. 해결한 파일을 stage하기

conflict를 해결한 파일을 stage합니다.

```bash
git add README.md
```

여러 파일을 해결했다면 각각 추가합니다.

```bash
git add README.md DCO_guide.md
```

상태를 확인합니다.

```bash
git status
```

모든 conflict가 해결되면 Git이 merge를 계속할 수 있는 상태라고 표시합니다.

## 7. merge commit 만들기

merge 중 conflict를 해결했다면 다음 명령으로 merge commit을 완료합니다.

```bash
git commit -s
```

Git이 기본 merge commit 메시지를 열어 줄 수 있습니다.
메시지를 그대로 사용해도 되고, 필요한 경우 간단히 수정합니다.

예시:

```text
Merge upstream/main into docs/add-introduction-issue-3
```

## 8. 다시 push하기

conflict 해결 commit을 내 fork 저장소로 push합니다.

```bash
git push
```

PR은 자동으로 업데이트됩니다.
GitHub PR 페이지에서 conflict 표시가 사라졌는지 확인합니다.

## rebase로 해결하는 방법

수업이나 프로젝트에서 commit history를 깔끔하게 유지하고 싶다면 merge 대신 rebase를 사용할 수도 있습니다.

```bash
git fetch upstream
git rebase upstream/main
```

conflict가 발생하면 파일을 수정하고 stage한 뒤 rebase를 계속합니다.

```bash
git add README.md
git rebase --continue
```

rebase가 끝나면 push합니다.
이미 원격에 올린 branch를 rebase했다면 history가 바뀌었기 때문에 다음 명령을 사용합니다.

```bash
git push --force-with-lease
```

초보자는 merge 방식이 더 안전합니다.
rebase는 기존 commit 순서를 다시 쓰기 때문에, 원리를 이해한 뒤 사용하는 것이 좋습니다.

## conflict 해결을 중단하고 싶을 때

merge 중이라면 다음 명령으로 merge 전 상태로 돌아갈 수 있습니다.

```bash
git merge --abort
```

rebase 중이라면 다음 명령을 사용합니다.

```bash
git rebase --abort
```

단, conflict 해결을 시작하기 전에 작업 내용을 commit하거나 stash해 두는 것이 안전합니다.

## 자주 하는 실수

### conflict 표시를 지우지 않음

`<<<<<<<`, `=======`, `>>>>>>>`가 파일에 남아 있으면 코드나 문서가 깨집니다.
해결 후 반드시 파일 전체를 확인합니다.

### 내 내용 또는 상대 내용을 무조건 삭제함

conflict 해결은 한쪽을 무조건 버리는 작업이 아닙니다.
두 변경사항을 모두 반영해야 하는 경우도 많습니다.

### main branch에서 conflict를 해결함

기여 작업은 보통 작업 branch에서 해결해야 합니다.
`main`에서 직접 수정하면 이후 작업 흐름이 꼬일 수 있습니다.

### force push를 무조건 사용함

merge 방식으로 해결했다면 보통 `git push`만 사용하면 됩니다.
`git push --force-with-lease`는 rebase처럼 history를 바꾼 경우에만 사용합니다.

## 수업용 요약

- conflict는 Git이 변경사항을 자동으로 합칠 수 없을 때 발생합니다.
- `git status`로 conflict가 난 파일을 확인합니다.
- 파일 안의 `<<<<<<<`, `=======`, `>>>>>>>` 표시를 지우고 최종 내용을 정리합니다.
- 해결한 파일은 `git add`로 stage합니다.
- merge 방식이면 `git commit -s` 후 `git push`합니다.
- rebase 방식이면 `git rebase --continue` 후 필요하면 `git push --force-with-lease`를 사용합니다.
