# Audit: follow-up retrieval fails on the final build's own headline traces

Bounty: POIDH Base #1520, "Audit a Month of Offline AI Research".
Scope: DEVICE-FINAL M1 (seed 42), phone run vs desktop twin, 8 multi-turn items, 13 follow-up turns.
Status: published 13 Nov 2026 as evidence for the POIDH Base bounty #1520 claim. All evidence is public data; no code execution from the audited repos (static inspection plus analysis scripts over published trace files only).

## Summary

The record's U5 verdict closed follow-up retrieval with "no fix proposed... in the app that will be delivered, [the maintainer's FollowUp] rule already resolves the case" (`rnd/research/coordinator/arms/uxmatrix/U5-FOLLOWUP.md`). The final build's own headline traces contradict that verdict. On DEVICE-FINAL M1 seed 42, 5 of the 12 non-health follow-up turns retrieved no on-topic passage on either path (phone and desktop kept byte-identical garbage), the kept passage bodies contain zero topic terms, and the answers that followed were ungrounded: one is factually inverted against its own rubric and carries a false citation to an unrelated passage. The shipped `FollowUp` rule has three precise gaps that explain the failure, two of which the record's own simulations never tested on the final build. This does not change the headline U-total (phone/desktop parity holds; both sides fail the same way), but it reopens a closed verdict with a concrete, specified fix worth an estimated +0.02 to +0.05 U on reached follow-up items (the record's own FOLLOWUP-r2 estimate).

## 1. The evidence: 5 of 12 non-health follow-up turns retrieve nothing on-topic

Data: phone run log `rnd/research/coordinator/arms/device-final/out/df-m1-v04-s42.jsonl` (88 rows, sha256 `97fbbb4d...`) and desktop twin `rnd/research/coordinator/arms/native-rc/work/v04/lite-s42.jsonl` (75 items, sha256 `11d53d39...`). Retrieved/kept titles are identical on both paths for all 13 follow-up turns. Relevance was judged by title and, for the desktop file, by kept body text; the titles below let any reader re-judge.

| Turn | Follow-up question (truncated) | Retrieved / kept titles | Verdict |
|---|---|---|---|
| cw-033#t3 | "ok what's it good for besides being dry" | Simple English Wikipedia: OK (x3); Stack Exchange: low-calorie brownies; Stack Exchange: Local cuisine in Peru; Stack Exchange: rainy season in the Peru highlands | No on-topic passage. Topic is the Atacama Desert; nothing retrieved mentions it. |
| cw-034#t3 | "which one is older" | Stack Exchange: Older US dollars not accepted everywhere? (x2); Stack Exchange: 401(k) and IRA limits | No on-topic passage. Topic is Alhambra vs Mosque of Cordoba. |
| cx-034#t2 | "Tell me more about the statefulness problem in the second one." | Stack Exchange: Schengen visa denied boarding; Stack Exchange: rent a car in Eastern Europe; Simple English Wikipedia: Gödel's incompleteness theorems; Simple English Wikipedia: Twenty-second Amendment | No on-topic passage. Topic is hash-based signature schemes. |
| cx-035#t3 | "Also, I land at night and must give a talk at 09:00 the next morning." | Stack Exchange: cheapest parking in Switzerland; Stack Exchange: glacier-fed river peak flow; Stack Exchange: spending the night at Mumbai airport; Simple English Wikipedia: I-CABLE News Channel | No on-topic passage. Topic is jet lag / sleep. |
| cx-036#t3 | "Which thinker from my first question saw political authority mainly as protection from violent insecurity?" | (empty; retrievalUsed=false on phone, keptN=0 on desktop) | No passage at all. |

