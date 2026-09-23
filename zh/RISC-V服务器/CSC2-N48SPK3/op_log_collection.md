# 日志收集

设备在运行过程中，会像写日记一样把发生的事情记录下来，这就是**日志**。排查问题时，只要抓对日志，往往能很快找到原因。

本文**先按「出了什么问题」讲几个最常见的场景**，告诉你每种情况该抓哪类日志、怎么抓、结果怎么看；**最后**再附上 `fflog` 的完整命令速查，供需要时查阅。跟随场景操作不需要懂编程，命令都可以直接复制，示例输出来自一台真实设备，实际内容以你的设备为准。

## 先登录 BMC [step]

所有场景都从登录 BMC 命令行开始（日志保存在 BMC 里）。两种方式任选其一：

- **SSH**：`ssh bmc@<aBMC 管理 IP>`，账号密码见[访问 BMC](start_login_bmc.md)；
- **BMC Shell**：在 aBMC 网页里用 **Remote console → BMC Shell** 打开命令行。

![在 aBMC Web 打开 BMC Shell](../../../servers_img/common/abmc_open_bmc_shell_en.png)

登录后确认工具可用：

```bash
fflog --version
# fflog v1.0
```

> 忘了参数不要紧，直接运行 `fflog` 或 `fflog <日志类型> --help` 就能看到帮助。

## 场景一：某块子板异常或失联 [step]

**现象**：某块子板（例如 `sub05`）连不上、状态异常，或这块子板上的功能不可用。

**这样做**：只抓这块子板的日志，并优先看警告和错误。

```bash
fflog general --core sub05 --min-level warn
```

不确定有哪些子板时，可以先列出来：

```bash
# 列出通用日志里出现过的所有子板名及条数
fflog general --all --fields core_name | sort | uniq -c
```

**怎么看**：

- 有输出：按时间和内容定位模块，例如出现 `error network sub05 ...`，说明是网络模块在这块子板上出错；
- 没有输出：说明这块子板本身没记录到错误，问题可能在网络、供电等外部因素，可结合[异常排查](op_issues_troubleshooting.md)继续排查。

> 只有**通用日志**和**系统日志**能按子板抓（`--core`）；管理日志没有子板维度，不能这样筛。

## 场景二：网页操作或接口调用失败 [step]

**现象**：在 aBMC 网页上做的操作没有生效，或某个 Redfish 接口返回了错误。

**这样做**：管理日志记录了「谁、什么时候、请求了什么、结果如何、耗时多少」，非常适合查这类问题。

```bash
# 最近的操作记录
fflog manager

# 只看没有成功（状态码不是 200）的请求
fflog manager --all --where "status_code != 200"

# 只看某个用户的请求
fflog manager --all --user admin
```

**怎么看**：一行管理日志依次是时间、级别、操作人、来源 IP、请求方式与地址、状态码、耗时、说明。例如：

```text
2026-09-23 10:24:07.694 info RunLog admin 172.16.11.30 GET /redfish/v1/Systems/bmc/Oem/Firefly/DateTime 200 19 Successfully obtained date and time
2026-09-22 13:58:48.735 info RunLog admin 172.16.11.30 POST /redfish/v1/Systems/sub05/Actions/ComputerSystem.Reset 200 101 Successfully reset the server power supply
```

第一条是一次查询时间的成功请求（状态码 `200`，耗时 19 毫秒）；第二条是对 `sub05` 的复位操作。状态码 `2xx` 表示成功，`4xx`/`5xx` 通常表示请求有问题或服务异常，结合 `path` 就能定位是哪个操作出了问题。

## 场景三：硬件报警（温度、电压、风扇）[step]

**现象**：设备报警、风扇转速异常、面板指示灯异常，或收到温度、电压类告警。

**这样做**：系统日志记录硬件事件与传感器信息，先看它。

```bash
# 最近的系统日志
fflog system

# 今天的全部系统日志
fflog system --today --all

# 只看警告及以上
fflog system --all --min-level warn
```

**怎么看**：例如

```text
2026-09-21 21:20:26.073 warn Log1 bmc SEL CPU State 33 "CPU temperature 70°C alarm":"bmc 节点cpu温度过 70 摄氏度"
```

这条说明 BMC 检测到 `CPU State` 传感器（编号 33）超过 70℃，并产生了一条 SEL 告警；可据此检查散热、风扇和环境温度。字段依次为：时间、级别、事件 ID、子板、事件类型、传感器类型、传感器编号、说明。

