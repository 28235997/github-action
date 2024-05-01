### 触发类型

on:
  schedule:
    - cron:
    - cron:
  push:
    branches:
      - master
  pull_request:
    types: [closed, assigned, opened, reopened]
  #接口出发，需要向action发送一个post请求
  #请求体格式{ "event_type": "build", "client_payload"}
  repository_dispatch:
    types: [test_result]

```
{
  "event_type": "test_result",
  "client_payload": {
    "passed": false,
    "message": "Error: timeout"
  }
}
```


then you can access the payload in a workflow like this:

```

on:
  repository_dispatch:
    types: [test_result]

jobs:
  run_if_failure:
    if: ${{ !github.event.client_payload.passed }}
    runs-on: ubuntu-latest
    steps:
      - env:
          MESSAGE: ${{ github.event.client_payload.message }}
        run: echo $MESSAGE
```

### env

env:
  WF_ENV: Available to all jobs

内置变量context
GITHUB_CONTEXT, 包含很多信息
steps.context
secret.xxx
job context
runner context


### automatic token authentication

内置变量：GITHUB_TOKEN
you can use the `GITHUB_TOKEN` include passing the token as an input to an action, or using it to make an authenticated GitHub API request.

```

name: Open new issue
on: workflow_dispatch

jobs:
  open-issue:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      issues: write
    steps:
      - run: |
          gh issue --repo ${{ github.repository }} \
            create --title "Issue title" --body "Issue body"
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```

可以用GITHUB_TOKEN去操作当前repo

### expression

${{ tojson() )}}
${{ 2 == 3}} return boolean type

### keyword

关键字：
runs-on: which os or version
needs: job sequence
continue-on-error: true/false whether it continues when this step fail
timeout-minutes:
