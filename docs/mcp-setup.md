# Colab MCP 설치 가이드 (이 폴더 전용)

Claude Code가 브라우저에 열린 Google Colab 노트북의 셀을 만들고 실행할 수 있게 해 주는
Google 공식 MCP 서버(`googlecolab/colab-mcp`)를 **이 프로젝트 폴더에서만** 쓰도록 설치하는 방법입니다.

- 공식 저장소: https://github.com/googlecolab/colab-mcp
- 공식 소개 글: https://developers.googleblog.com/en/announcing-the-colab-mcp-server-connect-any-ai-agent-to-google-colab/

---

## 0. 준비물

| 항목 | 확인 명령 | 확인한 버전 (2026-11-11, Windows 11) |
|---|---|---|
| uv / uvx | `uv --version` | 0.13.5 |
| Python | `python --version` | 3.14.7 |
| git | `git --version` | 2.56.0 |
| Claude Code CLI | `claude --version` | - |

uv가 없으면 `pip install uv`로 설치합니다.

---

## 1. 등록 범위(scope) 고르기

| 범위 | 설정이 저장되는 곳 | 어디서 쓰이나 | 이 과제에 |
|---|---|---|---|
| `-s local` | `~/.claude.json` 안의 **이 폴더 경로** 항목 | 이 폴더에서만, 나 혼자 | **사용** |
| `-s project` | 이 폴더의 `.mcp.json` | 이 폴더에서만, 저장소에 포함됨 | 공개 저장소에 올라가므로 안 씀 |
| `-s user` | `~/.claude.json` 최상위 | 모든 폴더 | 전역이라 안 씀 |

`-s local`은 **명령을 실행한 폴더**에 묶입니다. 반드시 이 프로젝트 폴더에서 실행하세요.
홈 폴더(`C:\Users\<이름>`)에서 실행하면 홈 폴더 전용으로 등록됩니다.

---

## 2. 설치

### 2-1. (해당 시) 다른 폴더에 잘못 등록된 것 지우기

홈 폴더에 등록했던 적이 있다면 그 폴더로 가서 지웁니다.

```bash
cd C:\Users\<이름>
claude mcp remove colab-mcp -s local
```

### 2-2. 이 폴더에 등록

```bash
cd C:\project\workspace\git_project\flux-pose-controller
claude mcp add colab-mcp -s local -- uvx git+https://github.com/googlecolab/colab-mcp
```

### 2-3. 등록 범위 확인

```bash
claude mcp get colab-mcp
```

`Scope: Local` 이고, **다른 폴더에서 `claude mcp list`를 돌렸을 때 `colab-mcp`가 안 보이면** 정상입니다.

---

## 3. 연결 확인과 "30초 타임아웃" 문제

```bash
claude mcp list
```

처음에는 아래처럼 실패할 수 있습니다.

```
colab-mcp: ... - ✘ Failed to connect — MCP server "colab-mcp" connection timed out after 30000ms
```

### 원인 (직접 측정)

서버 자체는 정상입니다. **시작이 느릴 뿐입니다.** 같은 PC에서 initialize 요청에 첫 응답이 오기까지 잰 값:

| 실행 방식 | 첫 응답까지 |
|---|---|
| `uvx git+https://github.com/googlecolab/colab-mcp` (기본) | 약 52초 |
| 커밋을 고정한 `...colab-mcp@<커밋해시>` | 1회차 54초, 2회차 33초 |
| `uvx --offline ...` | 실패 (매번 git HEAD 를 확인하려 해서) |

Claude Code 는 MCP 서버 시작을 기본 **30초**만 기다리므로 시간 초과로 끊깁니다.
uvx 는 실행할 때마다 git 저장소를 확인하고 Python 패키지를 불러오는데, 이 과정이 30초를 넘깁니다.
(Windows 백신 검사가 시간을 늘리는지는 확인하지 않았습니다.)

### 해결: 기다리는 시간 늘리기 (`MCP_TIMEOUT`, 단위 ms)

