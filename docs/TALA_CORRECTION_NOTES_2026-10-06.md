# TALA — Correction Notes (6 Oct 2026)

Paste-ready edits based on `TALA_DELTA_REPORT_2026-10-06.md`. Each edit shows the **file and section**, the **old text** (exact, as it is in the file today), and the **new text**. The baseline files are not changed by this note.

- **File A** = `TALA_PROJECT_INSTRUCTIONS.md`
- **File B** = `TALA Chatbot Discovery.md` (System Discovery part, the same text as `CIGNAL_CHATBOT_SYSTEM_DISCOVERY.md`)
- **File C** = the same file, Process Flow part (the same text as `CIGNAL_CHATBOT_PROCESS_FLOW.md`)

---

## A. Project Instructions (File A)

### A-1 · §5, paragraph 1
**Old:**
> (2) Gemini AI chat answering from a knowledge base (~342 articles) with a self-check, (3) a live-agent console (departments **Customer Service** and **Sales**, skill-based routing, `strict skill routing` on),

**New:**
> (2) Gemini AI chat answering from a knowledge base (345 articles as of 6 Oct 2026) with a self-check, (3) a live-agent console (departments **Customer Service** and **Sales**, skill-based routing; `strict skill routing` **off** since at least 6 Oct 2026, so chats fall back to any free agent when no skill-matched agent is free),

### A-2 · §5, "Key settings" sentence
**Old:**
> Key settings seen on 2026-10-02 [OBSERVED]: bot idle timeout **30 min** (beta showed ~2 min); 3 KB attempts before the "Talk to agent" button;

**New:**
> Key settings re-checked on 2026-10-06 [OBSERVED]: bot idle timeout **30 min** (beta showed ~2 min); **1** KB attempt before the "Talk to agent" button (was 3 on 2026-10-02; change date unknown);

(The rest of the sentence is unchanged.)

### A-3 · §6, the two-rows bullet
**Old:**
> row 1 `handed_off` (bot segment) and row 2 the agent outcome (`resolved` / `unresolved`) a few seconds later.

**New:**
> row 1 `handed_off` (bot segment, written at handoff) and row 2 the agent outcome (`resolved` / `unresolved`) written when the agent chat closes (Oct 1–5 average: about 12 min later for resolved, about 21 min for unresolved).

### A-4 · §7, `chatbot_session_summary` bullet
**Old:**
> `resolution_status` (abandoned / handed_off / resolved / unresolved)

**New:**
> `resolution_status` (abandoned / handed_off / resolved / unresolved / **resolved_unconfirmed** — the last one seen from 2026-10-03)

### A-5 · §7, `engagement` bullet
**Old:**
> `queued_at`, `assigned_at`, `closed_at`.

**New:**
> `queued_at`, `assigned_at`, `closed_at`, plus `tags` (close reason: `auto-closed-idle`, `customer-ended`, `auto-abandoned-queue`, blank = agent-ended), `page_id`, `context_passed`, `created_at`, `updated_at`, `last_customer_message_at`, `last_agent_message_at`. (`required_skill`, `ticket_ref`, `session_id` and `idle_warning_stage` exist but the AI rejects them by name. Do not rely on them.)

### A-6 · §7, `engagement_event` bullet
**Old:**
> No "escalation" or "offered" event type exists.

**New:**
> No "escalation" or "offered" event type exists here. Survey offered / accepted / declined / completed times are in `chatbot_survey_invitation`.

### A-7 · §7, `chatbot_csat_response` bullet
**Old:**
> `chatbot_csat_response`: `rating`, `recommend_score` (0–9), `ease_of_resolution`, `resolved`, `last_intent`, `conversation_id`, `resolved_by`, `created` (no `ticket_ref`).