The 7 remaining non-health follow-up turns retrieved at least one on-topic passage (cw-033#t2, cw-034#t2, cw-035#t2, cw-036#t2, cw-036#t3, cx-033#t2, cx-036#t2). The 13th follow-up turn, cx-035#t2, is empty by the health gate's design (healthFixed, fixed safe text) and is excluded from the count.

The kept bodies confirm the titles are not a labeling artifact. For cw-033#t3 the desktop kept bodies are: the definition of the word "OK" (LEXICAL score 1.0, the query's filler word "ok" dominated retrieval), a brownie recipe answer, a Peru cuisine answer, and a Peru highlands weather answer. None mentions the Atacama. For cw-034#t3 the bodies are US-dollar acceptance advice and 401(k) limits. None mentions the Alhambra or the Mosque.

## 2. The answers: ungrounded, one inverted, one falsely cited

With nothing on-topic in context, the model answered from conversation history and parametric memory. The native engine provably cannot leak context across calls (`sync_prompt` in `android-native/engine/src/main/cpp/boar_jni.cpp`, lines 118-142, removes all KV entries beyond the common prefix), so the anaphora resolution in these answers comes from history in the generation prompt, not from retrieval:

- cw-033#t3 (phone): "The Atacama Desert is known for its extreme dryness, but it also hosts unique ecosystems and is a site for scientific research, particularly in astronomy and geology." Correct topic, zero supporting passage.
- cw-034#t3 (phone and desktop, near-identical): "The Alhambra is older than the Cordoba Mosque, as it dates back to the 13th century, while the Mosque dates to the 12th century [1]." This is inverted against the item's own rubric T3: "the Cordoba mosque is older (begun 785-786 under Abd al-Rahman I), about four and a half centuries before the Nasrid Alhambra" (`bench/v0.4/items.jsonl`, cw-034). The desktop answer's "[1]" cites the first kept passage, "Older US dollars not accepted everywhere?", which says nothing about either monument. A wrong answer wearing a citation to an unrelated passage is exactly the failure mode the `FollowUp` doc comment warns about: "the model resolved 'he' from the history and answered from memory, uncited: the weaker case for the bounty."
- cx-036#t3 (both paths, identical): "Hobbes saw political authority as protection from violent insecurity." Correct, with no retrieval at all.

Score context (judge.v1 Opus, both orders, mean of the three rubric dimensions, from the pinned judgment files): multi-turn items average 1.40/5 on the phone and 1.42/5 on the desktop, vs 1.71/5 and 1.70/5 for single-turn items. The paired phone-minus-desktop difference on multi-turn items is -0.02 (median 0.00), the same as on single-turn items (+0.01): there is no phone-specific deficit. Both paths fail the same way, which is why the headline parity finding (F-2239) stands and why this audit targets the shared rule, not the phone.

## 3. The mechanism: three gaps in the shipped FollowUp rule

File: `android-native/engine/src/main/java/team/sopa/boar/engine/answer/FollowUp.kt` (labrat-boar-fork, HEAD `606923a`).

Gap 1: the fallback is gated on emptiness, and the retriever never returns empty. `search()` tries the history-combined query only when `find(query)` returns an empty list. But lexical/semantic search over 13 packs returns low-relevance matches instead of nothing. cw-034#t3 ("which one is older", 2 content terms, no own name) qualifies for the fallback, yet the 3 irrelevant chunks it retrieved blocked the fallback from ever running. The fallback path is effectively dead code on any query that matches anything.

Gap 2: the direct-path trigger vocabulary misses common anaphora. `POINTS_BACK` (line 21) matches pronouns only (he/she/it/they/this/that/these/those, plus "there" handled separately). It does not match anaphoric noun phrases: "which one" (cw-034#t3), "the second one" (cx-034#t2), "my first question" (cx-036#t3). And the short-question fallback requires at most `SHORT_FOLLOW_UP_TERMS = 3` content terms (line 60), so long anaphoric questions exceed the limit and fall through with no history augmentation at all. cx-034#t2, cx-035#t3, and cx-036#t3 each match neither trigger.

Gap 3: the published eval runner passes no history, so FollowUp cannot run on the phone path at all. `DeviceEvalRunner.kt:365` builds `AnswerRequest(q.query, systemPrompt, styleReminder, maxTokens, seed = ...)`, leaving `history` null; `FollowUp.plan()` returns an empty plan when there is no previous question (line 98). The shipped chat passes history (`ChatViewModel.kt:683`). Caveat: the M1 build commit (`7788924a`, per the `LABRAT-7788924a-pixel8a-s42` arm string) is not in the public fork history and could not be verified, and the phone's anaphora-resolving answers imply history reached generation through some path in the M1 run. The retrieval failure on the phone is proven from the traces regardless; the exact code state of the M1 eval runner is not publicly verifiable and is flagged here rather than asserted. The desktop twin passes history to generation (historyTurns 2/4 in the trace) and still retrieves identical garbage, which shows Gaps 1-2 bite even when history is present. The desktop harness code itself is not public, so this audit cannot distinguish "desktop retrieval ignores history" from "FollowUp ran and its combined query still failed"; both are consistent with the published data, and the fix below addresses both.

## 4. Why this reopens a closed verdict, and what it does not claim

The record studied follow-up retrieval thoroughly, but never on the final build's headline traces:

- U5-FOLLOWUP (23 Oct, 0 LLM, research engine replays) found source survival of 1/19 (F8) and 4/13 (LONGSESSION), and closed with "no follow-up fix is proposed from this test... in the app that will be delivered, [the maintainer's] rule already resolves the case." Its caveat explicitly notes the test "measures sources and the 'Not found' path, not answer text" and that history still reaches the prompt. This audit measures the answer text on the final build and finds the rule did not resolve the case there.
- QFORM-RESULTS.md:71 simulated the FollowUp plan on 48 F8 fragments (0 LLM, replays): 20/48 gold-in-kept, with the residual identified as the 8 no-pronoun, 4-plus-word fragments. A simulation on dev fragments is not a test of the shipped build; the M1 traces show the same residual class failing live, plus the empty-gate failure (Gap 1) that the simulation's "finds nothing" framing assumes away.
- FOLLOWUP-r2 (revisit-closed-2026-10-21.json, lines 113-125) was closed as a set artifact, with an expected gain of "+0.02 to +0.05 U on reached follow-up items" if retested. The retest design is already specified there (gold kept ON-OFF >= +5, 0 lost, on in-session traces).

What this audit does NOT claim: the headline U-total (0.165/0.242) and the 0-of-71 result stand; the phone/desktop parity stands; no judge, scorer, or aggregation defect is alleged; the health-gate empty turn (cx-035#t2) is by design and is not counted as a failure.

## 5. How this differs from the existing 32 claims

No existing claim covers FollowUp, multi-turn retrieval, anaphoric queries, or citations on follow-up turns. The nearest neighbors differ in target:

- Claim #9243 (DeviceEvalRunner `local-` pack-ID prefix + pack sort breaking HealthGate): same file, different divergence (pack configuration vs history passing). This audit cites it as adjacent, not derivative.
- Claims #9227, #9503, #9349, #9251 (retrieval is / is not the wall): single-turn delivery on the headline set. This audit is about follow-up turns only, a layer none of them decomposed (the phone traces log no chunk IDs, and neither claim examined the follow-up turn subset or the FollowUp rule).
- Claim #9473 (delivery split / pooling artifact): about the F-2242 split and title matching, not about follow-up retrieval mechanics.
- Claim #9556 (citation step score-neutral): about `blind()` stripping citations before judging. This audit's false-citation observation is about what the user sees (a cited claim whose source does not support it), not about judge scoring.
- Errata E1-E8 (`docs/ERRATA-BOUNTY-1520.md`): none mentions follow-up retrieval, FollowUp, or multi-turn items.

## 6. Concrete improvement

1. Relevance-gate the fallback, not emptiness-gate it. In `FollowUp.search()`, run the history-combined query when the direct query's best chunk scores below a relevance floor (or when no kept chunk shares a content term with the query, reusing the existing `aboutTheQuestion` stemmed-overlap check), not only when the result list is empty. This revives the fallback for queries like cw-034#t3 that currently retrieve only junk.
2. Extend the direct-path triggers to anaphoric noun phrases: "which one", "the second/first one", "my first/second question", "that one", and fragments opening with "and"/"then" (the record's own QFORM suggestion), or raise `SHORT_FOLLOW_UP_TERMS` with the `aboutTheQuestion` filter as the guard against topic borrowing.
3. Anchor to the previous turn's kept articles (the FOLLOWUP-r2 design already specified in revisit-closed-2026-10-21.json): when a follow-up resolves to the previous turn's topic, carry the previous turn's kept chunk IDs forward as the retrieval seed.
4. Retest on the M1 multi-turn items with the record's own frozen gate: gold kept ON-OFF >= +5 and 0 lost, plus a citation-support regression check (every cited claim in a follow-up answer must overlap a kept passage body; cw-034#t3's "[1]" would fail it). Pass history in the device eval runner so the phone measurement tests the same path the shipped chat runs, and record the eval-runner commit in the run log so the code state is verifiable.
5. Expected value: +0.02 to +0.05 U on reached follow-up items per the record's FOLLOWUP-r2 estimate, plus the removal of a false-citation pattern in which wrong answers carry citations to unrelated passages.

## 7. Reproduction

Prerequisites: the three public repos at the commits cloned 13 Nov 2026 (boar-research HEAD `6b7caca`, boar-bench, labrat-boar-fork HEAD `606923a`) and the boar-traces dataset. No model calls, no keys, no writes.

1. Verify the trace pins: `sha256sum` of `rnd/research/coordinator/arms/device-final/out/df-m1-v04-s42.jsonl` must equal `97fbbb4d8079302e7312f8e63652d2f86ad49356abe9bdeacf2e45d0107ec0df`; of `rnd/research/coordinator/arms/native-rc/work/v04/lite-s42.jsonl` must equal `11d53d3941930652a2e0e808ab1b9afaa124639d92dda1597f1995f2d7a91db6` (both from `TRACES.tsv`).
2. Extract follow-up turns: phone rows whose `queryId` contains `#t2`/`#t3` (13 rows); desktop `turns[]` with `turn > 1` (13 turns). Compare `retrievedTitles` (phone) to `turns[].kept[].title` (desktop): identical sets on all 13.
3. Read the kept bodies for cw-033#t3 and cw-034#t3 in the desktop file (`turns[].kept[].body`): no body mentions Atacama, Alhambra, or the Mosque.
4. Compare answers: phone `answer` vs desktop `turns[].storedText` for cw-034#t3 (both inverted, desktop carries "[1]"); check the rubric T3 for cw-034 in `bench/v0.4/items.jsonl` (boar-bench) for the correct ordering (mosque begun 785-786, about 4.5 centuries before the Nasrid Alhambra).
5. Read `FollowUp.kt` (`plan` lines 97-103, `search` fallback branch, `POINTS_BACK` line 21, `SHORT_FOLLOW_UP_TERMS` line 60) and confirm: fallback requires an empty direct result; "which one" / "the second one" / "my first question" match neither trigger; `DeviceEvalRunner.kt:365` passes no history while `ChatViewModel.kt:683` does.
6. Score context: combine both orders per item in `dev-s42.opus.judgments.jsonl` (sha256 `bd3cc103...`) and `lite-s42.opus.judgments.jsonl` (sha256 `0d92351f...`); multi-turn items (cw-033..036, cx-033..036, `multiTurn: true` in `questions.jsonl`) average 1.40/1.42 vs 1.71/1.70 for single-turn items.

## 8. Limitations

- Relevance judgments on the 13 follow-up turns are the auditor's, made from titles plus kept bodies; the titles are quoted in full in Section 1 so readers can re-judge. The count (5/12) treats cw-035#t2 (Osaka passages for a Japan-trip follow-up) as on-topic; excluding it as borderline would make it 5/11 with the same conclusion.
- The M1 build commit is not publicly verifiable (see Gap 3 caveat). Gap 3 is stated for the published code and flagged, not asserted for the M1 run.
- The desktop harness code is not public; the desktop mechanism (history to retrieval vs combined-query failure) cannot be distinguished from published data.
- The fix's +0.02 to +0.05 U estimate is the record's, not the auditor's; the retest in step 4 of Section 6 is what would confirm or refute it.
- Judge files used for score context are judge.v1 (Opus), not the v1.1b headline judge; the comparison is paired (same judge, same seed, both paths), so the conclusion (no phone-specific deficit) does not depend on the judge version.