**터미널(CLI)에서 확인만 할 때** — PowerShell:

```powershell
$env:MCP_TIMEOUT=120000; claude mcp list
```

Git Bash:

```bash
MCP_TIMEOUT=120000 claude mcp list
```

→ 이렇게 하면 `colab-mcp: ... - ✔ Connected` 로 바뀌는 것을 확인했습니다 (2026-11-11).

**매번 적용되게 하려면 (이 폴더 전용)** — 이 폴더에 `.claude/settings.local.json` 을 만들고:

```json
{
  "env": {
    "MCP_TIMEOUT": "120000"
  }
}
```

`settings.local.json` 은 개인 설정이라 git 에 올리지 않습니다 (`.gitignore` 에 `.claude/settings.local.json` 추가).
> 확인 못 한 것: 이 설정 파일의 `env` 가 MCP 서버 시작 대기 시간에도 적용되는지는 아직 직접 확인하지 않았습니다.
> 적용 후 새 세션에서 `colab-mcp` 도구가 보이는지로 확인하세요.

### (선택) 시작 시간 조금 줄이기: 커밋 고정

```bash
git ls-remote https://github.com/googlecolab/colab-mcp HEAD
claude mcp remove colab-mcp -s local
claude mcp add colab-mcp -s local -- uvx git+https://github.com/googlecolab/colab-mcp@<위에서 나온 커밋해시>
```

2회차부터 약 33초로 줄었지만 여전히 30초를 넘으므로 `MCP_TIMEOUT` 은 함께 필요합니다.
대신 업데이트가 자동으로 들어오지 않으니, 새 버전이 필요할 때 커밋해시를 바꿔 다시 등록합니다.

---

## 4. 사용법

1. 브라우저에서 사용할 Colab 노트북을 엽니다 (예: 이 저장소의 `pose_tool.ipynb` 를 Colab 에서 연 것).
2. Colab 메뉴 **[런타임 → 런타임 유형 변경 → T4 GPU]** 로 GPU 를 붙입니다. (GPU 선택은 사람이 직접)
3. 이 폴더에서 Claude Code 세션을 **새로** 엽니다. MCP 는 세션 시작 때 불러오므로, 등록 전에 열어 둔 세션에는 안 보입니다.
4. Claude 에게 "열려 있는 Colab 노트북의 1번 셀부터 실행해 줘"처럼 지시합니다.

공식 소개 글 기준으로 에이전트가 할 수 있는 일: 셀 추가(코드/마크다운), 셀 실행, 셀 순서 변경, `!pip install` 로 패키지 설치.
작업 중에도 브라우저에서 노트북을 직접 보고 손으로 이어받을 수 있습니다.

> 확인 못 한 것: 공식 문서에는 브라우저 노트북과 연결되는 정확한 방식(버튼, 인증 절차)이 적혀 있지 않습니다.
> 첫 연결 때 브라우저 쪽에서 허용을 묻는 화면이 나오면 그 안내를 따르세요.

---

## 5. 문제 해결

| 증상 | 확인할 것 |
|---|---|
| `connection timed out after 30000ms` | 3장의 `MCP_TIMEOUT` 설정 |
| 다른 폴더에서도 보임 | `claude mcp get colab-mcp` 의 Scope 확인. user 면 지우고 local 로 다시 등록 |
| 이 폴더에서 안 보임 | 등록할 때 다른 폴더에 있었는지 확인 (`~/.claude.json` 의 `projects` 아래 경로) |
| 갑자기 연결이 끊김 | 이전 세션의 서버 프로세스가 남아 있을 수 있음 (커뮤니티 보고). 작업 관리자에서 남은 `python`/`uv` 프로세스를 정리하고 세션 재시작 |
| 셀은 실행되는데 GPU 가 없음 | Colab 런타임 유형을 T4 GPU 로 바꿨는지 확인 |

## 6. 제거

```bash
cd C:\project\workspace\git_project\flux-pose-controller
claude mcp remove colab-mcp -s local
```
