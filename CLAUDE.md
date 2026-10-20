# CLAUDE.md — WeaveSol

@AGENTS.md

## Claude Code specifics
- Dev server (once code exists): the `weavesol` entry in `.claude/launch.json` (http://localhost:8810).
- Review rules for the review skills: `.claude/review-rules/` (code, security, spec, ui, ux).
- Project skills:
  - `.claude/skills/silksol-form-capture/SKILL.md` — capture one SilkSol screen into a `docs/forms/F-xxx` spec (Phase 0).
  - `.claude/skills/weavesol-screen/SKILL.md` — build one WeaveSol screen from its spec (Phase 4 onward).
- SilkSol walkthrough uses computer use with the app granted as `C:\SilkSol\SilkSol.exe`. The PC
  must be unlocked. Close any extra SilkSol copies you open; don't close the owner's own window.
- Local tools: PHP `C:/xampp/php/php.exe` (8.2.12, no `intl` / `zip` / `sodium`), MySQL 8.4
  (service `MySQL84`). The 32-bit Jet OLE DB driver (run through
  `C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`) reads `.mdb` files; always work on a copy.
