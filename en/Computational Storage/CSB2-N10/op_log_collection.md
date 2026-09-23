# Log Collection

While it runs, the device keeps a diary of what happens, and that diary is the **log**. When you troubleshoot a problem, catching the right logs often leads you to the cause quickly.

This document is **scenario-driven**: it first walks through the most common situations using "what went wrong", and for each one tells you which log to collect, how to collect it, and how to read the result. **At the end** it provides the complete `fflog` command reference for when you need to look something up. You do not need any programming knowledge, all commands can be copied directly, and the sample outputs come from a real device; the actual content depends on your device.

## Log In to the BMC First [step]

Every scenario starts with logging in to the BMC command line (the logs are stored on the BMC). Choose either method:

- **SSH**: `ssh bmc@<aBMC management IP>`; for credentials, see [Accessing the BMC](start_login_bmc.md);
- **BMC Shell**: open a command line in the aBMC web interface through **Remote console → BMC Shell**.

![Open the BMC Shell in the aBMC web interface](../../../servers_img/common/abmc_open_bmc_shell_en.png)

After logging in, verify that the tool is available:

```bash
fflog --version
# fflog v1.0
```

> If you forget the options, just run `fflog` or `fflog <log type> --help` to see the help.

## Scenario 1: A Sub-board Is Abnormal or Unreachable [step]

**Symptom**: a particular sub-board (for example `sub05`) cannot be reached, reports an abnormal state, or its features are unavailable.

**What to do**: collect only that sub-board's logs, and look at warnings and errors first.

```bash
fflog general --core sub05 --min-level warn
```

If you are not sure which sub-boards exist, list them first:

```bash
# List every sub-board name seen in the general logs, with counts
fflog general --all --fields core_name | sort | uniq -c
```

**How to read it**:

- Has output: locate the module by time and content. For example, `error network sub05 ...` means the network module failed on this sub-board;
- Empty: the sub-board itself recorded no error, so the cause is likely external (network, power). Continue with [Troubleshooting](op_issues_troubleshooting.md).

> Only the **general log** and the **system log** can be filtered by sub-board (`--core`); the manager log has no sub-board dimension and cannot be filtered this way.

## Scenario 2: A Web Operation or API Call Fails [step]

**Symptom**: an operation in the aBMC web interface does not take effect, or a Redfish API returns an error.

**What to do**: the manager log records "who, when, what was requested, the result, and the latency", which fits this kind of problem well.

```bash
# Recent operations
fflog manager

# Only requests that did not succeed (status code is not 200)
fflog manager --all --where "status_code != 200"

# Only requests from a particular user
fflog manager --all --user admin
```

**How to read it**: a manager log line contains, in order, time, level, operator, source IP, request method and address, status code, latency, and description. For example:

```text
2026-09-23 10:24:07.694 info RunLog admin 172.16.11.30 GET /redfish/v1/Systems/bmc/Oem/Firefly/DateTime 200 19 Successfully obtained date and time
2026-09-22 13:58:48.735 info RunLog admin 172.16.11.30 POST /redfish/v1/Systems/sub05/Actions/ComputerSystem.Reset 200 101 Successfully reset the server power supply
```

The first is a successful time query (status code `200`, 19 ms); the second is a reset operation on `sub05`. A `2xx` status code means success, while `4xx`/`5xx` usually means the request had a problem or the service failed; combined with `path` you can tell which operation went wrong.

## Scenario 3: Hardware Alarms (Temperature, Voltage, Fan) [step]

**Symptom**: the device raises an alarm, fan speed is abnormal, panel LEDs are abnormal, or you receive temperature or voltage alerts.

**What to do**: the system log records hardware events and sensor information, so look at it first.

```bash
# Recent system logs
fflog system

# All of today's system logs
fflog system --today --all

# Only warnings and above
fflog system --all --min-level warn
```

**How to read it**: for example

```text
2026-09-21 21:20:26.073 warn Log1 bmc SEL CPU State 33 "CPU temperature 70°C alarm":"bmc 节点cpu温度过 70 摄氏度"
```

This means the BMC detected that the `CPU State` sensor (number 33) exceeded 70°C and raised a SEL alarm; you can then check cooling, fans, and ambient temperature. The fields are, in order: time, level, event ID, sub-board, event type, sensor type, sensor number, and description.

