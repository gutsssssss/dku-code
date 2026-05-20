# DKU Code

DKU Code is a CLI agent runtime based on `claw`.
This guide covers a complete deployment/startup flow on Windows, including:

- `deepseek-v4-pro` usage
- reasoning mode toggle
- tool-enabled chat mode

Path placeholders used in this document:

- `<REPO_ROOT>`: local path where this repository is cloned
- `<WORK_DIR>`: working directory where you run `claw` sessions

## Prerequisites

- Git
- Rust toolchain (`rustup`, `cargo`, `rustc`)
- Windows PowerShell
- A valid DeepSeek API key

---

## 1) Install Rust (Windows)

If Rust is not installed, install it first:

```powershell
winget install --id Rustlang.Rustup -e
```

After installation, **close and reopen** PowerShell, then verify:

```powershell
rustc --version
cargo --version
```

If `cargo` is still not found in the current session, temporarily patch PATH:

```powershell
$env:Path = "$env:USERPROFILE\.cargo\bin;$env:Path"
```

---

## 2) Clone and Build

```powershell
git clone <your-repository-url>
cd <REPO_ROOT>\rust
cargo build --workspace
```

Build output:

- Windows binary: `<REPO_ROOT>\rust\target\debug\claw.exe`

---

## 3) Configure Environment Variables (Quick Start style)

Set these variables in the same PowerShell session before launching `claw.exe`:

```powershell
$env:OPENAI_API_KEY = "<YOUR_DEEPSEEK_API_KEY>"
$env:OPENAI_BASE_URL = "https://api.deepseek.com"
$env:CLAW_DEEPSEEK_V4_REASONING = "1"
$env:Path = "C:\Windows\System32\WindowsPowerShell\v1.0;C:\Windows\System32;$env:Path"
```

Notes:

- `OPENAI_API_KEY`: required
- `OPENAI_BASE_URL`: required for DeepSeek (`https://api.deepseek.com`)
- `CLAW_DEEPSEEK_V4_REASONING`: optional (`1` to enable, unset/`0` to disable)
- PATH patch ensures PowerShell tool calls can be discovered

---

## 4) Start in Interactive Mode

### DeepSeek V4 Pro + reasoning + tools (recommended)

```powershell
cd <WORK_DIR>
<REPO_ROOT>\rust\target\debug\claw.exe --model "xai/deepseek-v4-pro" --reasoning-effort high --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```

### Fast model switch (V4 Flash)

```powershell
cd <WORK_DIR>
<REPO_ROOT>\rust\target\debug\claw.exe --model "xai/deepseek-v4-flash" --reasoning-effort low --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```

### One-shot prompt mode

```powershell
cd <WORK_DIR>
<REPO_ROOT>\rust\target\debug\claw.exe --model "xai/deepseek-v4-pro" prompt "Hello"
```

---

## 5) Reasoning Mode Behavior

- Default behavior: reasoning is off unless enabled
- Enable reasoning:
  - set `CLAW_DEEPSEEK_V4_REASONING=1`
  - use `--reasoning-effort low|medium|high`
- Disable reasoning quickly:

```powershell
Remove-Item Env:\CLAW_DEEPSEEK_V4_REASONING -ErrorAction SilentlyContinue
```

---

## 6) Health Checks

```powershell
cd <REPO_ROOT>\rust
.\target\debug\claw.exe --help
.\target\debug\claw.exe doctor
```

---

## 7) Troubleshooting

- **`missing_credentials` asks for Anthropic key while using DeepSeek**
  - Re-run the environment variable setup block in section 3.
  - Confirm you are launching `claw.exe` in the same PowerShell session where variables were set.

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

## 8) Security Notes

- Never commit real API keys.
- Add `.env` to `.gitignore`.
- If a key is exposed in chat/terminal history, revoke and rotate it immediately.

---

## Quick Start (Windows Copy/Paste)

```powershell
cd <REPO_ROOT>\rust
cargo build --workspace

cd <WORK_DIR>
$env:OPENAI_API_KEY = "<YOUR_DEEPSEEK_API_KEY>"
$env:OPENAI_BASE_URL = "https://api.deepseek.com"
$env:CLAW_DEEPSEEK_V4_REASONING = "1"
$env:Path = "C:\Windows\System32\WindowsPowerShell\v1.0;C:\Windows\System32;$env:Path"

<REPO_ROOT>\rust\target\debug\claw.exe --model "xai/deepseek-v4-pro" --reasoning-effort high --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```