**New:**
> `chatbot_csat_response`: `csat_response_id`, `channel`, `session_id`, `sender_id`, `conversation_id`, `bot_key`, `resolved_by` (rule_flow / ai_chat / **chatbot** / live_agent), `last_intent`, `created`, `comments`, `rating`, `recommend_score` (0–9), `ease_of_resolution`, `cso_satisfaction`, `resolved` (still no `ticket_ref`).

### A-8 · §7, "Others seen" bullet
**Old:**
> - Others seen: `agent_profile`, `chatbot_prechat_profile`; a KB lookup table exists (name unconfirmed).

**New:**
> - KB table: `chatbot_article_usage` (user_message, article_id, article_title, kb_source, channel, conversation_id, created). Event log: `chatbot_analytics_event` (event_type, node_key, extra, result_count, created [UTC], conversation_id, bot_key, …).
> - `chatbot_survey_invitation`: offered_at, accepted_at, declined_at, completed_at, resolved_by, conversation_id, csat_response_id (engagement_id is always null). Use it for the CSAT response rate.
> - `chatbot_customer_visit` (customer_status new/returning, identity_basis account/channel_user) and `chatbot_customer_identity` (first/last seen).
> - Others seen: `agent_profile`, `chatbot_prechat_profile`, `chatbot_dpa_consent`, `chatbot_feed_event`, `app_reviews`, `team`, `chatbot_messenger_page`, `agent_status_history` (`*_ph` columns are Manila clock labeled +00:00), `chatbot_health_sample`, `chatbot_facebook_service_event`. (List from the Ad Hoc AI plus one TOP 1 test each, 2026-10-06.)

### A-9 · §7, rule 1
**Old:**
> A CTE, an invented table, or a vague "dataset" request gets rejected ("table not allowed").

**New:**
> An invented table gets rejected ("The table '…' does not exist in the provided schema"), and a query with no source table gets rejected ("rejected for safety (no source table)"). On 2026-10-06 a prompt with an explicit CTE returned results (the AI probably rewrote it), but keep "no WITH/CTE" in every prompt.

### A-10 · §7, rule 4 (timestamps)
**Old:**
> 4. **Timestamps**: `created`, `queued_at`, `assigned_at`, `closed_at` are **UTC**: add 8 hours with `DATEADD(hour, 8, …)`. `engagement_event.created_at` is already Manila: do not add. Convert **before** grouping by date. Verify before assuming IT's fix, because the API still returned +00:00 on 2026-10-02.

**New:**
> 4. **Timestamps** (re-verified 2026-10-06, NOT fixed): `created` (summary, CSAT, event log), `queued_at`, `assigned_at`, `closed_at` are **UTC (+00:00)**: add 8 hours with `DATEADD(hour, 8, …)`. `engagement_event.created_at` and `engagement.created_at / updated_at` come back as **+08:00** (Manila): do not add. Convert **before** grouping by date. When you compare a UTC column with an `engagement_event` time (DATEDIFF), compare the raw values: adding +8 first gives −480 min. On `SELECT *` the AI now outputs `created_local` (already +8); never add 8 to it again.

### A-11 · §8, "Confirmed Yes, resolved but left the CSAT unfinished"
**Old:**
> Whether partial CSAT is saved is [UNKNOWN].

**New:**
> Partial CSAT does not appear to be saved as a CSAT response: all CSAT rows have every required answer, and `chatbot_survey_invitation` shows 102 accepted vs 90 completed [INFERRED, 2026-10-06].

### A-12 · §8, Chats Offered
**Old:**
> Per-agent offered does not exist, and out-of-hours handoff requests are never queued.

**New:**
> Per-agent offered does not exist, and out-of-hours handoff requests are never queued. Tested 2026-10-06: Oct 1–5 = 976 offered (903 excl. `webchat:test`), 950 answered, 8 queue-abandoned. Some offered chats are neither answered nor abandoned (the customer likely left the queue).

### A-13 · §8, CSAT
**Old:**
> - **CSAT**: team formula = count of ratings 4 and 5 ÷ total responses (not the average), shown with the response count.

