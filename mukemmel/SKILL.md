---
name: mukemmel
description: Nail's standing perfection standard. Use on EVERY build, engine, video/motion, design or production task for Nail in any project, before showing him any output, before writing any agent brief, and whenever a fix cycle repeats. Also when he says mükemmel, kusursuz, canavar, güçlü, adam gibi, or types /mukemmel.
---

# /mukemmel — always the most perfect

Nail, 10-12 15:1x, verbatim: «köklüce güçlüce iş yapacaksa, yarayacaksa yap … mükemmeli istiyoruz, beni anla artık, bir daha asla tekrar ettirme bu sözlerimi. hep hep hep en mükemmel olsun … ultrathink brainstorming, akıllı çalış». The fear behind it (same day): «dönüşe dönüşe bağlamı kaybedip kaliteyi öldürüp yama üstüne yama yazıp boşuna GB tüketip, verdiğim referansları adam gibi çalışmayıp minik düzeltmelerle bir şeyler yapmaya çalışan ezik, yaratıcı modellerin gücünü kullanmayan hantal bir sistem».

He must never have to say this again. Read it as the default for all work, not as a mode.

## The bar
Perfect = at or above the best reference he gave (or the version he preferred), judged SIDE BY SIDE by eye. "Gates passed", "tests green", "judge 74" are not the bar.

## Laws
1. **Root, never patch.** Every critique becomes the generic ability that would have prevented it (engine / card / data), proven on every affected item at once (measure all, fix once). If the same area was fixed twice and he still rejects it: STOP patching, run an architecture review (≥ 2 outside refs, ≥ 3 approaches incl. one radical, cost + latency math) before any third fix.
2. **Use the creative power.** Taste (composition, motion, story, type) is decided by the strongest model WITH SIGHT of the references and of its own render. Code guards only the hard laws and measures. A gate that would flatten an idea sends it back with the reason; it never silently degrades it. One strong creative pass + a measured critique beats many tiny rule tweaks.
3. **Study the refs for real.** Before building a look: watch and measure the refs (frames, timing, compositions, motion curves, sound), write their grammar, and keep an "ours vs ref" sheet as the acceptance test.
4. **Ultrathink before building.** Brainstorm ≥ 3 approaches, pick the root one, say in one line why the others lose. No building on the first idea.
5. **Look before showing.** Render, look at every sheet as he would, next to the ref. Not at the bar → say so plainly and fix the root. Never show mediocre as done; never withhold silently either.
6. **Never make him repeat.** Each verdict goes verbatim into talepler and into the rule / memory / brief that governs it, the same turn. Every lane brief carries the binding verdicts verbatim + the bar file. A busy lane that cannot read a new verdict is stopped and restarted with it before it builds the rejected thing.
7. **No waste.** Lanes write RESUME before their cap; one owner per file; no fleets; finished worktrees archived after checking their storage; calls cached, capped, one caller at a time. Nail 10-21 «boşa gitmesin hiç bundan sonra … akıllı çalış … tüm agentlarda». What wasted a day:
   - a full ▶→mp4 run before the change was proven cheaply;
   - a speed change that dropped a good picture;
   - a run finished after it already showed the fault;
   - two lanes on one thing;
   - pods left running.

   So:
   - prove a change on the cheapest real proof first (a replay on a frozen pool, one reel, a side-by-side against the last good one), then run full reels once for all fixes;
   - a speed change counts only with a same-pool sheet showing no picture lost;
   - stop a run the moment it shows the fault;
   - read the newest messages before building;
   - before any dispatch, ask every lead which lane already owns the job; route it there, never open a second lane on it (Nail 10-21 «aynı işi iki kez yaptırmamak, lane sayısını az tutmak ve tek atışta bitirmek»);
   - sum the running pod $/h before opening one.
   - finish each job in one shot: a late fix that would force a re-run is folded in before the run, never after.
8. **Done** = at the bar + one look + one honest line. Extras are named, not built.