## Scenario 4: The Device Fails to Start, or a Service Will Not Come Up [step]

**Symptom**: after a restart, some feature is unavailable, or a service did not start properly.

**What to do**: the general log records the startup process and errors of each module.

```bash
# Latest general logs
fflog general

# Only errors and warnings
fflog general --level error,warn

# Only one module
fflog general --logger service-manager
```

**How to read it**: for example

```text
2026-09-23 09:11:44.674 error kea - dhcp4/core.go:81 /etc/kea/kea-dhcp4.conf is inexistence,program exit
2026-09-23 09:28:22.858 error network sub05 ffnetwork/lifecycle.go:58 unknown OS
```

The first means the DHCP service exited because a configuration file is missing; the second means the network module could not recognize the system on `sub05`. The `msg` is the reason, and the `logger` (module) and `caller` (code location) at the front help you locate it quickly.

## Scenario 5: The Problem Is Reproducing Right Now [step]

**Symptom**: the problem appears from time to time, or is reproducing right now, and you want to capture the logs at the moment it happens.

**What to do**: use `--follow` to keep watching newly generated logs (it shows only new logs after the command starts and does not replay history).

```bash
# Keep showing new error logs
fflog general -f --level error

# Keep watching one sub-board's new logs
fflog general --follow --core sub05

# Keep outputting JSONL for easy saving
fflog manager --follow --format jsonl
```

Press `Ctrl+C` to stop.

## Scenario 6: Send the Logs to Firefly Technical Support [step]

**Symptom**: you cannot figure it out yourself and need to send the logs to Firefly technical support for analysis.

**What to do**: export the logs to files first, then send them. Use `--format jsonl` (one record per line, which is easy for us to analyze).

```bash
# Export the three log types
fflog general --all --format jsonl > /tmp/general.jsonl
fflog manager --all --format jsonl > /tmp/manager.jsonl
fflog system --all --format jsonl > /tmp/system.jsonl
```

Then copy the files back to your computer (replace `<aBMC management IP>` with the real address):

```bash
scp bmc@<aBMC management IP>:/tmp/general.jsonl .
```

You can also retrieve the files through aBMC [File Sharing](/en/docs/server/bmc-software/aBMC/fileshare).

<Callout title="Collection Tips" type="info">
  When reporting a problem, also tell us roughly when the fault occurred, the device model, and the firmware version. Make the log time range cover as much of the period before and after the fault as possible, so it can be located more easily.
</Callout>

## Appendix: fflog Command Reference [step]

The scenarios above are "how to use it by situation"; this appendix is the "complete manual". For daily use you really only need three commands (`fflog general` / `fflog manager` / `fflog system`) and a few options.

### The three log types

| Log type | In plain words | Command | Alias |
| --- | --- | --- | --- |
| General log | What the device was doing: module startup, running state, internal errors | `fflog general` | `fflog g` |
| Manager log | Who operated the device through the web or an interface: user, request path, result, latency | `fflog manager` | `fflog m` |
| System log | The "health" of the hardware: temperature, voltage, fan, and so on | `fflog system` | `fflog s` |

Log levels, from low to high: `debug` < `info` < `warn` < `error`. With no options, the command shows the latest 10 logs, ordered from oldest to newest.

### Common options

| Option | Description |
| --- | --- |
| `-n, --limit N` | Select the latest `N` records (default 10; `N` must be a positive integer). |
| `--all` | Output all matching records. Mutually exclusive with `-n/--limit`. |
| `-r, --reverse` | Reverse the display order (newest first). |
| `--since TIME` | Query from the specified time, inclusive. |
| `--until TIME` | Query up to the specified time, exclusive. |
| `--today` | Query from local midnight to the current time. Mutually exclusive with `--since` and `--until`. |
| `-l, --level LEVELS` | Match exact levels; separate multiple values with commas, for example `warn,error`. |
| `--min-level LEVEL` | Match logs at or above the given severity, for example `--min-level warn`. |
| `-w, --where EXPR` | Filter by field; repeatable, and multiple conditions are combined with AND. |
| `-f, --follow` | Continuously output only logs written after the command starts. |
| `--interval DURATION` | Follow polling interval (default `500ms`, range `100ms` to `1m`). |
| `--format FORMAT` | Output format: `compact` (default) or `jsonl`. |
| `--fields FIELDS` | Output only the specified fields, in the given order. |
| `--exclude-fields FIELDS` | Remove the specified fields from the default set. |
| `--delimiter TEXT` | Set the compact field delimiter; `\t` means a tab. |
| `--header` | Output the field names once before the first compact record. |
| `--no-color` | Disable coloring of the level field. |

