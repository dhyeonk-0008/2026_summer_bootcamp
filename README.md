# 2026_summer_bootcamp : Day 3 실습 — Agentic AI × Drug Discovery 

Claude Code로 신약개발 파이프라인을 직접 돌려보는 3일차 실습 저장소입니다.

- **전반부** `egfr/` — EGFR 결합친화도 예측 파이프라인을 에이전트 팀에게 위임
- **후반부** `pnck/` — PNCK에 결합하는 소분자를 설계하고 검증

---

## 1. 준비물

| 항목 | 확인 방법 |
|---|---|
| Claude Code 설치 | 터미널에 `claude --version` |
| git 설치 | `git --version` |
| 실습용 API 키 | 메일로 전달 받은 API 키를 확인해주세요 |
| Colab 로그인 | 브라우저에서 미리 로그인해 두세요 |

Claude Code가 없다면 먼저 설치합니다.

```bash
npm install -g @anthropic-ai/claude-code
```

---

## 2. 저장소 내려받기

**반드시 홈 디렉터리에서 시작하세요.** 다른 프로젝트 폴더 안에서 clone하면 그 폴더의 설정이 섞여 실습이 꼬입니다.

```bash
cd ~
git clone https://github.com/dhyeonk-0008/2026_summer_bootcamp.git bootcamp-day3
cd bootcamp-day3
ls
```

`ls` 결과가 아래와 같으면 정상입니다.

```
README.md    egfr/    pnck/
```

---

## 3. API 키 설정 (한 번만)

**macOS / Linux**

아래 명령을 붙여넣되, `sk-ant-api03-...` 부분만 좌석 카드의 키로 바꿔 주세요.

```bash
mkdir -p ~/.claude
cat > ~/.claude/settings.json <<'EOF'
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-api03-...",
    "ANTHROPIC_MODEL": "claude-sonnet-5"
  },
  "permissions": {
    "deny": ["Read(**/.env*)", "Write(**/.env*)"]
  }
}
EOF
```

개인 Claude 구독을 쓰고 계셨다면, 기존 키가 남아 있는지 확인하고 정리합니다.

```bash
unset ANTHROPIC_API_KEY
grep -n ANTHROPIC_API_KEY ~/.zshrc ~/.bashrc ~/.bash_profile 2>/dev/null
```

**마지막 명령에서 뭔가 출력되면** 그 줄이 새 터미널마다 되살아납니다.
실습 중에는 해당 줄 앞에 `#`을 붙여 주석 처리하고, 끝난 뒤 되돌리세요.

설정이 제대로 됐는지 확인합니다.

```bash
cat ~/.claude/settings.json
```

키가 보이고 JSON 형식이 깨지지 않았으면 정상입니다.

**Windows (PowerShell)**

```powershell
$key = "sk-ant-api03-..."

$dir = Join-Path $env:USERPROFILE ".claude"
New-Item -ItemType Directory -Force -Path $dir | Out-Null

$cfg = [ordered]@{
  env = [ordered]@{
    ANTHROPIC_API_KEY = $key
    ANTHROPIC_MODEL   = "claude-sonnet-5"
  }
  permissions = @{ deny = @("Read(**/.env*)", "Write(**/.env*)") }
}
$cfg | ConvertTo-Json -Depth 5 |
  Set-Content -Path (Join-Path $dir "settings.json") -Encoding utf8

Remove-Item Env:\ANTHROPIC_API_KEY -ErrorAction SilentlyContinue
```

> 키는 실습 종료 다음 날 자동으로 만료됩니다. 외부에 절대 공유하지 마세요.

---

## 4. 실습 시작

### 전반부

```bash
cd ~/bootcamp-day3/egfr
claude
```

### 후반부

```bash
cd ~/bootcamp-day3/pnck
claude
```

**꼭 하위 폴더 안에서 실행하세요.** `bootcamp-day3` 최상위에서 `claude`를 켜면 그 폴더의 `CLAUDE.md`를 읽지 못해 에이전트가 환경을 인식하지 못합니다.

---

## 5. 시작 직후 확인

Claude Code에 들어가면 두 가지만 확인합니다.

```
/status     인증이 "API key"로 표시되면 정상
/mcp        연결된 외부 도구 목록
```

`/status`에 개인 구독 계정이 보이면 3장의 키 설정이 적용되지 않은 것입니다.

---

## 6. 폴더에 있는 파일들

```
bootcamp-day3/
├── egfr/                    ← 전반부 작업 폴더
│   ├── CLAUDE.md            항상 읽히는 환경·복구 안내
│   ├── harness_guide.md     이번 과제의 계약서 (빈칸을 직접 채웁니다)
│   └── data/datalist.md     만들어진 산출물을 기록하는 대장
└── pnck/                    ← 후반부 작업 폴더
    ├── CLAUDE.md            항상 읽히는 환경·복구 안내
    └── validation_mode.md   검증 기준 계약서 (관점을 직접 고릅니다)
```

**`CLAUDE.md`** 는 매 세션 자동으로 읽힙니다. 손대지 않아도 됩니다.

**`harness_guide.md` / `validation_mode.md`** 는 여러분이 채우고 고치는 문서입니다.
에이전트에게 참고시킬 때는 프롬프트에 `@harness_guide.md` 처럼 파일명을 붙여 주세요.

산출물은 각 폴더의 `outputs/` 아래에 쌓입니다.

---

## 7. 알아두면 편한 것

| 이런 순간 | 이렇게 |
|---|---|
| 파일을 만들기 전에 계획부터 보고 싶다 | `Shift+Tab` (Plan mode) |
| 지금 작업을 멈추고 싶다 | `Esc` |
| 방금 작업을 되돌리고 싶다 | `/rewind` |
| 책상(컨텍스트)이 꽉 찼다 | `/compact` |
| 이 파일 좀 봐줘 | `@파일명` |
| 터미널 명령을 직접 실행하고 싶다 | `!명령어` |
| 남은 공간이 궁금하다 | `/context` |
| 지금까지 쓴 비용이 궁금하다 | `/cost` |
| 전체 기능 목록 | `/help` |

---

## 8. 잘 안 될 때

| 증상 | 확인할 것 |
|---|---|
| `claude` 실행이 안 됨 | `claude --version`으로 설치 확인 |
| `/status`에 개인 계정이 뜸 | 3장 재실행 후 Claude Code 재시작 |
| 인증 오류(401) | 키가 잘렸는지 확인. 받은 메일에서 키를 다시 복사 |
| `/mcp`에 아무것도 없음 | 강사에게 알려주세요 |
| Colab GPU를 못 받음 | 강사에게 알려주세요. 혼자 CPU로 바꾸지 마세요 |
| 에이전트가 멈춘 것 같음 | 오래 걸리는 작업일 수 있습니다. `/cost`로 진행 여부 확인 |

막히면 혼자 오래 붙잡지 말고 손을 들어 조교에게 질문해주세요.

---

## 9. 실습이 끝나면

개인 환경을 정리하여 실습에 사용된 설정을 제거합니다.

```bash
rm ~/.claude/settings.json
```
