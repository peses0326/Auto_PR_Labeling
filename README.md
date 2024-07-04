# auto_labeling_final
PR or Issue의 title에 따라 자동으로 label을 붙여주는 auto_Labeling 입니다.  
  
### 사용법 1) .github/workflows/Auto_PR_Labeler.yml 를 생성하고 아래 코드를 입력
```
name: Issue PR Labeler #이름은 바꿔도 됨
on:
  issues:
    types:
      - opened
      - edited
  pull_request_target: # or pull_request_target
    types:
      - opened
      - reopened

jobs:
  main:
    runs-on: ubuntu-latest

    permissions:
      contents: read       # 위에 작성한 설정 파일을 읽기 위해 필요
      issues: write        # 이슈에 라벨을 추가하기 위해 필요
      pull-requests: write # PR에 라벨을 추가하기 위해 필요

    steps:
      - name: Run Issue PR Labeler
        uses: hoho4190/issue-pr-labeler@v2.0.0
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          disable-bot: true
          config-file-name: labeler-config.yml


```
### 사용법 2) .github/labeler-config.yml 를 생성하고 아래 코드를 입력  
filters: 아래에 다음과 같은 규칙으로 작성  
```
  - label: bug #(label 이름, 해당 label 없으면 자동 생성)  
    regexs: #(감지할 키워드, 규칙은 아래 참고) 
      - /\bfeat\b/  
      - /feature/i  
    events: [issues, pull_request]  #(issues에 적용할지, pr에만 적용할지 선택가능)
    targets: [title, comment] #(감지할 타겟 설정)
```  
```
filters:
  - label: feat
    regexs:
      - /\bfeat\b/
      - /feature/i
    events: [issues, pull_request]
    targets: [title, comment]
  - label: bug
    regexs:
      - /fix|bug/
    targets: [title]
  - label: documentation
    regexs:
      - /docs/
    events: [pull_request]
  - label: chore
    regexs:
      - /\bchore(\(.*\))?:/i
  - label: 1조
    regexs:
      - /박응수/
  - label: 2조
    regexs:
      - /홍길동|김철수|고로케/
```

#### Properties 참고  

| Key       | Type           | Value                       | Description                                                                               |
| --------- | -------------- | --------------------------- | ----------------------------------------------------------------------------------------- |
| `label`   | `String`       | label 이름                  | - 필수<br>- Label name to be added                                                    |
| `regexs`  | `List<String>` | 감지할 명칭                 | - 필수<br>- List of regular expressions to filter<br>- Syntax: `/pattern/modifier(s)` |
| `events`  | `List<Event>`  | `issues`<br> `pull_request` | - Optional<br>- List of events to filter<br>- Default: `[issues, pull_request]`           |
| `targets` | `List<Target>` | `title`<br> `comment`       | - Optional<br>- List of target to filter<br>- Default: `[title, comment]`  