## 场景四：设备启动异常或某个服务起不来 [step]

**现象**：设备重启后某个功能不可用，或某个服务没有正常启动。

**这样做**：通用日志记录了各个模块的启动过程和报错。

```bash
# 最新的通用日志
fflog general

# 只看错误和警告
fflog general --level error,warn

# 只看某个模块
fflog general --logger service-manager
```

**怎么看**：例如

```text
2026-09-23 09:11:44.674 error kea - dhcp4/core.go:81 /etc/kea/kea-dhcp4.conf is inexistence,program exit
2026-09-23 09:28:22.858 error network sub05 ffnetwork/lifecycle.go:58 unknown OS
```

第一条说明 DHCP 服务因为缺少配置文件而退出；第二条说明网络模块在 `sub05` 上无法识别系统。`msg` 是原因，前面的 `logger`（模块）和 `caller`（代码位置）能帮你快速定位。

## 场景五：问题正在复现，要抓现场 [step]

**现象**：问题时不时出现，或此刻正好在复现，想抓到出问题那一刻的日志。

**这样做**：用 `--follow` 持续跟踪新产生的日志（只显示命令启动后的新日志，不回放历史）。

```bash
# 持续输出新的错误日志
fflog general -f --level error

# 持续跟踪某块子板的新日志
fflog general --follow --core sub05

# 以 JSONL 格式持续输出，方便保存
fflog manager --follow --format jsonl
```

按 `Ctrl+C` 结束。

## 场景六：把日志提供给 Firefly 技术支持 [step]

**现象**：自己看不太明白，需要把日志发给 Firefly 技术支持分析。

**这样做**：先导出成文件再发送。建议用 `--format jsonl`（一行一条，方便我们分析）。

```bash
# 分别导出三类日志
fflog general --all --format jsonl > /tmp/general.jsonl
fflog manager --all --format jsonl > /tmp/manager.jsonl
fflog system --all --format jsonl > /tmp/system.jsonl
```

再用电脑把文件拷回来（把 `<aBMC 管理 IP>` 换成实际地址）：

```bash
scp bmc@<aBMC 管理 IP>:/tmp/general.jsonl .
```

也可以直接通过 aBMC 的[文件共享](/docs/server/bmc-software/aBMC/fileshare)把文件取回电脑。

<Callout title="收集建议" type="info">
  反馈问题时，请一并告诉我们：故障大概发生在什么时间、设备型号和固件版本。日志的时间范围尽量覆盖故障前后，越完整越好定位。
</Callout>

## 附录：fflog 命令速查 [step]

上面是「按场景用」，下面是「完整手册」。日常其实只需记住三条命令（`fflog general` / `fflog manager` / `fflog system`）和几个常用参数。

### 三类日志

| 日志类型 | 简单理解 | 命令 | 别名 |
| --- | --- | --- | --- |
| 通用日志 | 设备平时都干了什么：程序启动、模块运行、内部报错 | `fflog general` | `fflog g` |
| 管理日志 | 谁通过网页或接口操作过设备：操作人、请求地址、结果、耗时 | `fflog manager` | `fflog m` |
| 系统日志 | 硬件的「身体状况」：温度、电压、风扇等 | `fflog system` | `fflog s` |

日志级别由低到高：`debug`（调试） < `info`（正常） < `warn`（警告） < `error`（错误）。不带参数时默认显示最新 10 条，并按时间从早到晚排列。

### 常用选项

| 选项 | 说明 |
| --- | --- |
| `-n, --limit N` | 选取最新的 `N` 条记录，默认 10，`N` 为正整数。 |
| `--all` | 输出全部匹配记录，与 `-n/--limit` 互斥。 |
| `-r, --reverse` | 反转展示顺序（由新到旧）。 |
| `--since TIME` | 从指定时间开始查询，包含边界。 |
| `--until TIME` | 查询到指定时间之前，不包含边界。 |
| `--today` | 查询当天零点至当前时刻，与 `--since`、`--until` 互斥。 |
| `-l, --level LEVELS` | 精确匹配级别，多个用逗号分隔，如 `warn,error`。 |
| `--min-level LEVEL` | 匹配不低于该严重程度的日志，如 `--min-level warn`。 |
| `-w, --where EXPR` | 按字段筛选，可重复，多个条件按 AND 组合。 |
| `-f, --follow` | 只持续输出命令启动后新入库的日志。 |
| `--interval DURATION` | follow 的轮询间隔，默认 `500ms`，范围 `100ms` 至 `1m`。 |
| `--format FORMAT` | 输出格式，支持 `compact`（默认）和 `jsonl`。 |
| `--fields FIELDS` | 只输出指定字段，并保持给定顺序。 |
| `--exclude-fields FIELDS` | 从默认字段中排除指定字段。 |
| `--delimiter TEXT` | 设置 compact 的字段分隔符，`\t` 表示制表符。 |
| `--header` | 在首条 compact 记录前输出一次字段名。 |
| `--no-color` | 关闭 level 字段的颜色显示。 |

