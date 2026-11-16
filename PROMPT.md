# Weekly Work Recap: agent prompt

Fill in every `[BRACKETED]` value, then paste the whole thing as the prompt of a scheduled task in Claude Code. Schedule it for Friday afternoon in your timezone.

---

Write [YOUR FIRST NAME] a weekly work recap from Timing: what they did this week, how they did it, and who they did it for, per client. [YOUR FULL NAME] runs [YOUR BUSINESS] ([YOUR WEBSITE]), a [WHAT YOU DO] in [YOUR CITY]. This is the early version of an agent that will later read the week against 90-day plans and project timelines; build the week honestly now and leave the plan framing off until a plan exists.

HARD RULES: Read-only on Timing. NEVER send an email, never post anything, never write to any Google Sheet, never sign in or out, never enter a password. Never invent a task, a client, a number or an hour. Every figure must come from a Timing call this run. The only message allowed is ONE Slack DM to [YOUR FIRST NAME] at channel_id [YOUR SLACK DM CHANNEL ID]. Only write to the "work_weeks" collection and the "work_plans" settings doc described below. If Timing can't be reached, send one short Slack line saying so and stop.

Storage: [WHERE THE WEEKS ARE STORED, e.g. an artifact URL with a database, or "nowhere: skip steps 5 and 7"].

1. Dates: bash `TZ=[YOUR IANA TIMEZONE] date +%F` (today, a Friday = F) and the Monday of this week (M). The week is M through F inclusive.

2. WHAT THEY DID. Timing get_report with start_date_min M, start_date_max F, columns ["project","title"], include_child_projects true, max_lines 400. These are manually logged time entries: the task titles are what was actually done and the project is who it was for. Also call get_report with columns ["project"] and include_app_usage true for the same range, to get per-project totals including auto-tracked screen time.

3. CAPS. Client and work projects carry their weekly cap in the title, e.g. "Client A (7h cap)", "Client B (4h cap)", "My business (15h weekly)", "Writing (10h weekly)". Parse the number of hours out of each title with python. Projects with no cap in the title (Web Browsing, Activity Categories, Not Working, Lunch, Tracking Time, Concurrent Working and their children) are NOT client work: leave them out of the client section entirely. Compute, per capped project: hours logged, the cap, and hours over or under. Never guess a cap that isn't in the title.

4. HOW THEY DID IT. Timing get_activity_hierarchy for the same range (their own screen time: apps, files, URLs). For each capped project, pick the 3-5 apps or tools that carried the most time and name them plainly ("mostly in ClickUp and Gmail", "Canva and Flodesk"). This is the "how", and it only comes from this call, never from what you assume they use. If the hierarchy is empty or a Mac hasn't synced, say "screen time hasn't synced for part of this week" rather than filling the gap.

5. PLAN FRAME (stays off until a plan exists). Read the settings doc "work_plans". If it doesn't exist, create it once as {"note":"Per-client 90-day plans and project timelines. [YOUR FIRST NAME] fills these. While a client's entry is empty, the weekly recap says nothing about plans for them.","plans":{},"updated":"<today>"} and skip the rest of this step. If it exists, then for each capped project that has a non-empty entry in "plans" (shape: {"client":"...","goal":"...","milestones":[{"name":"...","due":"YYYY-MM-DD","done":false}]}), add to that client's paragraph: which milestones this week's work moved toward, which are due in the next 14 days, and which are past due and untouched. Judge "moved toward" only from the task titles in step 2, and say so plainly when the week's work doesn't map to any milestone; that is useful information, not a failure. Never invent a milestone, a goal or a due date, and never write into "plans" yourself.

6. WRITE THE RECAP. One paragraph per capped project with work in it. Plain, specific, no filler, no em dashes, no "it's not X, it's Y". Each paragraph: what got done (the real task titles, grouped into sensible chunks, not a list of every entry), how (the tools from step 4), hours against the cap, and the plan frame if step 5 produced one. Then one short line across the whole week: total client hours, total business hours, and the single thing that ate the most time. Do not editorialise about whether they worked enough, and never comment on rest, sleep or the "Not Working" project.

7. STORE IT. Save to collection "work_weeks", doc id the Monday date (M): {week_start: M, week_end: F, generated: <now ISO>, clients: [{project, hours, cap, over_under, did: <the paragraph>, tools: [..], titles: [..]}], totals: {client_hours, business_hours, writing_hours}, plan_frame: <true if step 5 produced any>}. If the doc exists, overwrite it.

8. SLACK. Confirm with slack_read_user_profile that the workspace is "[YOUR SLACK WORKSPACE NAME]" ([YOUR SLACK MEMBER ID]); if not, skip Slack. ONE DM to channel_id [YOUR SLACK DM CHANNEL ID]:
"*Your week · <Mon D> to <Mon D>*"
then, per client, "*<client name>* · <h>h of <cap>h" and the paragraph underneath.
then "*The week* · <total client>h client, <business>h business, <writing>h writing. Most of it went to <the biggest single thing>."
If no 90-day plans are set yet, end with one line: "No 90-day plans set, so this is just the week. Fill work_plans when you want it read against a timeline."
If a client logged no hours at all this week, give them one line ("Nothing logged this week") rather than a paragraph.

9. End with a one-line summary.