`--all` is mutually exclusive with `-n/--limit`, `--level` with `--min-level`, and `--fields` with `--exclude-fields`. `--follow` cannot be combined with `-n`, `--all`, `--reverse`, `--since`, `--until`, or `--today`.

### Dedicated filters

| Log type | Dedicated options (separate multiple values with commas) |
| --- | --- |
| general | `--core` (sub-board name), `--logger` (module) |
| manager | `--category`, `--user`, `--status` (status code) |
| system | `--core` (sub-board name) |

### Time filtering

`--since` accepts two forms:

- **Relative time**: a positive integer with a lowercase unit `s`, `m`, `h`, or `d`, for example `30s`, `15m`, `2h`, `1d` (`d` always means 24 hours);
- **Local absolute time**: `YYYY-MM-DD` or `YYYY-MM-DD HH:MM`.

`--until` accepts only local absolute time. The range is half-open: `--since` includes the start, `--until` excludes the end. All times are interpreted and displayed in the device's local time zone.

```bash
fflog general --since 30m
fflog manager --since "2026-09-23 08:00" --until "2026-09-23 12:00" --all
```

### Field filtering (--where)

The form is `field operator value`, with optional spaces around the field and operator. String fields support `=`, `!=`, `~` (contains), and `!~` (does not contain); number fields support `=`, `!=`, `>`, `>=`, `<`, and `<=`. Multiple `--where` conditions are always combined with AND.

| Log type | String fields | Number fields |
| --- | --- | --- |
| general | `logger`, `core_name`, `caller` | none |
| manager | `category`, `username`, `client`, `method`, `path` | `status_code`, `latency_ms` |
| system | `log_id`, `core_name`, `entry_type`, `sensor_type` | `sensor_number` |

```bash
fflog manager --all --where "status_code >= 400"
fflog manager --where "path~/redfish"
fflog manager --where "latency_ms>=30"
```

> If a field value itself contains a comma, use `--where` instead of a list option, for example `--where "logger=machine,network"`.

### Output formats and fields

- `compact` (default): one log per line, fields separated by spaces, good for reading directly; an empty value is shown as `-`.
- `jsonl`: one JSON object per line, good for export and program analysis. Use it when sending logs to technical support.

```text
{"time":"2026-09-23T09:28:22.771+08:00","level":"info","logger":"service-manager","core_name":"sub05","caller":"transaction/job.go:172","msg":"started vnc"}
```

Default fields of each log type:

| Log type | Each line contains, in order |
| --- | --- |
| general | time, level, logger, core_name, caller, message |
| manager | time, level, category, username, client, method, path, status_code, latency_ms, message |
| system | time, level, log_id, core_name, entry_type, sensor_type, sensor_number, message |

To keep only some fields or drop some, use `--fields` (keep only) or `--exclude-fields` (drop), for example:

```bash
fflog general --fields time,level,msg
```

### FAQ

**Q: The result is empty?**
By default only the latest 10 are shown, and the filters also limit the result. Remove the filters, add `--all`, or widen the `--since` time range and try again.

**Q: It reports that the database or a table does not exist?**
Confirm that the aBMC service on the device is running properly and that the current firmware version supports the log type you are querying.

**Q: The displayed time is different from what I expect?**
All times are shown in the device's local time zone. If the device time is wrong, calibrate the system time in the aBMC web interface first, then query again.

**Q: There are too many options to remember?**
Running `fflog` on its own shows the help below, or run `fflog general --help` for the options of one log type. (The tool's built-in help text is currently in Chinese.)

```text
查询 Firefly BMC 日志

用法:
  fflog [命令] [选项]

可用命令:
  general     查询 general 日志
  help        显示命令帮助
  manager     查询 manager 日志
  system      查询 system 日志

选项:
  -h, --help      显示帮助
  -v, --version   显示版本
```