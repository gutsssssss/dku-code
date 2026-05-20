# DKU Code

DKU Code is a CLI agent project centered around the `claw` runtime.
This README focuses on one thing: **how to deploy and start the project** quickly.

## What You Need

- Git
- Rust toolchain (`rustup`, `cargo`, `rustc`)
- PowerShell (Windows) or a Unix shell (macOS/Linux)
- One model API key (for example:
  - `OPENAI_API_KEY` for OpenAI-compatible endpoints
  - `ANTHROPIC_API_KEY` for Anthropic)

---

## 1) Clone the Project

```bash
git clone <your-repository-url>
cd claw-code-main
```

---

## 2) Build (Deploy) the CLI

From the repository root:

```bash
cd rust
cargo build --workspace
```

Build output:

- Windows: `rust\target\debug\claw.exe`
- macOS/Linux: `rust/target/debug/claw`

---

## 3) Configure Provider Environment Variables

### Option A: DeepSeek (OpenAI-compatible)

PowerShell:

```powershell
$env:OPENAI_API_KEY = "<YOUR_API_KEY>"
$env:OPENAI_BASE_URL = "https://api.deepseek.com"
Remove-Item Env:\ANTHROPIC_API_KEY -ErrorAction SilentlyContinue
```

### Option B: Anthropic

PowerShell:

```powershell
$env:ANTHROPIC_API_KEY = "<YOUR_API_KEY>"
Remove-Item Env:\OPENAI_API_KEY, Env:\OPENAI_BASE_URL -ErrorAction SilentlyContinue
```

---

## 4) Start the Project

### Interactive chat mode (recommended)

PowerShell:

```powershell
cd E:\claw-code-main\claw-code-main\rust
.\target\debug\claw.exe --model "xai/deepseek-v4-flash" --reasoning-effort low --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```

### One-shot prompt mode

PowerShell:

```powershell
cd E:\claw-code-main\claw-code-main\rust
.\target\debug\claw.exe --model "xai/deepseek-v4-flash" prompt "Hello"
```

---

## 5) Basic Health Checks

```powershell
cd E:\claw-code-main\claw-code-main\rust
.\target\debug\claw.exe --help
.\target\debug\claw.exe doctor
```

---

## 6) Troubleshooting

- **`cargo` not found**
  - Rust is not installed or PATH is not refreshed. Reopen terminal after install.
- **`failed to remove ... claw.exe (os error 5)`**
  - A running `claw.exe` process is locking the binary. Close it and rebuild.
- **PowerShell tool not found**
  - Ensure `powershell` is in PATH for the current session.
- **API 400 from provider**
  - Verify model name, base URL, and API key match the target provider.

---

## 7) Security Notes

- Never commit real API keys.
- If a key is exposed in terminal/chat history, revoke it and create a new one.

---

## Quick Start (Windows Copy/Paste)

```powershell
cd E:\claw-code-main\claw-code-main\rust
cargo build --workspace
$env:OPENAI_API_KEY = "<YOUR_API_KEY>"
$env:OPENAI_BASE_URL = "https://api.deepseek.com"
Remove-Item Env:\ANTHROPIC_API_KEY -ErrorAction SilentlyContinue
.\target\debug\claw.exe --model "xai/deepseek-v4-flash" --reasoning-effort low --allowedTools PowerShell,read_file,write_file,edit_file,glob_search,grep_search
```