**New:**
> - **CSAT**: team formula = count of ratings 4 and 5 ÷ total responses (not the average), shown with the response count. **Response rate** = completed ÷ offered from `chatbot_survey_invitation`. `resolved_by` also has the value `chatbot` (from 2026-10-03). Count it with `ai_chat` + `rule_flow` as bot-resolved.

### A-14 · §8, Routing problems (add a sentence at the end)
**Add after:**
> …and **does not prove** the chat went to the Sales queue.

**New sentence:**
> Since strict skill routing is **off** (seen 2026-10-06), an agent without the matching skill can receive a chat when no skill-matched agent is free. That is designed fallback, not misrouting.

### A-15 · §8, Sales path
**Old:**
> The Upgrade Plan and Account Management nodes are handoff nodes whose target queue is [UNKNOWN].

**New:**
> The Upgrade Plan and Account Management nodes are handoff nodes with no queue or skill target in their config. In the data, Account Management handoffs went to `cs_general` (109 of 109) and Upgrade Plan to `cs_general` (7 of 8, 1 `sales_general`) [OBSERVED 2026-10-06].

### A-16 · §8, Built-in dashboard caution
**Old:**
> and "Handoffs" counts events. Do not quote them as management KPIs.

**New:**
> and "Handoff Requests" (formerly "Handoffs") counts events. "Handoffs Completed" = live engagements. "Concerns" = summary rows (segment grain). Do not quote them as management KPIs.

### A-17 · §8, add a new bullet (after "Standard exclusions")
**New:**
> - **Resolved (Unconfirmed)**: `resolution_status = 'resolved_unconfirmed'` exists from 2026-10-03 (33 rows, all ticketed, no handoff reason). Its meaning is [UNKNOWN]. Report it on its own line, not inside Bot Resolved, until IT defines it [PROPOSED].
> - **Live-agent close reason**: `engagement.tags`. Every `auto-closed-idle` has `resolved = 0` and every `customer-ended` has `resolved = 1`, so always show Agent Resolution with this split.

### A-18 · §9, open items
**Old:**
> Which queue/skill the Account Management and Upgrade handoffs use; what starts a new Conversation ID; whether partial CSAT is stored; what "Resolved (Unconfirmed)" keys on; meaning of `human_only` and `user_stuck`; whether all timestamps are now Manila time (Sir Gener is confirming with IT); the KB table name. Full list: discovery §24.

**New:**
> What starts a new Conversation ID; what "Resolved (Unconfirmed)" keys on (data exists since 2026-10-03); meaning of `human_only` and `user_stuck`; when and why min KB attempts (3→1) and strict skill routing (on→off) changed; why Sales queue volume fell from 2026-10-03; whether `engagement.ticket_ref` / `required_skill` can be exposed to Ad Hoc. Answered on 2026-10-06: handoff queues (CS), KB table (`chatbot_article_usage`), partial CSAT (not stored as a response), timestamps (still UTC except engagement_event / engagement created_at). Full list: discovery §24.

---

## B. System Discovery (File B)

### B-1 · §1, finding F6
**Old:**
> | F6 | **Timezones are mixed.** API timestamps and dashboard day buckets are **UTC**. Customer Journey, SDQ tickets and live-agent event timelines show **Manila time**. **OBSERVED** |