`--all` 与 `-n/--limit` 互斥，`--level` 与 `--min-level` 互斥，`--fields` 与 `--exclude-fields` 互斥；`--follow` 不能与 `-n`、`--all`、`--reverse`、`--since`、`--until`、`--today` 同时使用。

### 专用筛选项

| 日志类型 | 专用选项（多个用逗号分隔） |
| --- | --- |
| general | `--core`（子板名）、`--logger`（模块） |
| manager | `--category`、`--user`、`--status`（状态码） |
| system | `--core`（子板名） |

### 时间筛选

`--since` 支持两类写法：

- **相对时间**：正整数加小写单位 `s`、`m`、`h`、`d`，例如 `30s`、`15m`、`2h`、`1d`（`d` 固定为 24 小时）；
- **本地绝对时间**：`YYYY-MM-DD` 或 `YYYY-MM-DD HH:MM`。

`--until` 只接受本地绝对时间。时间范围为半开区间：`--since` 包含起点，`--until` 不包含终点。所有时间均按设备本地时区解释与显示。

```bash
fflog general --since 30m
fflog manager --since "2026-09-23 08:00" --until "2026-09-23 12:00" --all
```

### 按字段筛选（--where）

格式为 `字段 操作符 值`，字段与操作符之间可以有空格。字符串字段支持 `=`、`!=`、`~`（包含）、`!~`（不包含）；数字字段支持 `=`、`!=`、`>`、`>=`、`<`、`<=`。多条 `--where` 始终按 AND 组合。

| 日志类型 | 字符串字段 | 数字字段 |
| --- | --- | --- |
| general | `logger`、`core_name`、`caller` | 无 |
| manager | `category`、`username`、`client`、`method`、`path` | `status_code`、`latency_ms` |
| system | `log_id`、`core_name`、`entry_type`、`sensor_type` | `sensor_number` |

```bash
fflog manager --all --where "status_code >= 400"
fflog manager --where "path~/redfish"
fflog manager --where "latency_ms>=30"
```

> 字段值本身包含逗号时，请改用 `--where` 而非列表选项，例如 `--where "logger=machine,network"`。

### 输出格式与字段

- `compact`（默认）：每条日志一行，字段之间用空格分开，适合直接看；空值显示为 `-`。
- `jsonl`：一行一条 JSON，适合导出和程序分析。导出给技术支持时建议用它。

```text
{"time":"2026-09-23T09:28:22.771+08:00","level":"info","logger":"service-manager","core_name":"sub05","caller":"transaction/job.go:172","msg":"started vnc"}
```

三类日志的默认字段：

| 日志类型 | 一行日志里依次是 |
| --- | --- |
| general | 时间、级别、logger、core_name、caller、内容 |
| manager | 时间、级别、category、username、client、method、path、status_code、latency_ms、内容 |
| system | 时间、级别、log_id、core_name、entry_type、sensor_type、sensor_number、内容 |

只保留或去掉某几个字段时，用 `--fields`（只保留）或 `--exclude-fields`（去掉），例如：

```bash
fflog general --fields time,level,msg
```

### 常见问题

**Q：查询结果什么都没有？**
默认只显示最新 10 条，同时受筛选条件限制。可以先去掉筛选，或加上 `--all`，或把 `--since` 的时间范围放宽再试。

**Q：提示数据库或数据表不存在？**
请确认设备上的 aBMC 服务运行正常，并且当前固件版本支持要查询的日志类型。

**Q：显示的时间和我预期的不一样？**
所有时间都按设备本地时区显示。如果设备时间不准，请先在 aBMC Web 里校准系统时间，再重新查询。

**Q：参数太多，记不住怎么办？**
直接运行 `fflog` 可以看到下面这些帮助信息，或运行 `fflog general --help` 查看某类日志的参数。

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