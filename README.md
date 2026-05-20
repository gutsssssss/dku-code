# DKU Code

DKU Code is a CLI agent runtime based on `claw`.
This guide covers a complete deployment/startup flow on Windows, including:

- `deepseek-v4-pro` usage
- reasoning mode toggle
- `.env` persistence
- tool-enabled chat mode

## Prerequisites

- Git
- Rust toolchain (`rustup`, `cargo`, `rustc`)
- Windows PowerShell
- A valid DeepSeek API key

---

## 1) Clone and Build

```powershell
git clone <your-repository-url>
cd E:\claw-code-main\claw-code-main\rust
cargo build --workspace
```

Build output:

- Windows binary: `E:\claw-code-main\claw-code-main\rust\target\debug\claw.exe`

---

## 2) Configure Environment (Recommended: `.env`)

`claw` reads a `.env` file from the **current working directory** where you launch `claw.exe`.

### Create `.env` (UTF-8 without BOM, PowerShell 5 compatible)

Run this in the directory where you will start `claw.exe`:

```powershell
cd C:\Users\Administrator\Desktop\工作\cs207代码

$envText = @"
OPENAI_API_KEY=<YOUR_DEEPSEEK_API_KEY>
OPENAI_BASE_URL=https://api.deepseek.com
CLAW_DEEPSEEK_V4_REASONING=1
"@
$utf8NoBom = New-Object System.Text.UTF8Encoding($false)
[System.IO.File]::WriteAllText(".\.env", $envText, $utf8NoBom)
```

Notes:

- `OPENAI_API_KEY`: required
- `OPENAI_BASE_URL`: required for DeepSeek (`https://api.deepseek.com`)
- `CLAW_DEEPSEEK_V4_REASONING`: optional (`1` to enable, unset/`0` to disable)

---

## 3) Start in Interactive Mode

### DeepSeek V4 Pro + reasoning + tools (recommended)

```powershell
cd C:\Users\Administrator\Desktop\工作\cs207代码
E:\claw-code-main\claw-code-main\rust\target\debug\claw.exe --model "xai/deepseek-v4-pro" --reasoning-effort high --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```

### Fast model switch (V4 Flash)

```powershell
cd C:\Users\Administrator\Desktop\工作\cs207代码
E:\claw-code-main\claw-code-main\rust\target\debug\claw.exe --model "xai/deepseek-v4-flash" --reasoning-effort low --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```

### One-shot prompt mode

```powershell
cd C:\Users\Administrator\Desktop\工作\cs207代码
E:\claw-code-main\claw-code-main\rust\target\debug\claw.exe --model "xai/deepseek-v4-pro" prompt "Hello"
```

---

## 4) Reasoning Mode Behavior

- Default behavior: reasoning is off unless enabled
- Enable reasoning:
  - set `CLAW_DEEPSEEK_V4_REASONING=1`
  - use `--reasoning-effort low|medium|high`
- Disable reasoning quickly:

```powershell
Remove-Item Env:\CLAW_DEEPSEEK_V4_REASONING -ErrorAction SilentlyContinue
```

---

## 5) Health Checks

```powershell
cd E:\claw-code-main\claw-code-main\rust
.\target\debug\claw.exe --help
.\target\debug\claw.exe doctor
```

---

## 6) Troubleshooting

- **`missing_credentials` asks for Anthropic key while using DeepSeek**
  - Usually `.env` encoding issue (BOM). Recreate `.env` with UTF-8 no BOM (section 2).
  - Confirm you launch `claw.exe` from the same directory that contains `.env`.

- **`PowerShell executable not found`**
  - Rebuild to latest code (`cargo build --workspace`), which fixes Windows shell detection.
  - Ensure PowerShell is on PATH in your current terminal:
    `C:\Windows\System32\WindowsPowerShell\v1.0;C:\Windows\System32`

- **`api returned 400 ... reasoning_content ... must be passed back`**
  - Use the latest build from this repository (reasoning continuity fixes are included).
  - Keep reasoning enabled via `CLAW_DEEPSEEK_V4_REASONING=1`.

- **`failed to remove ... claw.exe (os error 5)`**
  - Existing `claw.exe` process is still running; close it or kill process, then rebuild.

---

## 7) Security Notes

- Never commit real API keys.
- Add `.env` to `.gitignore`.
- If a key is exposed in chat/terminal history, revoke and rotate it immediately.

---

## Quick Start (Windows Copy/Paste)

```powershell
cd E:\claw-code-main\claw-code-main\rust
cargo build --workspace

cd C:\Users\Administrator\Desktop\工作\cs207代码
$envText = @"
OPENAI_API_KEY=<YOUR_DEEPSEEK_API_KEY>
OPENAI_BASE_URL=https://api.deepseek.com
CLAW_DEEPSEEK_V4_REASONING=1
"@
$utf8NoBom = New-Object System.Text.UTF8Encoding($false)
[System.IO.File]::WriteAllText(".\.env", $envText, $utf8NoBom)

E:\claw-code-main\claw-code-main\rust\target\debug\claw.exe --model "xai/deepseek-v4-pro" --reasoning-effort high --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```
