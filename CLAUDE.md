# One Punch

A one-shot prompt arena: players bring their own model, type a prompt (max 1000 characters) on our interface, and the model one-shots an artifact (website or game). Artifacts are pitted against other players' one-shots and voted on.

Design decisions (settled and still open) live in `docs/DECISIONS.md`. Read it before building anything.

## Working rules

- Commit and push all work before finishing a turn, every time. The user clears context often and hands off to new agents, so anything not pushed is lost.
- Push to the current working branch (`git push -u origin <branch>`); create a named branch first if on a detached HEAD. Don't open a PR unless asked.
