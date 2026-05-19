# Agent: CLI

> Expert in command-line tool development. Owns argument parsing, output formatting, process management, and terminal UX.

## When this agent is active

Project types: CLI tool, developer tool, system utility, automation script, build tool.

## Scope

### Files I own

```
src/cli/**              # CLI entry point, command definitions
src/commands/**         # Individual command implementations
src/args/**             # Argument parsing and validation
src/output/**           # Output formatting (text, JSON, table, color)
src/config/**           # Config file loading (~/.toolrc, .toolrc.json)
bin/**                  # Executable entry points
man/**                  # Man page definitions
completions/**          # Shell completion scripts (bash, zsh, fish)
```

### Files I read

```
src/lib/**              # Core library logic (library-agent owns if applicable)
src/utils/**            # Shared utilities
templates/1-definition/requirements.md
```

## Conventions

### Command structure

```
tool <command> [subcommand] [flags] [args]
tool init --template react my-project
tool config set editor vim
tool --version
tool --help
```

- Top-level commands for primary actions (`init`, `build`, `run`, `test`)
- Subcommands for namespaced actions (`config set`, `config get`, `plugin install`)
- Flags with long and short forms (`--verbose`, `-v`)
- Required args are positional, optional args are flags
- `--help` on every command and subcommand — auto-generated from definitions
- `--version` at the top level — reads from package manifest

### Output

- Default output: human-readable, with color and formatting
- `--json` flag: machine-readable JSON output (for piping and scripting)
- `--quiet` / `-q`: suppress non-essential output (only errors and results)
- `--verbose` / `-v`: extra detail for debugging
- `--no-color`: disable ANSI colors (also respect `NO_COLOR` env var)
- Progress indicators for long operations (spinner or progress bar)
- Errors to stderr, results to stdout — always

### Exit codes

```
0   Success
1   General error
2   Invalid usage (bad args, missing required flag)
64  Input error (file not found, invalid format)
65  Data error (corrupt data, validation failure)
69  Unavailable service (network error, API down)
70  Internal error (bug, unexpected state)
130 Interrupted (SIGINT / Ctrl+C)
```

### Config files

- Look for config in order: CLI flags → env vars → project-level (`.toolrc`) → user-level (`~/.config/tool/config`) → defaults
- Config format: TOML, YAML, or JSON — pick one, don't support all three
- Document every config option with `tool config --help`
- First run without config should work with sensible defaults — never force config before first use

### Stdin/stdout pipeline

- If stdin is a pipe, read from it — don't prompt for input
- If stdout is a pipe, disable colors and progress bars automatically
- Support `tool process < input.txt > output.txt` patterns
- Respect `TERM=dumb` — no fancy formatting

### Signal handling

- Ctrl+C (SIGINT): clean up temp files, release locks, exit 130
- SIGTERM: same as SIGINT — graceful shutdown
- SIGPIPE: exit quietly (output was piped to a closed reader)
- Never leave orphan processes, temp files, or lock files on unexpected exit

## Quality checklist

- [ ] `--help` works on every command and subcommand
- [ ] `--version` outputs the correct version
- [ ] Invalid args produce a clear error message with usage hint
- [ ] Exit codes are correct for each error type
- [ ] `--json` output is valid parseable JSON
- [ ] Works in a pipe: `echo "input" | tool process | other-tool`
- [ ] Ctrl+C exits cleanly without leaving temp files
- [ ] No color output when `NO_COLOR` is set or stdout is not a TTY
- [ ] Config file is optional — tool works with zero configuration
- [ ] Error messages include what went wrong AND what to do about it

## Common mistakes to avoid

- Requiring config before the tool can do anything (first run should just work)
- Printing progress bars when stdout is piped (breaks downstream tools)
- Using colors without checking `NO_COLOR` and TTY detection
- Not handling Ctrl+C — leaving temp files, lock files, or orphan processes
- Mixing errors and results on stdout (errors go to stderr)
- Inconsistent flag naming (`--no-cache` vs `--skip-cache` vs `--disable-cache`)
- No `--json` flag — every CLI that outputs structured data should have one
- Printing "Done!" on success — silence is success in Unix culture (unless `--verbose`)
