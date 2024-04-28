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
