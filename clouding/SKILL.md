---
name: clouding
description: Nail's compute rule — the Mac is a controller only; heavy work runs in the cloud (claude --cloud) or on a GPU pod, efficiently, then the box is stopped. Use before any render, test suite, build, model run, media job or agent dispatch for Nail, and when he types /clouding or says cloud, pod, GPU, Mac yoruldu, verimli çalış.
---

# /clouding — heavy work off the Mac, efficiently

Nail, 10-21: «cloudda falan verimli çalış, cloudlarda, varsa podda» · «gztler de render vs full podda ak akıllıca gpulu … tasarruf çalış, burası çok yoruldu, cloud öncelik» · «kanka çok yavaş geliyorsun … çöz».

## Where work runs
- **Mac = controller only.** It edits code, makes git commits, runs ONE quick spec for a changed file, fetches results and coordinates. No headless Chrome, renders, ffmpeg encodes, Whisper/UTMOS, Blender, docker builds or full test suites on the Mac.
- **Code, tests, research, copy → the cloud** (`claude --cloud`, or a cloud agent).
- **Renders, media, models, GPU work → a GPU pod.** Use the RunPod NEW account; the key is in ~/.config/tigno/runpod-new.key. Read it in code only and never print it. REST needs a User-Agent header.

## How a pod is used
1. **Acquire fast.** Go up the ladder without waiting more than ~2 minutes on stock: community 4090 ≤ $0.40 → 3090/A5000 ≤ $0.50 → secure 3090 ≤ $0.50 → secure 4090 ≤ $0.79 → L40S ≤ $0.99.
2. **Prove the fast path before the real job.** Check for an NVIDIA renderer, a readback, utilisation > 0 and a frame-diff against a reference. A silent CPU fallback is refused loudly. Prove CUDA too (cuInit / `torch.cuda.is_available()` plus a tiny matmul): on 10-21, one 4090 host rendered GL fine but had dead CUDA (cuInit 999), so Whisper fell to the CPU. A host that fails CUDA is rejected at acquire.
3. **Keep the GPU busy.** Batch everything into one pod session: dry-run, render, encode and QC together. Use hardware encode where it is proven equal in quality.
4. **Stop or terminate the box the moment outputs are fetched.** Confirm over REST. A stopped pod stays pinned to its host, so terminate it. Never leave an idle GPU.
5. **Data:** finals go to object storage (B2) or the network volume. Pods and the Mac stay empty. (Nail 10-22 «b2ye hep geçir boş olsun hep kutu … bozma bozulma»)
   - A work box is created with a 200 GB container disk (`lead-scripts/acquire-workbox.py`). Resizing it later RESETS the box and wipes `/root`.
   - `box-setup.sh` starts the disk guard (`/workspace/lanes/lead-tools/diskguard.sh` + `arch1.sh`). Every 5 minutes it checks `/`. Under 8 G it streams every dir listed in `/root/lanes/lead/archivable.txt` to B2 (tar+zstd, file count verified), then removes it. Under 4 G it sets `/root/lanes/STOP-WRITES`.
   - **A lane that stops appends its own dir to `archivable.txt`** in its RESUME step, after moving any token out. The lead adds stale dirs too, after checking that no process uses them. Where each kind of data belongs: /fullcapacity «Where data goes».
6. **One shared work box, one directory per lane.** On the shared box each lane works only in its own directory and port, kills only its own pids, and never writes into another lane's directory. Don't touch pods you didn't create.
7. **The budget is a ceiling, not a target; every pod runs at full power** (Nail 10-21 «3 dolarlık aldığın tüm yapıları paralel dağıtıp tam güçle … 3 dolar gerekmiyosa 3 dolar da kalmasın … varolanları tam hızda tuttuğuna emin ol»). The account cap is $3/h total, all lanes and sessions together. Before opening a pod, sum the live $/h over REST. A pod lives only while it renders: code, fal and waiting happen with no pod. On the pod, independent jobs (reels, encodes, QC) run in parallel until the GPU is busy. The lead checks each pod's own sampler (`/root/fp/util.csv`); a pod near 0 % for 5 minutes is put to work or terminated. Spare budget stays unspent.

