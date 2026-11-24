# Frontend Performance

An [Agent Skill](https://agentskills.io/specification) for finding and reducing slow frontend builds, CI checks, and releases. It helps an agent turn job logs into a concrete hypothesis and check whether a proposed change improves the wait that developers actually experience.

Use it to diagnose build cache misses, repeated TypeScript work, browser startup and retries, or time spent creating and verifying a release image. It works from your project's workflows and evidence. The skill contains instructions and references and requires no extra runtime or credentials to load.

## Install in Codex

Download and unzip [frontend-performance.zip](https://github.com/Harivarshan/frontend-performance-skill/releases/download/v1.0.0/frontend-performance.zip). Copy the extracted `frontend-performance` folder into your project's `.agents/skills/` directory. To make the skill available across projects, use `~/.agents/skills/`. Start a new Codex session in your project. [Codex skill locations](https://developers.openai.com/api/reference/cli)

The [skill source](skills/frontend-performance/SKILL.md) is also available in this repository. For other Agent Skills clients, use the skill location documented by your client.

## Try it

> Use $frontend-performance to compare the last few successful PR runs. Show which job controls the wait, include failed or retried runs in the reliability context, and propose one benchmark that keeps our existing checks.

> Use $frontend-performance to test whether changing our compiler-cache compression saves time. Include restore, build, archive and upload, and tell me whether it shortens the overall PR wait.

## License

[MIT](LICENSE)
