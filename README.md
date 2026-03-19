# ABCI Tools

Command-line tools for [ABCI](https://abci.ai/en/) (AI Bridging Cloud Infrastructure) v3.

## Tools

### qsub_shell - Interactive Shell Launcher

Simplifies launching interactive sessions on ABCI with sensible defaults.

```bash
qsub_shell                           # Default: 1 GPU, 30 min
qsub_shell -g 2 -t 60               # 2 GPUs, 60 minutes
qsub_shell --no-gpu                  # CPU-only mode
qsub_shell --resv R1538111           # Use reserved node
qsub_shell --resv R1538111 -q rt_AG  # Reserved node with specific resource type
```

#### Options

| Option | Long Option | Description | Default |
|--------|-------------|-------------|---------|
| `-g` | `--gres gpu:N` | Number of GPUs (use 0 for CPU-only) | 1 |
| | `--no-gpu` | CPU-only mode | |
| `-t` | `--time MINUTES` | Time limit in minutes | 30 |
| `-p` | `--project NAME` | Project group name | gch51606 |
| `-q` | `--queue NAME` | Queue / resource type | rt_HG |
| `-r` | `--resv ID` | Reservation ID (e.g., R1538111) | |
| `-n` | `--name NAME` | Job name | interactive |
| `-h` | `--help` | Show help | |

When `--resv` is specified, the reservation ID is used as the queue (`-q`) and the resource type is passed via `-v RTYPE=`.

---

### abci-monitor - Reservation Node Monitor

CLI-based visualization of reserved node and GPU usage. Shows real-time allocation status using `pbsnodes` for accurate per-node information, including other users' jobs.

```bash
abci-monitor                  # Show all reservations
abci-monitor -r R1601791      # Specific reservation only
abci-monitor -w 10            # Auto-refresh every 10 seconds
abci-monitor -c               # Compact mode (no job details)
abci-monitor --no-color       # Disable colors
```

#### Options

| Option | Long Option | Description |
|--------|-------------|-------------|
| `-r` | `--resv ID` | Show only specific reservation(s), comma-separated |
| `-w` | `--watch SEC` | Refresh every N seconds |
| `-c` | `--compact` | Compact mode (no per-job details) |
| | `--no-color` | Disable ANSI colors |

#### Example Output

```
 ABCI Reservation Monitor                              2026-03-19 13:46:40
 ══════════════════════════════════════════════════════════════════════════

 R1575592  RUNNING  Mar 02 10:00 → Mar 30 09:30  4 nodes  2R/0Q/0H
 ──────────────────────────────────────────────────────────────────────────
  hnode083  [████████] 8/8 GPUs
            └─ ace14352bs  RUN-UV-QSUB-OPE*  8 GPU  716:18:*
  hnode084  [████████] 8/8 GPUs
            └─ ace14352bs  RUN-UV-QSUB-OPE*  8 GPU  716:18:*
  hnode085  [░░░░░░░░] 0/8 GPUs
  hnode092  [░░░░░░░░] 0/8 GPUs

  Summary: 16/32 GPUs used (50%) │ 2/4 nodes active

 Overall: 42/192 GPUs (22%) │ 6/24 nodes active │ 3 reservations
```

#### Data Sources

| Command | Purpose |
|---------|---------|
| `qrstat` | List reservations |
| `qrstat -f <ID>` | Reservation details (nodes, period, state) |
| `qstat -Q <QUEUE>` | Job counts per reservation queue |
| `qgstat` | Group job list (user, name, time for all group members) |
| `qstat -f <JOB_ID>` | Own job details (RTYPE) |
| `pbsnodes <NODES>` | Per-node job/slot allocation (accurate GPU mapping) |

## Installation

```bash
git clone git@github.com:tatsukamijo/abci-tools.git
cd abci-tools
chmod +x qsub_shell abci-monitor

# Add to PATH (e.g., in ~/.bashrc)
export PATH="$PATH:$(pwd)"
```

Edit the default project group name in `qsub_shell` (`PROJECT_GROUP`) to match your own.

## Resource Types

| Resource Type | GPUs | CPUs | Memory | Use Case |
|---------------|------|------|--------|----------|
| `rt_HC` | 0 | 32 | 320GB | CPU-only tasks |
| `rt_HG` | 1 | 16 | 160GB | Single GPU workloads |
| `rt_HF` | 8 | 192 | 1920GB | Multi-GPU / full node jobs |

## Requirements

- ABCI v3 account with PBS job scheduler access
- Bash (qsub_shell)
- Python 3 (abci-monitor, standard library only)

## License

MIT