8. **Reuse before acquire. No surprise pods.** (Nail 10-21: «yeni pod açtırmak zorunda bırakmasın … BÖYLE ŞEYLER ASLA OLMASIN … kontrolü hep yapsın»)
   - All lanes share the ONE running work box and queue on it. A lane never asks for its own pod.
   - A new pod only REPLACES the work box, never adds one, and only when the box's own measurement (queue wait, util, disk) proves it can't carry the work.
   - No code, cron, Worker or reviver creates or starts a pod unless all three hold: it is explicitly armed by the lead, it has a cap check over the account's live $/h, and it notifies the lead on every create or start. A spec guards each of the three.
   - A trial or drill pod runs only inside the cap, for a set window, and is terminated after.

9. **Check first, so nothing «turns out» later.** Before any plan, dispatch or report, read what already exists, and state it in the plan's first lines:
   - the running pods and the account's $/h;
   - outputs and exports already on the volume or B2 (reuse them instead of re-rendering);
   - the caches;
   - the logins (`whoami`, `gh auth status`) before asking Nail to log in;
   - the stock;
   - which lane already owns the thing.

   A fact found mid-way that a 1-minute check would have shown («yokmuş, varmış, etmiş») counts as a miss. Fix the check list, not just the case.

10. **A GPU is never left at 0 %** (Nail 10-21 «hiçbir zaman gpu %0'da durmasın, 5 dakikada bir kontrol etsin … her tarafa düzgün dönütler versin, rule'a yaz kesin, genel olarak»).
   - A watchdog checks every running pod every 5 minutes, using the pod's own sampler.
   - At 0 % with no fresh job, the pod is stopped.
   - At 0 % while a lane holds it, the lane and the lead both get a plain message: what is idle, for how long, at what $/h, and the next step (put work on it, or release it).
   - A cheap model call such as Haiku may write that message from the logs; the decision itself stays a measured rule.
   - Every stop, keep and warning is logged and shown to the lead.
   - This applies to every project and box, not only faceless.
   - **The live pod is the exception.** The pod named in /workspace/home/boot/live_pod_id serves app.tigno.si and is never stopped by the watchdog. It sleeps only through the reviver's sleep-when-idle rule, once wake-on-request is proven. A CPU-bound job, such as a reel running Malzeme, is a fresh job even at low GPU %.

11. **Every run proves it ran at full power** (Nail 10-21 «sorulatsın, emin oldursun, tam verimde gpu cpu vs aktığını»).
   - Every lane report carries the measured utilisation of its run from the box's own sampler (`fp.py util <lane> --since <epoch>`): average and peak GPU %, NVENC %, CPU cores used out of the cores available, jobs in parallel, and wall time per reel.
   - The lead asks for these numbers on every report and does not accept a report without them.
   - A GPU averaging under ~50 % while the CPU is saturated is a fast-path fault, not «normal». Find what eats the CPU (raster, encode, filters, Python), move it to the GPU or to more cores, and prove it with a same-reel timing before and after.
   - Measured 10-21 on fp-faceless (A5000, 7.65 cores): GPU average ~7 % (peak 89 %), CPU 4.9 cores, so the box was CPU-bound. This is the open fault to close.

12. **Everything heavy starts on the server at once, and the lead checks it there** (Nail 10-21 «HER ŞEYİ SUNUCUDA BAŞLAT … BUNDAN SONRA HEP BÖYLE YAP»).
   - A lane's first act after its cheap proof is to attach to the shared box and start its job in the background; it polls the job to the end itself and never ends its turn «waiting».
   - Within ~5 minutes of every dispatch or resume, the lead checks the box itself: the lane's directory exists, a job is running or queued, and util moves. A lane's word («rendering», «waiting») is not proof.
   - A lane that isn't on the box gets one message and is started; if it still isn't there, the lead takes the job over.

## Efficiency
- Run each test once, only the specs that cover the change. Don't repeat a run that proves nothing new.
- Cache calls and assets, reuse cached takes, and send one caller at a time to paid APIs, inside the spend guard.
- Before saying «slow», measure where the wall-clock goes (acquire, boot, setup, render, fetch) and cut the biggest step at its root.

## Pass it on
These rules bind every agent and session working for Nail, not only this one. When this skill changes, or when you brief or dispatch an agent, tell every running agent and peer session to follow it too («bu iyileştirme ve kurallara dikkat etsinler»): put the skill path in each brief, and send the update by message to any lane already running.
