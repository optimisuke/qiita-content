---
title: IBM watsonx Orchestrate ADK を uv でインストール
tags:
  - Python
  - UV
  - watsonxOrchestrate
  - watsonxOrchestrateADK
private: false
updated_at: '2026-02-05T09:55:52+09:00'
id: c2fcfd85d8e36caeb9b1
organization_url_name: ibm
slide: false
ignorePublish: false
---
# はじめに

pip で入れると、どのpythonのどのpipで入れたかわからなくなるので、uvで管理することにした。
どうやって入れたか、どうやってアップデートするか忘れてしまうのでメモ

## インストール方法
```
uv tool install ibm-watsonx-orchestrate
```

## アップデート方法
```
uv tool upgrade ibm-watsonx-orchestrate
uv tool install --upgrade ibm-watsonx-orchestrate==X.Y.Z
```

## 確認方法
```
% uv tool list
ibm-watsonx-orchestrate v2.2.0
- orchestrate
```

## インストール場所

```
% which orchestrate
/Users/ito/.local/bin/orchestrate

% ls -ltr /Users/ito/.local/bin            
total 68392
-rwxr-xr-x  1 ito  staff    336416 May 31  2025 uvx
-rwxr-xr-x  1 ito  staff  34676656 May 31  2025 uv
lrwxr-xr-x  1 ito  staff        72 Jan 23 10:14 orchestrate -> /Users/ito/.local/share/uv/tools/ibm-watsonx-orchestrate/bin/orchestrate
```

## 実行方法
```
% orchestrate --version
ADK Version: 2.2.0
Langflow Version: 1.7.1
Developer Edition Image Tags (if not overridden in env file)
```

# 参考

https://docs.astral.sh/uv/

https://developer.watson-orchestrate.ibm.com/getting_started/installing
