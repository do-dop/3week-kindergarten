# Contribution Guide

이 문서는 GitHub에서 저장소를 fork한 뒤, 새 branch를 만들고, issue 번호가 포함된 commit을 작성해서 Pull Request를 보내는 흐름을 설명합니다.

## 전체 흐름

1. 원본 저장소를 fork합니다.
2. fork한 저장소를 내 컴퓨터로 clone합니다.
3. 작업용 branch를 만듭니다.
4. 파일을 수정합니다.
5. issue 번호가 포함된 commit을 만듭니다.
6. 내 fork 저장소로 push합니다.
7. GitHub에서 Pull Request를 엽니다.

## 1. 저장소 fork하기

GitHub에서 원본 저장소 페이지로 이동합니다.

```text
https://github.com/inpyu/kindergarten
```

오른쪽 위의 `Fork` 버튼을 누릅니다.
그러면 내 GitHub 계정 아래에 같은 저장소가 복사됩니다.

예시:

```text
https://github.com/<내 GitHub ID>/kindergarten
```

fork는 원본 저장소를 직접 수정하지 않고, 내 계정의 복사본에서 안전하게 작업하기 위한 과정입니다.

## 2. fork한 저장소 clone하기

내 계정에 만들어진 fork 저장소 주소를 복사한 뒤 clone합니다.

```bash
git clone https://github.com/<내 GitHub ID>/kindergarten.git
cd kindergarten
```

현재 연결된 원격 저장소를 확인합니다.

```bash
git remote -v
```

처음 clone하면 보통 `origin`은 내 fork 저장소를 가리킵니다.

## 3. 원본 저장소 upstream 등록하기

원본 저장소의 최신 변경사항을 가져오기 위해 `upstream`을 등록합니다.

```bash
git remote add upstream https://github.com/inpyu/kindergarten.git
git remote -v
```

이후 원본 저장소의 최신 내용을 가져올 때는 다음 명령을 사용합니다.

```bash
git fetch upstream
```

## 4. 작업 branch 만들기

`main` branch에서 바로 작업하지 않고, issue 또는 작업 목적에 맞는 branch를 새로 만듭니다.

```bash
git switch main
git fetch upstream
git merge upstream/main
git switch -c docs/add-introduction-issue-3
```

branch 이름에는 작업 내용을 짧게 넣습니다.
issue 번호가 있다면 branch 이름에도 포함하면 추적하기 쉽습니다.

예시:

```text
docs/add-introduction-issue-3
fix/readme-typo-issue-7
```

## 5. 파일 수정하기

필요한 파일을 수정한 뒤 변경사항을 확인합니다.

```bash
git status
git diff
```

수정한 파일을 commit 대상에 추가합니다.

```bash
git add README.md
```

여러 파일을 추가해야 한다면 파일명을 각각 적거나 다음 명령을 사용할 수 있습니다.

```bash
git add .
```

단, `git add .`를 사용할 때는 원하지 않는 파일이 포함되지 않았는지 `git status`로 반드시 확인합니다.

## 6. issue 번호를 포함해서 commit하기

commit 메시지에는 어떤 작업을 했는지와 관련 issue 번호를 포함합니다.

예시:

```bash
git commit -s -m "Docs: add self introduction (#3)"
```

또는 GitHub가 issue를 자동으로 닫게 하려면 `Fixes`, `Closes`, `Resolves` 같은 키워드를 사용할 수 있습니다.

```bash
git commit -s -m "Docs: add self introduction (Fixes #3)"
```

여기서 `#3`은 issue 번호입니다.
실제 작업할 때는 본인이 해결하는 issue 번호로 바꿔야 합니다.

`-s` 옵션은 commit 메시지에 DCO용 `Signed-off-by` 줄을 자동으로 추가합니다.
DCO가 필요한 저장소에서는 이 옵션을 사용하는 것이 안전합니다.

## 7. 내 fork 저장소로 push하기

작업 branch를 내 fork 저장소로 push합니다.

```bash
git push origin docs/add-introduction-issue-3
```

처음 push한 branch라면 Git이 안내하는 `--set-upstream` 명령을 사용해도 됩니다.

```bash
git push --set-upstream origin docs/add-introduction-issue-3
```

## 8. Contribute 버튼으로 Pull Request 열기

GitHub에서 내 fork 저장소 페이지로 이동하면 `Contribute` 버튼이 보입니다.
`Contribute`를 누른 뒤 `Open pull request` 또는 `Compare & pull request`를 선택합니다.

만약 버튼이 보이지 않는다면 원본 저장소의 `Pull requests` 탭에서 `New pull request`를 눌러 직접 branch를 비교할 수 있습니다.

Pull Request의 방향은 보통 다음과 같아야 합니다.

```text
base repository: inpyu/kindergarten
base branch: main
head repository: <내 GitHub ID>/kindergarten
compare branch: docs/add-introduction-issue-3
```

PR 제목에는 작업 내용을 짧게 적습니다.

```text
Docs: add self introduction
```

PR 본문에는 관련 issue 번호를 적습니다.

```text
Fixes #3
```

이렇게 작성하면 PR이 merge될 때 GitHub가 해당 issue를 자동으로 닫습니다.

## 9. 리뷰 반영하기

리뷰어가 수정 요청을 남기면 같은 branch에서 파일을 고치고 다시 commit합니다.

```bash
git status
git add README.md
git commit -s -m "Docs: address review comments (#3)"
git push
```

이미 열린 PR은 같은 branch에 새 commit을 push하면 자동으로 업데이트됩니다.
PR을 새로 만들 필요는 없습니다.

## 수업용 요약

- 원본 저장소를 fork한 뒤 내 fork 저장소에서 작업합니다.
- `main`에서 바로 작업하지 말고 작업용 branch를 만듭니다.
- commit 메시지에는 관련 issue 번호를 포함합니다.
- DCO가 필요한 경우 `git commit -s`를 사용합니다.
- 작업 branch를 push한 뒤 GitHub에서 Pull Request를 엽니다.
- PR 본문에 `Fixes #이슈번호`를 적으면 merge 시 issue가 자동으로 닫힙니다.