9. **Creativity in the model, infrastructure in code (Nail 10-12 21:3x «tam olarak bu vizyonda ilerlesin, rule'a da yaz»).** Code never makes a creative decision a model could make; it builds what the model cannot: input, matte, cut, material search, generation plumbing, render, brand, hard-law checks, chat ops, measurement, caches. Every model sits behind a provider choice (data), so a better model drops into its node and the product gets better with it. The engine grows as a parts library (kinds, cards, assets, events): a reel that needs something new gets that part built ONCE, then every reel can use it.

10. **The prompt never grows; the QC gate does (Nail 10-13 «bir daha yaşanmasın … boktan renderlar olmasın … diskte boşuna bişey kaplaman yaşanmasın»).** Measured 10-12: the same code with a grown prompt scored 56 against 62. Each OBJECTIVE fault class therefore becomes a code default plus a DETECTOR that fails the render (black, freeze, overlap, safe zone, head zone, silent flash, stray marker, text hold, fragment). Taste is never a rule or detector (Nail 10-21 «hiçbir şey için yapma … duru bi zihin … kullanıcı kendi refleriyle belirler»): openings, moves, pacing and style follow the user's refs, read by the model. A render that fails the gate never reaches him. A creative prompt change ships only after an A/B judge against the last prompt. Every heavy job removes its own temp outputs once the final output is verified, so disk never fills from leftovers.

11. **Close the class, never patch the instance (Nail 10-21 «kökten çöz asla yamama global kural»).** Global, every app and project; every lane brief carries it.
   - **A bug is a class.** Find every sibling across the app before fixing one: same pattern, other windows, other styles, other paths. Close them with ONE shared mechanism plus a guard that fails CI on any new instance: a contract spec, a typed map, or a detector. Example, 10-21: one missing Ses icon crashed the page. The fix was a typed map with a fallback, a backend↔frontend contract spec, an error boundary on every window, and a click-everything crawler; adding one icon alone would have been the patch.
   - **A gate fixes before it refuses.** The engine crops, swaps or re-lays what it can itself. It refuses only the unfixable, with a plain Turkish next step. It never stops a run for something fixable, never warns and ships a known fault, never drops something silently, and never says «bitti» while anything after it is stale.
   - **A fix counts only when proven on the user's real path, under live-equal conditions.** That means the real run or UI, with the same network, keys and GPU. A unit spec alone doesn't count, because config can be filtered away before the run, and a box without WARP or stock judges nothing.
   - **Refused in review:** a per-item special case, a hard-coded value for one reel, a fix that only moves the symptom, a gate loosened to pass.

12. **Plain prompts, no busywork (Nail 10-21 «sade … sıkı sistem promptu, amelelik, yamama yapma»).** A creative model gets the real need, the refs and the material, said plainly at the length it needs: no growing MUST/NEVER lists, keyword tables or style rules in a system prompt. No mechanical per-item fiddling or step-by-step chains where one capable pass would do; if something repeats, build the ability once.

13. **Industry best practice, 100 % sure (Nail 10-21 «en iyi uygulamayı istiyoruz, endüstriyel 2026+2027 son pratikleri tuttuğuna 100 % emin ol … refactor et gerekirse, verimli çalıştığına emin ol, boşa test çalıştırma»).**
   - Every design choice matches current (2026–2027) industry practice. Check it in official docs, changelogs and production-grade examples, never from memory. If you're not sure, look it up before building.
   - Refactor when the structure is the problem. A clean refactor beats stacking a fix on a bad shape.
   - Work efficiently. Run the specs that cover what changed, once. No full suites by reflex, no repeated runs that prove nothing new, no heavy jobs on the Mac (see /clouding).

14. **Material search is engineering, not prompt growth (Nail 10-21 «malzeme çekme işi mükemmel bi mühendislikle olsun … hep üstüne üstüne prompt eklemesi yapma … akıllı yöntemle aksın»).** A wrong or missing picture (wrong city, a stand-in person, no portrait, a tiny image) is fixed in the retrieval system, never by another sentence in a prompt. The system's parts:
   - queries built from the named entities (who, where, what);
   - sources in order: real/agency, then culture stock, then generic, then generated;
   - ranking on measured facts: size, face present, entity match, freshness;
   - a sighted check that verifies each pick against the line it serves;
   - ONE placement path, so every pick passes the same gates;
   - caches.

   Each fault class gets a measured detector plus a same-pool before/after sheet. A prompt edit is the last resort (Nail «en son çare promptta düzenleme yaparsın … amelece olmasın»): only when the engineering cannot reach the fault, said plainly and briefly, and shipped after an A/B against the last prompt.

## Before saying "done" or opening anything for him
- [ ] Side by side with his ref / preferred version: is ours as good or better? Where not?
- [ ] Would he say «bok gibi», «sakin», «minik kutu», «titrek», «alakasız», «yama»? Check each.
- [ ] Is any part a fix for this one item instead of an ability? Did I close the whole class, with a guard that fails on the next instance?
- [ ] Does any gate refuse, warn or drop something the engine could have fixed itself? Was the fix seen working on the real path, under live-equal conditions?
- [ ] Did a model with eyes choose this, or a rule that flattened it?
- [ ] Has he said any of this before? If yes, why did it come back, and is that cause fixed?

## Pass it on
These rules bind every agent and session working for Nail, not only this one. When this skill changes, or when you brief or dispatch an agent, tell every running agent and peer session to follow it too («bu iyileştirme ve kurallara dikkat etsinler»): put the skill path in each brief, and send the update by message to any lane already running.
