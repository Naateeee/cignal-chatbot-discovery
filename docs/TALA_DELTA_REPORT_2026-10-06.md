# TALA Chatbot — Delta Report vs 2 Oct 2026 Baseline

| Item | Value |
| --- | --- |
| Check date | Tue 6 Oct 2026, about 10:00–11:00 AM Manila |
| Baseline | `TALA Chatbot Discovery.md` (combined System Discovery + Process Flow, read-only discovery of 2 Oct 2026) and `TALA_PROJECT_INSTRUCTIONS.md` |
| Access | Role `cegadmin` (unchanged: `isRootAdmin = false`). Read-only. No settings saved, no chats started, no live chats accepted, no exports |
| Method | Console pages, the console's own read-only GET APIs, and 40+ small Ad Hoc prompts (one SELECT each, TABLE output, TOP-N or one-day filters) |
| Privacy | No customer names, numbers, emails or account numbers are copied here. Agent names are also left out. Ticket refs and conversation IDs are kept only where they prove a point |

Evidence labels: **OBSERVED** (seen directly today), **INFERRED** (reasoned, not confirmed), **UNKNOWN** (not seen). Status values: `Unchanged`, `Changed`, `New`, `Removed`, `Cannot verify`. "Not in baseline" means the item exists today but the baseline did not record it, so it may be old or new.

---

## 1. Executive summary

1. **Timestamps were NOT fixed to Manila.** `chatbot_session_summary.created`, `engagement.queued_at / assigned_at / closed_at`, the CSAT `created`, the event log `created`, and the console API all still return **UTC (+00:00)**. `engagement_event.created_at` returns **+08:00**. The +8-hour rule for UTC columns is still required. Today's Oct 1 regression returned exactly the baseline result (182 rows / 177 with tickets / 0 duplicates), so there is no double +8. **OBSERVED**
2. **Two business settings changed:** "Min KB attempts before handoff" went from **3 to 1**, and "Live Agent: Strict Skill Routing" went from **Enabled to Disabled**. All other settings match the baseline. The change dates are **UNKNOWN** because Audit Logs are not accessible to this role. **OBSERVED**
3. **A fifth outcome value is now in the data:** `resolution_status = 'resolved_unconfirmed'` has 33 rows (first seen 3 Oct, Manila date). CSAT `resolved_by` also has a new value, `chatbot` (4 responses, from 3 Oct). The console renamed "Session Summaries" to **"Concern Summaries"**, renamed `handed_off` to **"Handoffs Completed"**, and added the KPI cards **Concerns** and **Handoffs Completed**. **OBSERVED**
4. **Ad Hoc can see more than we knew.** The AI reported, and test queries confirmed, these tables: `chatbot_survey_invitation` (survey offered / accepted / declined / completed), `chatbot_article_usage` (the KB table), `chatbot_analytics_event`, `chatbot_customer_visit` (new vs returning), `chatbot_customer_identity`, `agent_status_history`, `chatbot_health_sample`, `chatbot_facebook_service_event`, `team` and `chatbot_messenger_page`. `engagement.tags` holds the close reason (`auto-closed-idle`, `customer-ended`, `auto-abandoned-queue`). **OBSERVED**
5. **Unchanged:** the menu flow (73 active nodes, 147 options, 30 Y/N leaves, the same 31 article references), the 13 skill categories and 13 landing concerns, teams, the 5 FB pages, both bots, 14 agent statuses, 4 greetings, 49 dispositions, the queue keys, the engagement event types, the handoff-reason values in the summary table, the two-summary-rows-per-handoff behavior, and the built-in KPI formulas. The KB index grew from about 342 to **345** articles (rebuilt 5 Oct, "+25 new"). **OBSERVED**

---

## 2. Change log

### A. Settings

