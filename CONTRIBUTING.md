# Contributing to myPOS Developer Skills

## Adding a new skill

1. Create a new directory under `skills/` named `mypos-{integration-type}/`
2. Add `SKILL.md` following the format in existing skills
3. Add `agents/openai.yaml` with interface configuration
4. Add `references/README.md` as the navigation index
5. Add individual reference files for each feature area
6. Update the root `README.md` skills table, `GEMINI.md`, and `CLAUDE.md`

## Improving an existing skill

- **SKILL.md**: Keep it concise. The decision tree, rules, and workflow are the high-value content.
- **Reference files**: Stay minimal — one code snippet per feature, a doc URL, reading order, and see-also links. Avoid prose.
- **Doc URLs**: All links must point to `https://developers.mypos.com/` and match the actual page structure of the developer documentation site.

## Reference file format

```markdown
> Prefer the latest docs: https://developers.mypos.com/[path]

## [Feature Name]

[minimal working code snippet]

## Reading Order

1. This file.
2. Next reference file for the logical next step.

## See Also

- references/related-file.md
```

## Repository layout

`skills/` is the single source of truth. The repo root is itself the plugin for Claude Code (`.claude-plugin/`), Cursor (`.cursor-plugin/`) and Gemini CLI (`gemini-extension.json`), so there are no generated copies to keep in sync.

## Reporting issues and security

- Bugs, inaccuracies and suggestions: open a GitHub issue. Pull requests are welcome.
- These skills are guidance for AI assistants. The [myPOS developer documentation](https://developers.mypos.com/) is authoritative; if they disagree, the docs win and the skill should be fixed.
- Security concerns or anything involving real credentials: do **not** open a public issue. Email integrations@mypos.com.
- For help with your own integration or account, contact myPOS through the [Partner Portal](https://partners.mypos.com/en) or integrations@mypos.com rather than GitHub issues.

## License

By contributing you agree your contributions are licensed under Apache 2.0.
