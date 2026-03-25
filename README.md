# job-search-skills
Open sourcing the materials and skills I've used in my job search.

I've been lucky to take time off over the last year to recover from a difficult pregnancy and birth and watch my daughter grow up. When I started my job search, I had to reckon with a few things I hadn't fully processed: how do I take everything I love about my career and everything I love about my new life and figure out how they fit together? And how do I run an efficient job search with 16 hours of childcare each week?

That turned into a journey of both intellectual discernment and hands-on building, and I wanted to share it for others who may be in a similar spot. (It's 2026, so yes — I'm going to tell you a lot about how I used AI to get here.)

## Starting with what I actually want

The discernment work started with a job search charter — a tool from [*Never Search Alone* by Phyl Terry](https://www.neversearchalone.org/). It's a living document that forces you to get specific about your goals, what you love doing, what you won't tolerate, and what a company needs to look like for you to thrive. **I'm including [my version](job-search-charter-meg) and the [template](job-search-charter-template) in this repo so anyone can fill out their own.**

I'd created a version of this charter in 2023, so I uploaded that into a Claude project alongside work artifacts from the last three years — strategy docs, peer feedback, performance reviews, and Slack messages from moments that mattered. I prompted Claude to highlight overlaps with my existing charter and flag skills or weaknesses I hadn't identified.

Then came the harder part. I uploaded notes I'd taken while pregnant — conversations with friends and women executives about what came next — and asked the AI to push me on how I really want work to fit into my life now. The result was surprisingly effective. I've spent hours talking about working motherhood with friends, and what comes up in those conversations is a lot of empathy and camaraderie — which is wonderful, but it doesn't force decisions. What I got from the AI was cold, hard trade-offs: *If your daughter gets sick the day before a product launch, what would you want your job to allow you to do without guilt or career penalty?*

I came out of this exercise with a charter that I was genuinely excited about and rebuilt from the ground up for the life I'm living now. Next, I prompted Claude to help me develop a few more screening tools:

- Two **role archetypes** that I was genuinely excited about and helped me get specific about what I was targeting.
- A series of **screening questions for interviews** to make sure the opportunities I was targeting were real fits with the areas of my charter I care most about.
- A few variations of an **elevator pitch** to talk about my experience and what I care most about in a new opportunity. This has dramatically cut down my prep time!

## Turning the charter into a scoring system

From there, I turned the charter into a rubric: five scored dimensions (role level, lifestyle fit, company stage, domain fit, culture signals), each worth up to two points, plus hard filters that immediately disqualify roles before scoring. I hand Claude a job posting link, and it returns a per-dimension score, a total out of ten, and a quick verdict. Eight or above, I apply. Below that, I move on.

**You can find the [rubric](rubric.md) in this repo.**

What surprised me was where the high scores showed up — B2B SaaS and fintech companies I wouldn't have taken seriously on a quick scroll, but whose problem spaces were a strong match for my experience. The rubric forced me to evaluate fit on my actual criteria rather than gut-level pattern matching. The low scores were just as useful: roles at companies I genuinely admire that scored poorly because the job descriptions signaled a rockstar-ninja-hustle culture that isn't compatible with parenthood. Without the rubric, I might have applied anyway out of brand affinity.

## Building the machine

Claude lets you create "skills" — custom instructions for specific, repeatable tasks. (If you haven't built one before, [here's a reference](https://code.claude.com/docs/en/skills)!) I built two.

### The job search skill

**→ [`job-search-skill-public.md`](job-search-skill-public.md)**

This skill automates finding, filtering, and scoring postings. It searches across crypto, fintech, adtech, martech, and data platforms, then filters to only direct ATS pages (Lever, Greenhouse, Ashby) — because aggregators keep dead postings live. It applies my hard filters, scores what's left against the rubric, and maintains an exclusion list so I never see the same posting twice. I set up a cron job that runs it every morning and sends results via a Telegram bot. I review them over breakfast and decide which ones are worth pursuing. The whole cycle takes about fifteen minutes.

### The resume editing skill

**→ [`resume-edit-public.md`](resume-edit-public.md)**

This skill handles tailoring. I pass it a job description link, and it clones my master resume, renames the copy with the company name, and edits bullets to emphasize the right scope, metrics, and domain language. Early versions hallucinated details about my experience — that's gotten dramatically better with feedback cycles. It turns a twenty-minute task into a five-minute review.

### Iteration notes

Neither skill started polished. The search skill needed several iterations: restricting sources to ATS pages, switching from native web crawling to [Exa](https://exa.ai/docs/reference/exa-mcp), adding hard exclusion filters for industries I'd never consider, and building the deduplication list. Like any good product, it improved after a few rounds of "well, that could have been better."

## What it's added up to

I'm still mid-search, but I'm happy with what the process has produced. I've juggled a much higher volume of applications than I could have otherwise — and more importantly, the right ones. The skills and automations are helping me stay more honest and true to my criteria than I have in the past.

One thing I want to be clear about: AI can automate your grunt work and help you keep yourself honest, but it can't replace human connections. Through my last two searches, I've had a regular meeting with a small group of friends job-seeking in similar industries, and I can't recommend that highly enough. I’ve also been incredibly grateful to friends who have introduced me to opportunities. AI could go to zero tomorrow and the humans would still be the most valuable part of any job search.

## What's in this repo

| File | Description |
|------|-------------|
| [`job-search-charter-meg`](job-search-charter-meg) | My 2026 job search charter |
| [`job-search-charter-template`](job-search-charter-template) | Blank template — fill out your own |
| [`rubric.md`](rubric.md) | Scoring rubric with hard filters |
| [`job-search-skill-public.md`](job-search-skill-public.md) | Claude skill for automated job searching |
| [`resume-edit-public.md`](resume-edit-public.md) | Claude skill for resume tailoring |

## Setup

If you want to adapt these skills for your own search:

1. **Start with the charter.** The automation is only as good as the criteria behind it. Use the template to define what you actually want before building anything. I've added mine as a reference for a finished product. I included a potential prompt to get you started.
2. **Create your rubric.** Translate your charter into scored dimensions and hard filters. I included a prompt to get you started. The rubric in this repo is mine — yours will look different.
3. **Set up the skills in Claude Code.** Copy the SKILL.md files and update them with your rubric, target domains, and filters.
4. **Optional: Add a cron job + Telegram bot.** If you want daily results delivered to your phone, [Telegram's bot tutorial](https://core.telegram.org/bots/tutorial) covers setup in minutes. You can set up a cron job in Claude Code.

---

Take a look, adapt what works for you, and let me know what you think!
