---
name: fullcapacity
description: Nail's full-capacity check — every running GPU box works at 80–90 % while there is work, every dispatched lane is really on the box, nothing waits. Use whenever Nail types /fullcapacity, says tam kapasite, %80 %90, GPU boşta, hızlı aksın, kontrolde olsun, and on every lead tick while lanes render; pairs with /mukemmel and /clouding.
---

# /fullcapacity — run the boxes at 80–90 %, everything under control

Nail, 10-21: «80% 90% da takılsın mümkünse hep, hani tasarrufun içinden geç … detaylı kontrolünü yapıp aksın, seri şekilde yapması gerekeni tam söyleyen … hızlıca aksın, her şey kontrolde olsun». The three skills that always run together: /mukemmel (quality), /clouding (where and how work runs), /fullcapacity (how hard it runs).

## The target
- While work exists, every running GPU box averages **80–90 % GPU** (or its CPU cores are ≥ 80 % busy on CPU-bound steps), measured on the box, not guessed.
- Saving money is not the goal; finishing fast is. The account cap ($3/h; Nail's own short raises count) is a ceiling: inside it, use what is paid for to the full. No work at all → the box stops (/clouding rule 10).

## The check (run it in this order, it takes ~2 minutes)
1. **Pods and money:** list the running pods over REST (`~/.config/tigno/runpod-new.key`, read in code only, User-Agent header): name, $/h, total vs cap.
2. **What should be running:** every lane I dispatched or resumed that has render work (from my plan file and the agent list).
3. **What is on the box:** `fp.py ssh <any-lane> "ls /root/fp/lanes/; nvidia-smi --query-gpu=utilization.gpu,utilization.encoder,memory.used --format=csv,noheader; cat /sys/fs/cgroup/cpu.max /sys/fs/cgroup/cpu.pressure"` (fp.py: `~/.claude/plans/faceless-v1-backup-1020/gpu-fastpath/wt/deploy/fastpath`). `/proc/loadavg` in the container is the HOST's — never read it as our load; use the cgroup's cpu.pressure and the sampler.
4. **The last 5 minutes:** `fp.py util <lane> --since <epoch now−300>`: GPU avg/peak, NVENC, CPU cores used of the cgroup's cores, jobs running/queued.
5. **Compare:** lanes that should be running but have no dir or no job; GPU average vs 80–90 %; queued jobs while slots are free; cores idle while jobs queue.
6. **Disk, every check** (Nail 10-22 «disk olarak asla yanlış yeri seçmesin»): on each box, `df -h / /workspace /dev/shm; du -sh /root/lanes/* | sort -h | tail; touch /workspace/.qprobe && rm /workspace/.qprobe`. The volume's `df` shows the whole cluster, not our quota; read the quota from `du -sh /workspace/*`, refreshed in the background every 30 min, against the volume size from REST (tigno-vol = 150 GB). The write probe catches «Disk quota exceeded» at once.
7. **Lanes vs owners:** the agent list against the box dirs. Two lanes on one job, two agents in one dir, or a lane still working after it said «stopped» → close one the same turn. A lane's «stopping» is not proof. Before its job or dir goes to a new lane, ListAgents must show it idle. If it isn't idle, send it STOP first and wait for idle (10-22: malzeme-3, yerlestirme-1022 and gzt-motion-2 each kept working after saying «stop», so two lanes ended up in one dir). Before a fold, `git merge-base --is-ancestor <live sha> <branch>` must pass for every branch, or the branch is rebased first: a branch without live rolls live back.

## Where data goes (never the wrong place)
| What | Where | Never |
|---|---|---|
| A lane's code, venv, node_modules, runtime under test | the box's own disk, `/root/lanes/<lane>` | `/dev/shm` (noexec: native .so fail «failed to map segment»); `/workspace` (FUSE, slow) |
| Shared, durable data: bare repo, fixtures, tools, live app | network volume `/workspace` | GBs of lane leftovers or archives (150 GB quota, live writes there too: hit 10-22) |
| A finished lane's dir, an archive | B2 (`rclone copy` + `check`, then delete), see b2_sync.sh | the volume or the box's own disk |
| Inert scratch that fits in RAM (frames, temp media) | `/dev/shm` | anything that must be executed or survive a restart |

Thresholds:
- **Box `/`:** at least 8 G free. Under 8 G, archive a stopped lane to B2 the same turn. Under 4 G, every lane stops writing new outputs until there is room again.
- **Volume:** at least 15 G of headroom under the quota. Nothing over 1 G moves onto it without a fresh headroom check.
- **Every lane brief carries this table.** A lane checks `df` before any write over 1 G, and refuses loudly if less than 4 G would be left.

## What to do (in this order)
1. **A dispatched lane is not on the box** → one message: attach and start now, poll to the end itself. Still not there next check → I take the job over.
2. **Jobs queued, slots free** → raise the box's parallel slots to measured headroom (cores per job, VRAM, disk).
3. **GPU low, CPU cores idle, jobs sitting in serial steps** (QC probes, heals, contact sheets, Python per-frame work) → that step is parallelised across cores or moved to the GPU (NVENC, GPU decode, GL) — as an engine fix, proven with a same-reel timing and SSIM, deployed only between renders.
4. **GPU low, CPU saturated** → the CPU work moves to the GPU, or the box is replaced (never added) by a flavour with more vCPU per GPU, inside the cap, armed and announced, at a moment that kills no render.
5. **GPU at 80–90 %, queue empty** → good; nothing to change.
6. **No work** → stop the box after outputs are safe.

## It never stops while anything renders (Nail 10-21 23:3x «sürekli kontrol et … tam verim her yere dağılmış kullanılıyor mu … hep sorunsuz akıtsın hep hep hep»)
- While any lane has work on a box, the lead re-runs this check every ~5 minutes. Every check ends by arming the next one, as a background timer or a wakeup, until the box has no work left. A check with no next one armed is a miss.
- **Each check looks for every way capacity is wasted, not just «GPU low»:**
  - jobs queued while cores or slots sit idle: the slot sizing is wrong; fix the sizing;
  - a queue wait over ~5 minutes;
  - two lanes doing the same job: close the extra one at once;
  - a lane holding the box with no job;
  - an experiment taking slots from a delivery: the delivery goes first;
  - a heavy step running outside the gate;
  - a CPU encode where NVENC is proven;
  - a disk under its threshold, a failed quota write probe, or a lane writing to the wrong place in the table;
  - a branch about to be folded that lacks live.
- **The box's watchdog raises the same faults on its own every 5 minutes**, written to the lane INBOX, the lead inbox and a macOS notification, so a gap in my checks is still caught. If the watchdog stops raising them, that is a fault to fix too.
- **What happens after a check:**
  - If it finds a fault, I take action that same turn.
  - Every check is also a short line in the night plan.
  - I tell Nail only what changed.

## Report (to Nail, Turkish, short)
One line per box: GPU avg/peak, CPU cores used/available, jobs running/queued, $/h, disk free on `/` and volume headroom; then what I changed and what is still open. Numbers only from the measurement of this check.

## Pass it on
Every lane brief that renders carries this target and must report its run's util numbers (/clouding rule 11). A lane that ends its turn «waiting» on a render it isn't polling breaks this skill.
