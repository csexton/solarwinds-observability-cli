# solarwinds-observability

A simple command-line tool to fetch logs from SolarWinds Observability's log
API using app-specific API tokens, config-based defaults, and CLI options.

Built in Ruby with no external dependencies.

---

## Setup

You'll need:

- Ruby installed (`ruby --version`)
- An API token from SolarWinds Observability (either via environment variable or config file, see [Configuration Reference](#configuration-reference) below)
- A copy of the script (`solarwinds-observability`)

### 1. Install Ruby

Most macOS and Linux systems already have Ruby. To check:

```bash
ruby --version
```

If you don't have Ruby, install it via your system package manager (`brew`, `apt`, etc.).

### 2. Get the script

You can either:

**Clone the full repo**:
```bash
git clone https://github.com/csexton/solarwinds-observability-cli.git
```

**Or just download the script**:
```bash
curl -o solarwinds-observability https://raw.githubusercontent.com/csexton/solarwinds-observability-cli/main/solarwinds-observability
chmod +x solarwinds-observability
```

### 3. Configure your API token

You can set the token using an environment variable:

```bash
export SOLARWINDS_API_TOKEN="your-api-token"
```

Or you can create a config file at `~/.solarwinds_observability.yml`:

See [Configuration Reference](#configuration-reference) for the format.

---


## Usage

### Basic

```bash
./solarwinds-observability <filter>
```

Example:

If you wanted to search for "ActiveJob" in the logs

```bash
./solarwinds-observability ActiveJob
```

### Use a specific app from your config

```bash
./solarwinds-observability ActiveJob --app my-app-production
```

### Use raw token from environment

```bash
export SOLARWINDS_API_TOKEN="your-token-here"
./solarwinds-observability ActiveJob
```

---

## CLI Options

| Option              | Description                                         |
|---------------------|-----------------------------------------------------|
| `<filter>`          | Required log search query                           |
| `--app NAME`        | Select token by app name from config                |
| `--pages N`         | Number of pages to fetch                            |
| `--page-size N`     | Logs per page (default: 50)                         |
| `--direction DIR`   | `backward` or `forward`                             |
| `--start-time TIME` | ISO8601 UTC start time                              |
| `--end-time TIME`   | ISO8601 UTC end time                                |
| `--group NAME`      | Filter logs by group                                |
| `--json`            | Print full JSON response instead of parsed lines    |
| `--no-color`        | Disable colored terminal output                     |
| `--list-apps`       | Print available apps from config and exit           |

---

## Examples

### With filters and colorized output

```bash
./solarwinds-observability --pages 3 "order_id=1234"
```

### Dump raw JSON

```bash
./solarwinds-observability --json "error"
```

### See configured apps

```bash
./solarwinds-observability --list-apps
```

---

## Configuration Reference

`~/.solarwinds_observability.yml`

```yaml
default: my-app-production
apps:
  my-app-production: "api-token-1"
  my-app-staging: "api-token-2"
cli_options:
  pages: 2
  page_size: 100
  direction: backward
  color: true
```

You can override any `cli_options` via command-line flags.

---

## Token Priority Logic

When the script runs, it selects the API token using the following priority:

1. If `--app <name>` is provided, that token is used.
2. Else if the environment variable `SOLARWINDS_API_TOKEN` is set, it is used.
3. Else, if the YAML config file has a `default:` app, its token is used.
4. If none of these are present, the script exits with an error.


---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

## Contributing

PRs welcome. Please keep the script dependency-free and tested on macOS/Linux with a stock Ruby install.

