**English** | [日本語](README_ja.md)

# covautolib (sanitized release)

Public-friendly snapshot of the internal **covautolib** utilities that automate Coverity® Connect operations such as project introspection, snapshot comparisons, and user management. All company-specific hosts, credentials, and addresses have been replaced with environment-configurable values so the tooling can be adapted to other environments.

## Features
- Wraps the Coverity REST API (v2) for listing projects, streams, snapshots, issues, and users.
- Exports results to CSV/JSON and manipulates them with `pandas`/`openpyxl`.
- Sends status e-mails with SMTP (now fully configurable).
- Provides spreadsheet helpers for GitLab/Coverity roster housekeeping.

## Requirements
- Python 3.9+ (tested with CPython on Windows/Linux).
- Python packages: `requests`, `urllib3`, `openpyxl`, `pandas` (install via `pip install -r requirements.txt` when available, or install manually).

## Environment configuration
Credentials, proxy settings, and server endpoints are read from environment variables. Copy the template and adjust values before running any scripts:

```powershell
# Windows PowerShell
Copy-Item .env.example .env
notepad .env  # edit values

# Load the variables for the current session
Get-Content .env | Where-Object { $_ -and $_ -notmatch '^#' } | ForEach-Object { $name, $value = $_ -split '=', 2; [System.Environment]::SetEnvironmentVariable($name.Trim(), $value.Trim()) }
```

### Required variables
| Variable | Purpose |
| --- | --- |
| `COVAUTHUSER` / `COVAUTHKEY` | Coverity API credentials (basic auth). |
| `COVAUTO_API_BASE_URL` | Base URL of your Coverity instance, e.g. `https://coverity.example.com/api/v2`. |
| `COVAUTO_HTTP_PROXY` / `COVAUTO_HTTPS_PROXY` | Default proxy settings (optional). |
| `COVAUTO_ALT_HTTP_PROXY` / `COVAUTO_ALT_HTTPS_PROXY` | Fallback proxy settings (optional). |
| `COVAUTO_RUNTIME_HTTP_PROXY` / `COVAUTO_RUNTIME_HTTPS_PROXY` | Overrides applied at runtime by `COVProj` (optional). |
| `COVAUTO_SMTP_HOST` / `COVAUTO_SMTP_PORT` | SMTP server used by `GLMail` (defaults to `localhost:25`). |
| `COVAUTO_BASE_DIR` | Root working directory (defaults to `~/cov`). |
| `COVAUTO_SHARE_DIR` | Shared resource directory (defaults to `<base_dir>`).

All variables can also be set through your shell profile, CI secrets, or any secret-management tool.

## Usage
1. Install dependencies (`pip install requests urllib3 openpyxl pandas`).
2. Configure environment variables as described above.
3. Execute the desired helper, for example:
   ```powershell
   python covautolib_3.py --help
   ```
   Many routines are exposed as class methods; adapt or import them into your own scripts as needed.

## Development notes
- `covautolib_3.py` remains a large legacy script; consider extracting reusable components into modules when extending functionality.
- To keep private data out of the tree, ensure any new constants come from env vars or external config files.
- Generated artefacts (`*.csv`, `*.log`, `__pycache__`) are ignored by default; clean them before publishing.

## License
This repository retains the original license (`LICENSE`). Review it before redistributing.
