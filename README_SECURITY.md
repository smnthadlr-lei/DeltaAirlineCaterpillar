Security checklist and scanning steps

This file documents recommended checks to prevent accidental leakage of secrets and how to remediate if something is found.

1) Quick scan locally
- Search for common secret keywords:
  - token, secret, password, api_key, client_secret, PRIVATE KEY, BEGIN RSA
  - Use this command (PowerShell):
    Select-String -Path . -Pattern "token|secret|password|api_key|apikey|client_secret|BEGIN PRIVATE KEY|BEGIN RSA PRIVATE KEY|aws_access_key|aws_secret" -SimpleMatch -Recurse

2) Scan git history (recommended)
- Install and run truffleHog to detect secrets in repo history:
  - Install: `pip install truffleHog` or use the binary
  - Run: `trufflehog filesystem .`
- Or use `git-secrets` to scan commits:
  - Install `git-secrets`, then run `git secrets --scan --recursive` from repo root.

3) If you find a secret
- Treat it as compromised. Rotate the secret immediately in the system that issued it (cloud provider, API provider).
- Remove the secret from the git history using `git filter-repo` or `bfg repo-cleaner`.
- After history rewrite, force-push to remote and notify any collaborators (they must re-clone).

4) Prevent future leaks
- Add sensitive files to `.gitignore` (this repo already contains a `.gitignore`).
- Use environment variables for runtime secrets and do not store credentials in source.
- Use a secrets manager (Azure Key Vault, AWS Secrets Manager, HashiCorp Vault, GitHub Actions Secrets) for CI/CD.
- Add pre-commit hooks with `git-secrets` or `pre-commit` to block commits containing secrets.

5) Static-site specifics
- Remove server-side template tokens from files intended for static hosting (e.g., `th:href="@{...}"`) so templates are not accidentally published.
- Ensure all external resources in HTML/JS use `https://` (this project already does).

6) Useful commands
- Quick grep for email-like strings:
  - PowerShell: `Select-String -Path . -Pattern "[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}" -AllMatches -Recurse`
- Scan for private key headers:
  - PowerShell: `Select-String -Path . -Pattern "BEGIN (RSA )?PRIVATE KEY" -Recurse`

7) Next steps I can do for you (pick any):
- Run the above scans here and produce a report (I can run repository text scans for you).
- Add `git-secrets` pre-commit hook and a `.env.example` file.
- Rewrite `index.html` to fully remove template attributes (already partially done) and ensure all asset paths are consistent (`assets/` vs `assests/`).

If you want me to run any automated scans or apply additional fixes now, tell me which scan or change to perform and I'll execute it.