**New:**
> | F6 | **Timezones are mixed (re-confirmed 6 Oct 2026; IT's fix not visible).** API timestamps, Ad Hoc `created`/`queued_at`/`assigned_at`/`closed_at`, and Dashboard/Analytics day buckets are **UTC**. Customer Journey, SDQ tickets, Concern Summaries display, Live Agent Reports day buckets, and `engagement_event.created_at` (+08:00) are **Manila time**. **OBSERVED** |

### B-2 · §1, engine 2 and §2.2 Knowledge base row
**Old (§1):** `typed questions are answered from about 342 indexed eLearning articles.`
**New:** `typed questions are answered from 345 indexed eLearning articles (index rebuilt 5 Oct 2026).`

**Old (§2.2):** `| Knowledge base | E-Learning articles (≈342 indexed), 11 KB categories |`
**New:** `| Knowledge base | E-Learning articles (345 indexed on 6 Oct 2026), 11 KB categories |`

### B-3 · §2.7, Escalation row
**Old:** `Customer taps **Talk to agent** (shown after **3** KB attempts — "Min KB attempts before handoff")`
**New:** `Customer taps **Talk to agent** (shown after **1** KB attempt since at least 6 Oct 2026; was 3 on 2 Oct — "Min KB attempts before handoff")`

### B-4 · §3.5, AI chat row
**Old:** `Continue, Talk to agent (after 3 attempts), or close`
**New:** `Continue, Talk to agent (after 1 attempt; was 3), or close`

### B-5 · §5 J1 table and J3 table
**Old (J1):** `Customer types "Live agent" or taps Talk to agent (gate: 3 KB attempts)`
**New:** `Customer types "Live agent" or taps Talk to agent (gate: 1 KB attempt; was 3)`

**Old (J3):** `| Escalation | After 3 KB attempts a **Talk to agent** button appears; typing an agent request bypasses the gate |`
**New:** `| Escalation | After 1 KB attempt (was 3) a **Talk to agent** button appears; typing an agent request bypasses the gate |`

### B-6 · §8.1 States, "Resolved (Unconfirmed)" row
**Old:** `| Resolved (Unconfirmed) | OBSERVED as a filter value | Session Summaries filter — 0 rows seen |`
**New:** `| Resolved (Unconfirmed) | OBSERVED (data value from 3 Oct 2026) | summary resolution_status = resolved_unconfirmed — 33 rows by 5 Oct; meaning UNKNOWN |`

### B-7 · §9.1, Sales and Promo rows (column "Description (classifier guidance)")
**Old:** `| Sales | New purchase, upgrade, subscribing |`
**New:** `| Sales | New application, or subscribing (skill guidance as of 6 Oct 2026) |`

**Old:** `| Promo | Promos or discounts |`
**New:** `| Promo | Promos or discounts, upgrade, downgrade (skill guidance as of 6 Oct 2026) |`

### B-8 · §9.3, Event log row
**Old:** `| Event log (`handoff.extra`) | `user_requested` (button), `user_typed_request`, `user_agreed_to_offer` | OBSERVED |`
**New:** `| Event log (`handoff.extra`) | `user_requested` (button; console label "Subscriber-initiated (asked for agent)"), `user_typed_request`, `user_agreed_to_offer`, `landing_apply_now` (from 2 Oct 2026) | OBSERVED |`

### B-9 · §10, `resolution_status` row
**Old:** `| resolution_status | resolved / unresolved / handed_off / abandoned (+ filter "Resolved (Unconfirmed)") |`
**New:** `| resolution_status | resolved / unresolved / handed_off (console label "Handoffs Completed") / abandoned / resolved_unconfirmed (data from 3 Oct 2026) |`

### B-10 · §10, CSAT row
**Old:** `resolved_by (rule_flow / ai_chat / live_agent), last_intent | Survey | `chatbot_csat_response``
**New:** `resolved_by (rule_flow / ai_chat / chatbot / live_agent), last_intent | Survey | `chatbot_csat_response`; offers in `chatbot_survey_invitation``

### B-11 · §10, engagement row
**Old:** `| engagement_id, queue (cs_general…), state (closed…), resolution (Resolved/Not resolved), disposition, assigned agent, queued_at / assigned_at / closed_at | Live-agent engagement |`
**New:** `| engagement_id, queue (cs_general…), state (closed…), resolution (Resolved/Not resolved), disposition, assigned agent, queued_at / assigned_at / closed_at (UTC), tags = close reason (auto-closed-idle / customer-ended / auto-abandoned-queue), created_at / updated_at (+08:00) | Live-agent engagement |`

### B-12 · §10, add rows at the end of the table
**New rows:**
> | survey offered / accepted / declined / completed | Survey lifecycle per conversation | `chatbot_survey_invitation` | System | offered_at | CSAT response rate OBSERVED |
> | customer_status, identity_basis | New vs returning per visit | `chatbot_customer_visit` | System | returning / account | Repeat contact OBSERVED |

### B-13 · §11 Business Rules
**Old:** `| KB attempts before handoff | Customer wants an agent via AI path | **3** KB attempts | "Talk to agent" button appears after 3 tries (0 = always) |`
**New:** `| KB attempts before handoff | Customer wants an agent via AI path | **1** KB attempt (was 3 on 2 Oct 2026) | "Talk to agent" button appears after 1 try (0 = always) |`

**Old:** `| Strict skill routing | Assignment | Enabled | Only agents with the matching skill get the chat | Queue wait depends on skill coverage | OBSERVED |`
**New:** `| Strict skill routing | Assignment | **Disabled** (was Enabled on 2 Oct 2026) | Prefers a skill-matched agent; if none is free, falls back to any available agent by lowest load | Agents may handle chats outside their skills; this is not misrouting | OBSERVED |`

### B-14 · §12, Escalation row
**Old:** `| **Escalation** | Button after 3 attempts,`
**New:** `| **Escalation** | Button after 1 attempt (was 3),`

### B-15 · §14.1 Console KPI table
**Old:** `| Handoffs | 341–343 | Count of `handoff` **events** (sessions with a handoff ≈ 257) | OBSERVED reconciled |`
**New:** `| Handoff Requests (was "Handoffs") | 341–343 | Count of `handoff` **events** (sessions with a handoff ≈ 257) | OBSERVED reconciled |`

**Add rows (6 Oct 2026):**
> | Concerns ("topics worked to a conclusion") | 2,813 (30-day view, 6 Oct) | Count of summary rows (all 5 statuses) — segment grain | OBSERVED reconciled |
> | Handoffs Completed ("requests that reached an agent") | 1,018 (30-day view, 6 Oct) | Live engagements (= handed_off rows); "% of requests" = ÷ handoff events | OBSERVED reconciled |

### B-16 · §14.2, CSAT Response Rate row
**Old:** `| CSAT Response Rate | Survey coverage | Responses | Eligible closed episodes (bot-resolved + CS agent-closed, not idle) | N ÷ D | % | Episode | — |`
**New:** `| CSAT Response Rate | Survey coverage | `completed_at` count | `offered_at` count (`chatbot_survey_invitation`) | N ÷ D | % | Invitation | SX |`

### B-17 · §15 Dimension Dictionary
**Old:** `| Resolution Status (segment) | Summary status | resolved, unresolved, handed_off, abandoned |`
**New:** `| Resolution Status (segment) | Summary status | resolved, unresolved, handed_off, abandoned, resolved_unconfirmed |`

**Old:** `| Resolved By | Survey resolver | rule_flow, ai_chat, live_agent |`
**New:** `| Resolved By | Survey resolver | rule_flow, ai_chat, chatbot, live_agent |`

**Old:** `| New vs Returning | Customer seen before | new, returning (30 days) | Repeat contact | PROPOSED |`
**New:** `| New vs Returning | Customer seen before | new, returning (`chatbot_customer_visit.customer_status`, basis account / channel_user) | Repeat contact | OBSERVED source; window rule UNKNOWN |`

### B-18 · §24, add an "Answered 6 Oct 2026" note under these questions
> Q6 → partial: `chatbot_customer_visit` / `chatbot_customer_identity` hold new/returning by account or channel user.
> Q7 → **No.** `created`, `queued_at`, `assigned_at`, `closed_at` and the API are still UTC; `engagement_event.created_at` and `engagement.created_at` are +08:00.
> Q10 → still open; `resolved_unconfirmed` now has 33 rows (from 3 Oct).
> Q28 → still open; `engagement.ticket_ref` exists but is not queryable by name.
> Q30 → **Yes**: `chatbot_survey_invitation` stores offered / accepted / declined / completed.
> Q31 → partial: the console says "Unattributed" = traffic from before product tracking began.
> Q32 → partial: live-agent engagements are kept indefinitely (retention disabled).
> New Q33: When and why were min KB attempts (3→1) and strict skill routing (on→off) changed?
> New Q34: Why did `sales_general` volume fall from 3 Oct (52, 41, then 7, 3, 3 per day)?
> New Q35: Can `engagement.ticket_ref`, `required_skill` and `session_id` be added to the Ad Hoc schema description?

### B-19 · §25, L4
**Old:** `| L4 | Ad Hoc Reports blocks schema queries (`INFORMATION_SCHEMA`), so the table inventory is based on observed outputs and earlier validated prompts |`
**New:** `| L4 | Ad Hoc Reports blocks schema queries (`INFORMATION_SCHEMA`). On 6 Oct 2026 the Ad Hoc AI listed 19 tables and each one answered a TOP 1 query, but this is not a formal schema view |`

### B-20 · §28.1, Session Summaries row
**Old:** `| Session Summaries | ✅ Explored | 825 rows analyzed in-browser (PII not exported) |`
**New:** `| Concern Summaries (renamed from Session Summaries) | ✅ Explored | 825 rows analyzed in-browser (PII not exported); re-checked 6 Oct |`

### B-21 · §28.4 Top data gaps, item 5
**Old:** `5. No survey-offered / survey-declined events (no CSAT response-rate denominator).`
**New:** `5. ~~No survey-offered / survey-declined events~~ — available in `chatbot_survey_invitation` (found 6 Oct 2026).`

### B-22 · §28.6, remove "CSAT response rate" and add it to §28.5
**Old (§28.6):** `· CSAT response rate ·`
**New (§28.6):** ` · ` (delete the item)
**§28.5, append:** ` · CSAT response rate (survey invitations) · new vs returning customers (customer visits) · live-agent close-reason split (engagement.tags)`

---

## C. Process Flow (File C)

### C-1 · §1 Overall flow (Mermaid line)
**Old:** `G -- "3 KB attempts → Talk to agent button<br/>or customer types 'agent'" --> Q`
**New:** `G -- "1 KB attempt (was 3) → Talk to agent button<br/>or customer types 'agent'" --> Q`

### C-2 · §12 AI free-text flow (Mermaid line)
**Old:** `B --> C["KB retrieval: semantic + lexical<br/>(about 342 articles; Handling category excluded)"]`
**New:** `B --> C["KB retrieval: semantic + lexical<br/>(345 articles on 6 Oct 2026; Handling category excluded)"]`

### C-3 · §13 Escalation flow (Mermaid lines)
**Old:** `T1["Talk to agent button<br/>(after 3 KB attempts)<br/>extra: user_requested"]`
**New:** `T1["Talk to agent button<br/>(after 1 KB attempt; was 3)<br/>extra: user_requested"]`

**Old:** `QU --> AS{"Agent with matching skill free?<br/>(strict skill routing ON)"}`
**New:** `QU --> AS{"Agent with matching skill free?<br/>(strict skill routing OFF: else any free agent)"}`

**Old:** `AS -- "Yes" --> AG["Assigned → 'You are now connected to {agent}'"]`
**New:** `AS -- "Yes, or any free agent (fallback)" --> AG["Assigned → 'You are now connected to {agent}'"]`

---

## D. Prompt texts used in the regression (for reuse)

### D-1 · Live-agent WebChat, one row per conversation (H1 — returned 182 / 177 / 0 for Oct 1)
> Create a TABLE (not a chart). One SELECT only, no WITH/CTE, no derived tables in FROM. FROM engagement e OUTER APPLY (SELECT TOP 1 s.ticket_ref FROM chatbot_session_summary s WHERE REPLACE(CAST(s.conversation_id AS varchar(64)), '-', '') = REPLACE(CAST(e.conversation_id AS varchar(64)), '-', '') AND s.ticket_ref IS NOT NULL AND s.created >= DATEADD(minute, -2, e.queued_at) ORDER BY s.created ASC) t WHERE e.channel LIKE 'webchat%' AND DATEADD(hour, 8, e.queued_at) >= '2026-10-01' AND DATEADD(hour, 8, e.queued_at) < '2026-10-02' AND e.engagement_id = (SELECT MIN(e2.engagement_id) FROM engagement e2 WHERE e2.conversation_id = e.conversation_id AND e2.channel LIKE 'webchat%' AND DATEADD(hour, 8, e2.queued_at) >= '2026-10-01' AND DATEADD(hour, 8, e2.queued_at) < '2026-10-02'). Columns in this exact order: Engagement = e.engagement_id, Chat Session ID = REPLACE(CAST(e.conversation_id AS varchar(64)), '-', ''), Channel = e.channel, Queue = e.queue_key, Queued PH = DATEADD(hour, 8, e.queued_at), Ticket Ref = t.ticket_ref. Sort by e.queued_at ascending.

To change the day, edit the two date pairs. To drop test traffic, add `AND e.channel <> 'webchat:test'` in both WHERE parts.

### D-2 · Chats Offered by Manila date and queue (H2)
> Create a TABLE (not a chart). One SELECT only, no WITH/CTE, no derived tables. From engagement where DATEADD(hour, 8, queued_at) >= '2026-10-01' and DATEADD(hour, 8, queued_at) < '2026-10-06'. Group by Manila date and queue_key. Columns in this exact order: PH Date = CAST(DATEADD(hour, 8, queued_at) AS date), Queue = queue_key, Chats Offered = COUNT(DISTINCT engagement_id), Offered excl Test = COUNT(DISTINCT CASE WHEN channel <> 'webchat:test' THEN engagement_id END), Answered = COUNT(DISTINCT CASE WHEN assigned_at IS NOT NULL THEN engagement_id END), Answered excl Test = COUNT(DISTINCT CASE WHEN assigned_at IS NOT NULL AND channel <> 'webchat:test' THEN engagement_id END), Queue Abandoned = SUM(CASE WHEN state = 'abandoned' THEN 1 ELSE 0 END). Order by PH Date, Queue.

### D-3 · CSAT response rate (new)
> Create a TABLE (not a chart). One SELECT only, no WITH/CTE, no derived tables. From chatbot_survey_invitation where DATEADD(hour, 8, offered_at) >= '2026-10-01' and DATEADD(hour, 8, offered_at) < '2026-10-06' and channel <> 'webchat:test'. Group by resolved_by. Columns in this exact order: Resolved By = resolved_by, Offered = COUNT(offered_at), Accepted = COUNT(accepted_at), Declined = COUNT(declined_at), Completed = COUNT(completed_at).

(D-3 is **untested** with the date filter. The all-time version without the filter ran OK.)

### D-4 · Live-agent close reasons (new)
> Create a TABLE (not a chart). One SELECT only, no WITH/CTE, no derived tables. From engagement where DATEADD(hour, 8, queued_at) >= '2026-10-01' and DATEADD(hour, 8, queued_at) < '2026-10-06' group by queue_key, state, tags: columns in this exact order: Queue = queue_key, State = state, Tags = tags, Engagements = COUNT(*), Resolved = SUM(CASE WHEN resolved = 1 THEN 1 ELSE 0 END), With Disposition = COUNT(disposition). Order by Queue, Engagements DESC.

(D-4 was tested with the equivalent UTC window `queued_at >= '2026-09-30T16:00:00' and < '2026-10-05T16:00:00'`.)
