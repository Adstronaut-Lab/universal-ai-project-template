# Universal Token Efficient Agent Rules

## Core
- Read relevant files before changing them.
- Do not reread unchanged files.
- Prefer targeted edits over rewrites.
- Keep reasoning thorough and output concise.
- User instructions override this file.

## Output
- Start with the result.
- Skip praise, preambles, restatements, and closing fluff.
- Do not explain obvious code.
- Use bullets only when they improve scanning.
- Do not add unsolicited alternatives or future work.

## Coding
- Implement the simplest working solution.
- Follow existing project patterns.
- Avoid abstractions for single-use logic.
- Do not invent APIs, paths, versions, flags, or package names.
- Verify with local code or official docs before asserting details.

## Context Control
- Open only files needed for the task.
- Skip files over 100 KB unless required.
- Summarize large outputs instead of pasting them.
- Report uncertainty instead of guessing.

## Validation
- Run the narrowest useful test or check.
- If validation cannot run, say why.
- Report changed files and verification only.
