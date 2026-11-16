# The Weekly Work Recap

An AI agent that reads your time-tracking data every Friday afternoon and writes your week back to you, per client: what you actually did, which tools you did it in, how many hours against that client's weekly cap, and who it was for.

It never tracks for you, never decides what mattered, and never talks to a client. It reads, adds up, and writes one message to you. You read it, adjust what it missed, and make your plans.

> "Manual tracking was so difficult because I kept on getting into a workflow and just getting into a zone. I would just get so excited about work, I wouldn't start the timer. I just wanted that to be one less thing I was doing that wasn't the work of work."
>
> Ada Wogu, Fractional COO, [The Optimized CEO](https://www.theoptimized.ceo)

## What it does

Every Friday at 3:18 pm:

1. Pulls the week (Monday to Friday) from [Timing](https://timingapp.com): your logged tasks per project, plus the auto-tracked screen time that shows which apps and files carried each project.
2. Reads each project's weekly cap straight out of the project name (`Client A (7h cap)`), so the rules live in your own tracker, not in the agent.
3. Writes one paragraph per client: what got done, how, hours against the cap.
4. If you have filled in a 90-day plan for a client, reads the week against it: which milestones moved, which are due in the next two weeks, which are past due and untouched. Until a plan exists it says nothing about plans at all.
5. Sends you one Slack DM and stores the week as a record you can look back on.

## What stays yours

- Deciding what actually mattered, and anything that is bad news.
- What to escalate, and what to say to the client.
- The plans. The agent reads them; it never writes them.

## What it will never do

- Invent a task, a client, a number or an hour. Every figure comes from a Timing call in that run.
- Send an email, post anywhere, or write to a spreadsheet.
- Comment on whether you worked enough, or on your rest.

## Set it up (five steps)

### 1. Name your Timing projects with the cap in the title

The agent parses the hours out of the project name. Nothing else is configured anywhere.

```
Client A (7h cap)
Client B (4h cap)
My business (15h weekly)
Writing (10h weekly)
```

Projects without a cap in the name (Web Browsing, Lunch, Not Working, and so on) are ignored as non-client time.

### 2. Decide where the recap lands

The prompt sends one Slack DM. You need your own Slack member ID and the channel ID of your DM with yourself (open the DM, the URL ends in `/D0…`). If you would rather get it by email or as a note, change step 8 of the prompt to match. Keep it to one destination.

### 3. Create the scheduled agent

In Claude Code, open **Scheduled tasks** (claude.ai/code/routines) and create a new task. Paste [`PROMPT.md`](PROMPT.md) as the prompt after filling in the `[BRACKETED]` placeholders. Schedule: Fridays, late afternoon, in your timezone. Connect the **Timing** and **Slack** connectors.

If you do not want the weeks stored anywhere, delete steps 5 and 7 from the prompt. The recap still arrives in Slack; you just lose the 90-day plan reading.

### 4. Run it once by hand

Hit **Run now** on the task and read what comes back. The first run usually shows you where your project names or caps are messy. Fix them in Timing, not in the prompt.

### 5. Add a 90-day plan when you have one

Copy [`work_plans.example.json`](work_plans.example.json), fill in a client's goal and milestones, and save it where step 5 of the prompt reads from. The next Friday's recap reads the week against it.

## Files

| File | What it is |
| --- | --- |
| `PROMPT.md` | The agent's instructions, with placeholders for your details |
| `work_plans.example.json` | The shape of a per-client 90-day plan the agent can read |
| `recap.example.md` | What a Friday message looks like (placeholder names and numbers) |

## Shared as-is

This is the agent exactly as it runs in one Fractional COO practice, with names taken out. No support, no roadmap. Fork it and make it yours.

Built by Ada Wogu at The Optimized CEO. I build the backend your business actually runs on.
