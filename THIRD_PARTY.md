# Third-party code

This repository includes two pieces of third-party work. Both use the MIT
licence. Each keeps its copyright notice in a `LICENSE` file beside it.

## humanizer

| Field | Value |
|---|---|
| Upstream | https://github.com/blader/humanizer |
| Copyright | (c) 2025 Siqi Chen |
| Licence | MIT, in `plugins/writing/skills/humanizer/LICENSE` |
| Based on | Upstream version 3.0.0 |
| Upstream checked | `0ef9fe5` (version 3.1.0), 2026-11-09 |
| Path | `plugins/writing/skills/humanizer/` |

This copy is a modified version of upstream 3.0.0. Upstream has since released
3.1.0. Changes from 3.1.0 aren't merged.

## ste-lint.py

| Field | Value |
|---|---|
| Upstream | https://github.com/danyuchn/asd-ste100-skill, `scripts/ste-lint.py` |
| Copyright | (c) 2026 Dustin Yuchen Teng |
| Licence | MIT, in `plugins/writing/skills/technical-writing/scripts/LICENSE-ste-lint` |
| Pinned commit | `d48bfca` (skill version 0.4.0), copied 2026-11-09 |
| Path | `plugins/writing/skills/technical-writing/scripts/ste-lint.py` |
| Changes | None. The file is a byte-for-byte copy. |

The linter checks the structural rules of ASD-STE100 Simplified Technical
English: semicolons, sentence length, phrasal verbs, nominalisation, marketing
adjectives, synonym rotation, passive voice, and compound tenses. It doesn't
check ASD's dictionary.

**Review status: partial.** On 2026-11-09 we checked the imports and file I/O
only:

- It imports the standard library only: `argparse`, `bisect`, `contextlib`,
  `html.entities`, `io`, `json`, `os`, `re`, `sys`, and `tempfile`.
- It reads the files named on its command line or standard input.
- It writes files only in its `--selftest`, inside a temporary directory.
- It makes no network or subprocess calls.

Review the remaining logic before you make the linter a required check. To
update it, copy the file from a newer upstream commit and update this table.