| Area | Item | Baseline (Oct 2) | Now (Oct 6) | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| Settings | Bot session (idle) timeout | 30 min | 30 min | Unchanged | OBSERVED |
| Settings | Min KB attempts before "Talk to agent" | **3** | **1** (help text: "0 = always show") | **Changed** | OBSERVED (field value + API `min_kb_attempts_before_handoff = "1"`) |
| Settings | Support hours | Mon–Sun 06:00–24:00 PH | Mon–Sun 06:00–00:00, offset 480 min | Unchanged | OBSERVED |
| Settings | Sales hours | Mon–Sun 09:00–18:00 PH | Mon–Sun 09:00–18:00, offset 480 min | Unchanged | OBSERVED |
| Settings | Live-agent idle auto-close; warnings | 5 min; 3 and 1 min before | 5 min; 3 and 1 min before | Unchanged | OBSERVED |
| Settings | Queue abandon | 60 min | 60 min | Unchanged | OBSERVED |
| Settings | Agent response timeout | 5 min | 5 min | Unchanged | OBSERVED |
| Settings | CSAT after CS chat / Sales chat | Enabled / Disabled | Enabled / Disabled | Unchanged | OBSERVED |
| Settings | CSAT not sent after idle auto-close | Rule in settings text | Same text ("not sent for an idle-timeout auto-close") | Unchanged | OBSERVED |
| Settings | Upsell after confirmed bot resolution; category | Enabled; VAS #42 | Enabled; VAS #42 | Unchanged | OBSERVED |
| Settings | Save offer | Enabled; VAS #42 | Enabled; VAS #42 | Unchanged | OBSERVED |
| Settings | KB exclusion / Promotions category | Handling #43 / Promotions #44 | Same | Unchanged | OBSERVED |
| Settings | SDQ default service / category | Invalid (#68 not found, #1217 not in service) | Still invalid (same text) | Unchanged | OBSERVED |
| Settings | **Strict skill routing** | **Enabled** | **Disabled** (help text: falls back to any available agent by lowest load when no skill-matched agent is free) | **Changed** | OBSERVED (dropdown + screenshot) |
| Settings | Allow Manual End Chat (Agents / Supervisors) | Not recorded | Enabled / Enabled | Not in baseline | OBSERVED |
| Settings | Pre-chat fields | Support: name + mobile required; Sales: name + mobile + email required | Same | Unchanged | OBSERVED |
| Settings | Filipino: flow messages / button labels | ON / OFF | ON / OFF | Unchanged | OBSERVED |
| Settings | Notifications | Email on, SMS off | Email on, SMS off | Unchanged | OBSERVED |
| Settings | Wrap-up dispositions | 49 | 49 | Unchanged | OBSERVED |
| Settings | Live-agent retention | Not recorded | Live Agent Reports banner: "Retention: disabled. Closed engagements are kept indefinitely." The "Retention (days)" setting is not visible to this role | Not in baseline | OBSERVED |
| Org | Departments / Teams | Customer Service ("All"), Sales ("Multi-Channel Sales") | Same | Unchanged | OBSERVED |
| Org | Queue keys | `cs_general`, `sales_general` | Same (Ad Hoc distinct values) | Unchanged | OBSERVED |
| Org | Skill categories | 13 | 13, same names and order | Unchanged | OBSERVED |
| Org | Skill guidance — Sales | "New purchase, upgrade, subscribing" (§9.1) | "New application, or subscribing." | Changed (wording) | OBSERVED now; the baseline text may have been paraphrased, so the change itself is **INFERRED** |
| Org | Skill guidance — Promo | "Promos or discounts" (§9.1) | "Promos or discounts, upgrade, downgrade" | Changed (wording) | OBSERVED now; change **INFERRED** (same caveat) |
| Org | Landing Page Concerns (topic list) | 13 | 13, same names | Unchanged | OBSERVED |
| Org | Agent statuses | 14 | 14 | Unchanged | OBSERVED |
| Org | Greetings | 4 active | 4 active | Unchanged | OBSERVED |
| Channels | FB pages (Page Flow Assignment) | 5 (Buy Cignal → sales_bot; Cignal Play → cignalplay_menu; 3 test pages) | Same 5, same flows and start nodes | Unchanged | OBSERVED |
| Bots | Bots | `customer_support_bot`, `sales_bot` | Same, both active | Unchanged | OBSERVED |
| Access | Role menus | No Technical Settings, Audit Logs, Logs, Integrations, Identity Clients, Role Permissions, Widget docs, Live-Agent API docs | Same (API `menu-permissions/my`) | Unchanged | OBSERVED |

### B. Menu flow

| Area | Item | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| Flow | Published version | Main Support Flow v1, published 6 Jul 2026 1:25 PM | Same version (`published 2026-07-06T05:25:19Z`) | Unchanged | OBSERVED |
| Flow | Node count | 73 | 73 active (75 rows; `cignal_account_reconnection` and `cignal_account_reconnection_no` are inactive) | Unchanged | OBSERVED |
| Flow | Options (buttons) | 147 | 147 on active nodes (149 incl. inactive) | Unchanged | OBSERVED |
| Flow | Y/N resolution-check leaves | 30 | 30 | Unchanged | OBSERVED |
| Flow | "Main menu only" leaves; no-button leaf | 9; `cignal_billing_load_store` | 9; `cignal_billing_load_store` still has no buttons | Unchanged | OBSERVED |
| Flow | Article references | 31 (2583, 2536, 2827 …) | Same 31 IDs | Unchanged | OBSERVED |
| Flow | Account lookups | 4 lookup nodes + 1 account-type route | Same config | Unchanged | OBSERVED |
| Flow | Cignal / SatLite / Cignal Play trees | §3–4 | Same node keys | Unchanged | OBSERVED |
| Flow | Handoff nodes | 3 (Upgrade, Account Management, Missing GCash/Maya credit) | Same 3. Each node's config holds only `{"reason": …}`, with **no queue or skill target** | Unchanged | OBSERVED |
| Flow | **Target of Upgrade Plan handoff** | UNKNOWN | Node config: no target. Actual engagements joined by conversation + time window: **7 in `cs_general`, 1 in `sales_general`** (all-time) | Answered (open item) | OBSERVED data; queue choice by skill classifier **INFERRED** |
| Flow | **Target of Account Management handoff** | UNKNOWN | Node config: no target. Actual: **109 engagements, all `cs_general`** (110 handoff events) | Answered (open item) | OBSERVED data |
| Flow | Sales bot | 1 node, auto handoff `"Sales auto transfer to agent"`, target `webchat` | Same | Unchanged | OBSERVED |
| Flow | Fingerprint for the next delta | — | SHA-256 of all 75 node details = `d312ebc62a02ab4e…` (58,740 chars) | New reference | OBSERVED |

### C. Knowledge base

| Area | Item | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| KB | Indexed articles | ≈342 | **345** ("last rebuilt 10/5/2026, 12:23:24 PM (+25 new)") | Changed | OBSERVED |
| KB | Categories | 11 | 11, same names. Counts: Technical & Troubleshooting 63, Handling 60, VAS 57, Prepaid 37, Billing 36, Programming 28, SatLite 20, PLDT 16, References 12, Account Status 10, Promotions 6 (sum 345) | Unchanged (count) | OBSERVED |
| KB | Regression Test Set panel on KB Diagnostics | Not recorded | Present, 1 pinned question | Not in baseline | OBSERVED |
| KB | KB answer quality (last 24 h at check time) | — | 200 checks, 81.5% pass, 37 failed, 2 regenerated | Snapshot only | OBSERVED |
| KB | Greetings counted as KB hits | Yes | Still yes ("Hi" retrieves 2 articles; blank message retrieves 5 billing articles) | Unchanged | OBSERVED |

### D. Built-in dashboards and reports

| Area | Item | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| Nav | Page name | "Session Summaries" | **"Concern Summaries"** ("AI-generated summaries of completed concerns — a single chat session can raise more than one") | Changed (rename) | OBSERVED |
| Dashboard | Sessions card | "unique conversations/sessions", distinct session_id | "SESSIONS — unique chat threads" (1,248 for the 30-day window) | Changed (label only) | OBSERVED |
| Dashboard | **Concerns** card | — | "CONCERNS — topics worked to a conclusion" = count of summary rows (2,813 = sum of the 5 outcome rows) | **New** | OBSERVED; formula reconciled |
| Dashboard | Bot Resolution | 53.5% = ai_chat ÷ (ai_chat + ai_no_answer) | 54.5% "AI answered vs no-answer" = 869 ÷ 1,595 | Unchanged (formula) | OBSERVED reconciled |
| Dashboard | KB Hit Rate | kb_hit ÷ (kb_hit + kb_miss) | 99.0% = 1,761 ÷ 1,779 | Unchanged (formula) | OBSERVED reconciled |
| Dashboard | Handoffs | "Handoffs" = handoff **events** | Renamed **"HANDOFF REQUESTS — live-agent transfer requests"** (1,095 events; Analytics shows "88% of sessions", still mixed grain) | Changed (label) | OBSERVED |
| Dashboard | **Handoffs Completed** | — | "requests that reached an agent" = 1,018 = live engagements (Analytics: "93% of requests") | **New** | OBSERVED; 1,018 also equals total engagements and `handed_off` rows |
| Dashboard | Avg CSAT; % positive | Mean; ratings 4–5 ÷ responses | 4.3 / 5, 88 responses, "89% positive" (78 ÷ 88) | Unchanged (formula) | OBSERVED reconciled |
| Dashboard | Avg recommend score | 0–9 | 7.0 / 9 | Unchanged | OBSERVED |
| Dashboard | Resolution Outcomes | 4 statuses | **5**: Handed Off, Abandoned, Unresolved, Resolved, **Resolved Unconfirmed** (33) | Changed | OBSERVED |
| Dashboard | Daily buckets | UTC | Still UTC (`2026-10-06T00:00:00Z` bucket had only 31 sessions at 10:19 AM PH, consistent with a day that started at 08:00 PH) | Unchanged | OBSERVED value; bucket logic **INFERRED** |
| Analytics | Handoff reason labels | Raw codes | `user_requested` shown as "Subscriber-initiated (asked for agent)". **New event-level value `landing_apply_now`** (5 events, first seen 2 Oct PH) | Changed | OBSERVED |
| Analytics | CSAT "By resolution route" | rule_flow, ai_chat, live_agent | adds **`chatbot`** (4 responses) | Changed | OBSERVED |
| Analytics | Product Split note | Most traffic "Unattributed" | Note now says "Unattributed is traffic from before this breakdown was tracked" | Not in baseline | OBSERVED |
| Concern Summaries | Resolution filter | resolved, unresolved, handed_off, abandoned, Resolved (Unconfirmed) | Same values; `handed_off` now labeled **"Handoffs Completed"** | Changed (label) | OBSERVED |
| Concern Summaries | Sentiment filter / cards | positive, neutral, frustrated, escalated (0) | Same; Escalated still 0 | Unchanged | OBSERVED |
| Live Agent Reports | KPI set | Resolved Rate, Avg Wait, Avg Handle | Total Engagements 1,018 (1,001 closed), Resolved Rate 38.5%, Avg Wait 51 s, Avg Handle 17m 10s, Abandoned 8; By Queue (CS 909, Sales 109) | Unchanged (definitions) | OBSERVED |
| Live Agent Reports | Daily buckets | Not recorded | **Manila date**: Oct 1 = 233 and Oct 2 = 247 created, which match the Ad Hoc counts by Manila date of `queued_at` exactly | Not in baseline | OBSERVED |

### E. Status and value lists

| Area | Item | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| Summary | `resolution_status` | abandoned, handed_off, resolved, unresolved | + **`resolved_unconfirmed`**. All-time rows: handed_off 1,018, abandoned 664, unresolved 620, resolved 480, resolved_unconfirmed 33 | **New value** | OBSERVED (Ad Hoc distinct) |
| Summary | `resolved_unconfirmed` profile | — | 33 rows, all with a ticket, 33 distinct tickets, no handoff reason, avg 12–29 messages, Manila dates 3–5 Oct only, all channels incl. test | New | OBSERVED |
| Summary | `handoff_reason` | User requested live agent, human_only, upsell accepted, User agreed to live agent offer, user_stuck, Apply Now | Same 6 (810 / 154 / 40 / 3 / 4 / 7) | Unchanged | OBSERVED |
| Summary | `primary_intent` | 13 topics | Same 13 (PPV, Activation, Loading now have rows) | Unchanged | OBSERVED |
| Summary | `channel` | webchat, webchat:website, webchat:test, facebook_messenger | Same 4 | Unchanged | OBSERVED |
| Engagement | `state` | closed, abandoned (+ live states) | closed, active, abandoned | Unchanged | OBSERVED |
| Engagement | `tags` (close reason) | Not listed in §7 | `auto-closed-idle`, `customer-ended`, `auto-abandoned-queue`, blank | Not in baseline (column usable by name) | OBSERVED |
| Engagement | `event_type` | 11 values | Same 11 (message, idle_warning, disposition_updated, assigned, queued, monitored, closed, attachment, transferred, whisper, priority_changed) | Unchanged | OBSERVED |
| Event log | `handoff.extra` | user_requested, user_typed_request, user_agreed_to_offer | + **`landing_apply_now`** (first seen 2 Oct PH; on both bots) | New value | OBSERVED |
| CSAT | `resolved_by` | rule_flow, ai_chat, live_agent | + **`chatbot`** (4 responses, from 3 Oct PH; invitations from 1 Oct PH). Survey invitations also show `get_account_status` (1) | New value | OBSERVED |
| CSAT | Rating values | 1, 2, 4, 5 (no 3) | Still 1, 2, 4, 5 | Unchanged | OBSERVED |
| Disposition | Options | 49 | 49 | Unchanged | OBSERVED |

### F. Timestamps (most important)

| Source | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- |
| Console API `/reports/sessions` `created`, `transcriptTimestampsJson` | UTC +00:00 | UTC +00:00 (e.g., `2026-10-06T02:17:29+00:00` for a 10:17 AM PH row) | Unchanged | OBSERVED |
| Concern Summaries page display | Manila | Manila (browser renders the UTC value as 10:17 AM) | Unchanged | OBSERVED |
| Dashboard / Analytics day buckets | UTC | UTC | Unchanged | OBSERVED value / INFERRED logic |
| Live Agent Reports day buckets | Not recorded | Manila date | Not in baseline | OBSERVED |
| Ad Hoc `chatbot_session_summary.created` | UTC | UTC (`02:20:33+00:00` raw; `DATEADD(hour, 8, …)` gives `10:20:33`) | Unchanged | OBSERVED |
| Ad Hoc `engagement.queued_at / assigned_at / closed_at` | UTC | UTC +00:00 | Unchanged | OBSERVED |
| Ad Hoc `engagement.created_at / updated_at` | Not recorded | **+08:00** (Manila clock) | Not in baseline | OBSERVED |
| Ad Hoc `engagement_event.created_at` | "Already Manila" | Returned as **+08:00** (e.g., `10:17:28+08:00`), the same instant as `queued_at` `02:17:28+00:00` | Unchanged (now confirmed with offset) | OBSERVED (5 of 5 engagements) |
| Ad Hoc `chatbot_csat_response.created`, `chatbot_analytics_event.created` | UTC | UTC | Unchanged | OBSERVED |
| Ad Hoc `agent_status_history.started_at_ph / ended_at_ph` | — | Manila clock but labeled +00:00 | New table | OBSERVED |
| **Ad Hoc AI behavior** | AI used `created` as-is unless told | On `SELECT *` the AI now **renames `created` to `created_local` and converts it to Manila** by itself ("timestamp converted to Philippine local time"). When the prompt names columns and says `DATEADD(hour, 8, created)`, it does **not** add a second +8 | **Changed** | OBSERVED |

**H3 timestamp check (5 most recent engagements 1014–1018):** `DATEDIFF(minute, e.queued_at, ev.created_at) = 0` for all 5, because both are datetimeoffset values of the same instant. The clock values differ by exactly 8 hours (`02:17` vs `10:17`). After `DATEADD(hour, 8, e.queued_at)`, the DATEDIFF becomes **−480** (tested on engagements 971–975). **Conclusion: the +8 rule is NOT obsolete for `queued_at / assigned_at / closed_at / created`. Do not add +8 to `engagement_event.created_at`.** OBSERVED

### G. Ad Hoc schema and behavior

| Area | Item | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| Ad Hoc | Page note | — | "Personal data is masked; use the 👁 to reveal." API returns `piiColumns` (e.g., `sender_id`, `user_message`) | Not in baseline | OBSERVED |
| Ad Hoc | `chatbot_session_summary` columns | 13 listed | Same 13 (`conversation_id` works when named; `SELECT *` leaves it out and shows `created_local`) | Unchanged | OBSERVED |
| Ad Hoc | Two rows per handed-off ticket | Yes | Yes. Oct 1–5 PH: 964 `handed_off` tickets; the second row is `unresolved` 598, `resolved` 351, `abandoned` 1. The second row comes **12.5 min (resolved) / 21.2 min (unresolved) later on average**, not a few seconds | Unchanged (behavior); timing detail new | OBSERVED |
| Ad Hoc | `chatbot_csat_response.ticket_ref` | Absent | Still absent ("Invalid column name 'ticket_ref'") | Unchanged | OBSERVED |
| Ad Hoc | CSAT columns | 8 listed | 15: csat_response_id, channel, session_id, sender_id, conversation_id, bot_key, resolved_by, last_intent, created, comments, rating, recommend_score, ease_of_resolution, cso_satisfaction, resolved | Not in baseline (these match the Sep 30 beta list in project memory) | OBSERVED |
| Ad Hoc | `engagement` columns | 13 listed | Usable by name: + `tags`, `page_id`, `context_passed`, `created_at`, `updated_at`, `last_customer_message_at`, `last_agent_message_at`. One `SELECT *` run also showed `required_skill`, `ticket_ref`, `session_id`, `idle_warning_stage`, but the AI **rejects them by name** ("column does not exist") and later `SELECT *` runs left them out | Not in baseline / unstable | OBSERVED |
| Ad Hoc | KB table name | Unconfirmed | **`chatbot_article_usage`** (user_message, article_id, article_title, kb_source, channel, conversation_id, created) | Answered | OBSERVED (the AI named it; a TOP 1 query on it ran) |
| Ad Hoc | Event log table | Seen via probes | **`chatbot_analytics_event`** (event_id, event_type, channel, session_id, sender_id, user_message, node_key, result_count, extra, created, conversation_id, bot_key, correlation_id, duration_ms, threshold_ms, threshold_exceeded) | Answered | OBSERVED |
| Ad Hoc | **`chatbot_survey_invitation`** | — | survey_invitation_id, invitation_key, channel, session_id, sender_id, conversation_id, engagement_id, resolved_by, bot_key, offered_at, accepted_at, declined_at, completed_at, csat_response_id. All-time: offered 337, accepted 102, declined 213, completed 90; `engagement_id` always null | Not in baseline (data from 1 Oct PH) | OBSERVED |
| Ad Hoc | **`chatbot_customer_visit`** / **`chatbot_customer_identity`** | — | Visit: customer_key, channel, session_id, conversation_id, bot_key, page_id, customer_status (new / returning), identity_basis (account / channel_user), observed_at. Identity: customer_key, first/last seen at and channel | Not in baseline (data from 1 Oct PH) | OBSERVED |
| Ad Hoc | Other tables | agent_profile, chatbot_prechat_profile | + chatbot_dpa_consent, chatbot_feed_event, app_reviews, team, chatbot_messenger_page, agent_status_history, chatbot_health_sample, chatbot_facebook_service_event (FB service-level flags sl_1h…sl_12h) | Not in baseline | OBSERVED (each TOP 1 query ran) |
| Ad Hoc | Invented table | Rejected: "table not allowed" | Rejected: "The table 'chatbot_episode_fact' does not exist in the provided schema" | Changed (message) | OBSERVED |
| Ad Hoc | Query with no source table | — | Rejected: "rejected for safety (no source table)" | Not in baseline | OBSERVED |
| Ad Hoc | CTE | Rejected | A prompt with an explicit `WITH x AS (…)` **returned results**. The SQL is not shown, so the AI probably rewrote it as a plain SELECT | Changed (behavior) | OBSERVED result; mechanism **INFERRED** |
| Ad Hoc | Subqueries / OUTER APPLY | Worked | Worked (`OUTER APPLY` + correlated `MIN()` in WHERE). Aggregate over a subquery fails ("Cannot perform an aggregate function on an expression containing … a subquery") | Unchanged | OBSERVED |
| Ad Hoc | `INFORMATION_SCHEMA` | Blocked | untested | Cannot verify | — |

### H. Regression tests

| # | Test | Baseline | Now | Status | Evidence |
| --- | --- | --- | --- | --- | --- |
| H1 | Live-agent WebChat, Oct 1 (rebuilt; see note) | 182 rows, 177 with tickets, 0 duplicate tickets | **182 rows, 177 with tickets, 0 duplicate tickets** | Unchanged | OBSERVED |
| H2 | Chats Offered, Oct 1–5 | Never tested | Runs. Totals below | New result | OBSERVED |
| H3 | `queued_at` vs `queued` event | +8 rule assumed | Same instant; clock differs by 8 h; +8 rule still valid | Unchanged | OBSERVED |

**H1 grain and filters.** One row per conversation (`conversation_id`), taking the earliest engagement whose Manila date of `queued_at` is 1 Oct 2026. Channel `LIKE 'webchat%'` (includes `webchat:test`, as the 182 baseline did). Ticket = earliest summary ticket with `created >= queued_at − 2 min`. The breakdown is webchat 148 + webchat:website 26 + webchat:test 8 = 182 conversations. On a UTC day the same query would give 181, so the match also shows that no double-conversion happened. **Note:** the exact saved wording of this prompt is not in project memory, so it was rebuilt from Instructions §7 (rules 3, 4 and 6). See the Correction Notes for the prompt text.

**H2 Chats Offered.** Grain: distinct `engagement_id`. Date: Manila date of `queued_at`. "Answered" = `assigned_at` present. Test traffic is shown both ways.

| PH date | Queue | Offered | Offered excl. test | Answered | Answered excl. test | Queue abandoned |
| --- | --- | --- | --- | --- | --- | --- |
| Oct 1 | cs_general | 181 | 175 | 175 | 170 | 3 |
| Oct 1 | sales_general | 52 | 49 | 39 | 38 | 1 |
| Oct 2 | cs_general | 206 | 163 | 200 | 158 | 3 |
| Oct 2 | sales_general | 41 | 37 | 40 | 36 | 1 |
| Oct 3 | cs_general | 128 | 123 | 128 | 123 | 0 |
| Oct 3 | sales_general | 7 | 7 | 7 | 7 | 0 |
| Oct 4 | cs_general | 157 | 154 | 157 | 154 | 0 |
| Oct 4 | sales_general | 3 | 3 | 3 | 3 | 0 |
| Oct 5 | cs_general | 198 | 189 | 198 | 189 | 0 |
| Oct 5 | sales_general | 3 | 3 | 3 | 3 | 0 |
| **Total** | | **976** | **903** | **950** | **881** | **8** |

Observations: (1) Oct 2 had 43 test engagements in CS (206 vs 163). (2) Some offered chats were neither answered nor queue-abandoned (Oct 1 Sales: 52 − 39 − 1 = 12), which suggests the customer left the queue before assignment. **INFERRED** (3) **Sales queue volume fell sharply from 3 Oct** (52 → 41 → 7 → 3 → 3), while CS stayed steady. The cause is **UNKNOWN**. It could be a routing or classifier change, or a real drop in demand. Ask IT. The Live Agent Reports daily totals (233 and 247 for Oct 1–2) match these Manila-date counts.

**Engagement close reasons, Oct 1–5 PH (grain: engagement, `tags`).** Closed 968: `auto-closed-idle` 517 (53%, `resolved = 0` in every case), `customer-ended` 242 (`resolved = 1` in every case), blank (agent-ended) 209 (125 resolved). Queue-abandoned 8. **OBSERVED**. The engagement `resolved` flag is therefore driven by how the chat closed. Every idle auto-close counts as "not resolved", and every customer-ended chat counts as "resolved". **INFERRED**

---

## 3. Definitions affected (Instructions §8)

| §8 definition | Still correct? | What to change |
| --- | --- | --- |
| Abandoned (bot) | Yes | No change. The 30-min timeout is unchanged. |
| Confirmed resolved but CSAT unfinished | Partly | New evidence: `chatbot_survey_invitation` shows accepted 102 vs completed 90, and every CSAT row has rating, recommend, ease and resolved filled in. So partially answered surveys are **not saved as CSAT responses**, but the invitation row records accepted-without-completed. **INFERRED** from counts |
| Queue abandoned | Yes | You can now also identify it with `engagement.tags = 'auto-abandoned-queue'` (8 all-time). |
| Chats Offered | Yes, tested | Add: "tested 6 Oct; Oct 1–5 = 976 offered (903 excl. test), 950 answered". Add the "left queue before assignment" case. |
| CSAT | Formula unchanged | Add **response rate** = `completed_at` count ÷ `offered_at` count from `chatbot_survey_invitation` (all-time 90 ÷ 337 = 26.7%, test included). Add the `resolved_by = 'chatbot'` value. |
| NPS-like 0–9 | Yes | No change. |
| Routing problems | **Needs edit** | With strict skill routing **Disabled**, a chat can be auto-assigned to an agent without the matching skill whenever no skill-matched agent is free. That is designed fallback, not misrouting. Queue choice (CS vs Sales) is unaffected. |
| Sales path | **Needs edit** | Upgrade Plan handoffs land in `cs_general` (7 of 8). Account Management handoffs land in `cs_general` (109 of 109). Neither node has a target in its config. |
| Built-in dashboard caution | **Needs edit** | "Handoffs" is now labeled "Handoff Requests" (still events). New "Handoffs Completed" = engagements. New "Concerns" = summary rows (segment grain, not volume). The Bot Resolution and KB Hit formulas are unchanged. |
| Standard exclusions (SX) | Yes | No change. Note that `resolved_unconfirmed` also occurs on `webchat:test`. |
| Bot resolved (implicit in §8 and discovery §14) | **Needs a decision** | `resolved_unconfirmed` (33 rows) is a new bot outcome. Decide whether it counts in Bot Resolved, Containment or neither. Suggested: report it as its own line until IT defines it. **PROPOSED** |
| Agent resolution | **Needs a note** | `engagement.resolved` = 0 for all `auto-closed-idle` and 1 for all `customer-ended`. Report Agent Resolution with the close-reason split (`tags`). |

---

## 4. Prompts affected

| Prompt | Breaks / different numbers? | Exact fix |
| --- | --- | --- |
| RAW_Sessions (saved 1 Oct) | Runs. `Ticket Status = resolution_status` can now return **`resolved_unconfirmed`**, which the v6.1 helpers and branch logic do not map | In the template, add `resolved_unconfirmed` to the status mapping (own bucket "Resolved (Unconfirmed)", or under Resolved with a flag). The prompt text itself needs no change |
| RAW_CSAT | Runs. New `resolved_by = 'chatbot'` (4 responses) | Where C8 "Resolved by Bot (AI + Rule Flow)" counts `ai_chat` + `rule_flow`, change the condition to `resolved_by IN ('ai_chat', 'rule_flow', 'chatbot')` |
| RAW_LiveAgents / Live-agent WebChat | Same numbers (H1 182 / 177 / 0) | No change. If you add a DATEDIFF between an engagement column and an `engagement_event` time, compare **raw to raw** (do not add +8 first). Otherwise the result is off by −480 min |
| Any prompt using `SELECT *` or a vague time column | Can return `created_local` (already Manila) | Always name columns explicitly with `DATEADD(hour, 8, created)`. Never add +8 again in Excel to a column named `created_local` |
| Prompts naming `engagement.ticket_ref` / `required_skill` | Fail ("column does not exist") | Keep the time-window ticket pick (Instructions §7 rule 6) |
| Chats Offered (new) | Works | Use the prompt in the Correction Notes, §C-3 |
| Any Messenger/Sales split | Sales queue volume dropped from 3 Oct | No prompt fix. Flag it in the report and ask IT |

---

## 5. Open items: answered or still open

| Open item (Instructions §9 / discovery §24) | Status | Evidence |
| --- | --- | --- |
| Queue of Account Management / Upgrade handoffs | **Answered (data)**: `cs_general` (AM 109/109; Upgrade 7 of 8, 1 in `sales_general`). No target in the node config | OBSERVED join; classifier routing INFERRED |
| KB table name | **Answered:** `chatbot_article_usage` | OBSERVED |
| Partial CSAT stored? | **Mostly answered:** not stored as a CSAT response. Accepted-not-completed is visible in `chatbot_survey_invitation` | OBSERVED counts; conclusion INFERRED |
| Survey offered / declined stored? (§24 Q30) | **Answered:** yes, in `chatbot_survey_invitation` | OBSERVED |
| Timestamps now Manila? (§24 Q7) | **Answered: no**, for `created`, `queued_at`, `assigned_at`, `closed_at` and the console API. `engagement_event.created_at` and `engagement.created_at / updated_at` are +08:00 | OBSERVED |
| Repeat users (§24 Q6) | **Partly answered:** `chatbot_customer_visit.customer_status` (new / returning) with `identity_basis` (account / channel_user) | OBSERVED |
| Data retention (§24 Q32) | **Partly answered:** live-agent engagements are kept indefinitely (retention disabled). Summaries, events and transcripts: open | OBSERVED |
| Engagement ↔ ticket link (§24 Q28) | Still open. `engagement.ticket_ref` appeared once in `SELECT *` (null on engagement #1) and cannot be queried by name | OBSERVED / UNKNOWN |
| "Resolved (Unconfirmed)" meaning (§24 Q10) | Still open, but now has data: 33 rows from 3 Oct PH, all ticketed, no handoff reason. It may be related to the new CSAT `resolved_by = 'chatbot'` (same start date) | OBSERVED / INFERRED link |
| `human_only`, `user_stuck` | Still open (154 and 4 rows) | OBSERVED counts |
| What starts a new Conversation ID | Still open | — |
| Product attribution (§24 Q31) | Partly answered: "Unattributed is traffic from before this breakdown was tracked" | OBSERVED note |
| Bot vs agent segment flag (§24 Q4) | Still open. No new column | OBSERVED absence |
| When the two settings changed | **UNKNOWN**. Audit Logs are not accessible to this role | — |

---

## 6. Not verified

| Item | Why |
| --- | --- |
| Technical Settings, Audit Logs, Logs, Integrations, Identity Clients, Role Permissions, Widget / API docs | Not in this role's allowed menus (unchanged). Not opened |
| Change dates of the two settings | No audit log access |
| Customer Journey, Supervisor Dashboard, Realtime Dashboard, QA Reviews, Agent Workspace | Not re-opened in this delta check (untested) |
| DPA Consent, Email/SMS Log, Feed Events, App Reviews, Feed Response Actions, Agent Profiles | Not re-opened (untested) |
| Canned responses (baseline 8) | Page loaded but the list was not read reliably (untested) |
| Landing Page Concern guidance text | Shown only in the Edit form. Not opened, to avoid edit screens |
| Node message text changes | Baseline has no text-level copy to diff against. A fingerprint is recorded for the next check |
| Concern Summaries filtered to "Resolved (Unconfirmed)" | The list stayed on "Loading…". The values were taken from Ad Hoc instead |
| `INFORMATION_SCHEMA`, "unreadable response" on long prompts, AM/PM sort quirk | untested |
| The table list | Comes from the AI's own description plus one TOP 1 query per table, not from a schema view. `required_skill`, `ticket_ref` and `idle_warning_stage` on `engagement` are unstable |
| Customer-side behavior of KB attempts = 1 | Would require starting a chat (not allowed) |
