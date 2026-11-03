# Claude Code agents for the Linggo LLMs web apps

The master copies of two Claude Code subagents. They run inside each app
repo (`linggollms-aphasia-webapp` and `linggollms-asd-idd-webapp`), in
`.claude/agents/`. Claude Code reads the code, drives the live beta in a
browser with Playwright, and writes reports and tutorials into the repo.

| Agent | What it does | Where it writes | When it stops |
| --- | --- | --- | --- |
| `linggo-user-tester` | Tests the beta as the learner, or the family member who sets it up, in stages 0 to 6 | `docs/testing/` | After every stage |
| `linggo-tutorial-writer` | Does a task in the beta and writes a step-by-step tutorial, then other languages, Polly audio, captions and video | `docs/tutorials/` | After the English draft, and again before audio and video |

Change the agents here first, then copy them to both app repos, so the two
stay the same.

## Install (in each app repo)

1. Copy `linggo-user-tester.md` and `linggo-tutorial-writer.md` into
   `.claude/agents/`.
2. Add `docs/testing-context.md`: which Linggo LLMs app it is, the beta
   address, who uses the app today, the test account (environment variable
   names only, never passwords), the tester personas, what matters most for
   accessibility in this app, the feature list and the Polly voice
   settings. Both agents read it first.
3. Set the test account environment variables where Claude Code runs (on
   the Mac, or in the Claude Code environment's settings for web sessions).
4. Playwright is **not** added to the app's `package.json`: changing it
   would change the lockfile, which web sessions must not do. The agents use
   Playwright if it is already there (it is in Claude Code web sessions), or
   install it in `~/.cache/linggo-testing`, outside the repo.
5. Commit on a branch and open a pull request, as with every change.

## Use

Open Claude Code in the app repo and say, for example:

**Linggo LLMs Aphasia** (`linggollms-aphasia-webapp`)

1. "Use the linggo-user-tester agent. Stage 0, then stage 1 as Alex (AP-2), on his phone."
2. "Use the linggo-user-tester agent. Stage 2 as Sam (AP-1), left hand only."
3. "Use the linggo-user-tester agent. Stage 4, the Linggo LLMs accessibility test pass."
4. "Use the linggo-user-tester agent. Stage 5 as Karim (AP-4, Arabic) and Wei (AP-5, Mandarin), keyboard only."
5. "Use the linggo-user-tester agent. Stage 6 for P0-1, P0-2 and P0-3 from yesterday's report."
6. "Use the linggo-tutorial-writer agent. Task: add a new word to My Words, as Alex (AP-2). English first."
7. "Use the linggo-tutorial-writer agent. Make the audio and video for the five tutorials I approved, all five languages."

**Linggo LLMs ASD/IDD** (`linggollms-asd-idd-webapp`)

1. "Use the linggo-user-tester agent. Stage 0, then stage 1 as Sofia (KP-2), on her phone."
2. "Use the linggo-user-tester agent. Stage 2 as Maya (KP-1), pictures only."
3. "Use the linggo-user-tester agent. Stage 4, the Linggo LLMs accessibility test pass."
4. "Use the linggo-user-tester agent. Stage 5 as Omar (KP-3, Arabic, keyboard only) and Grandma Li (KP-5, Mandarin)."
5. "Use the linggo-user-tester agent. Stage 6 for P0-1, P0-2 and P0-3 from yesterday's report."
6. "Use the linggo-tutorial-writer agent. Task: add a new word to My Words, as Sofia (KP-2). English first."
7. "Use the linggo-tutorial-writer agent. Make the audio and video for the five tutorials I approved, all five languages."

"As the parent" or "as the learner" also works: each app's
`docs/testing-context.md` maps the role to a persona.

### The tester's stages

| Stage | What |
| --- | --- |
| 0 | Get ready: read the context, check the browser, sign in to each test account, write a plan. No testing. |
| 1 | First time, start to finish, as one person (sign in, setup, first sentence, Dashboard, lock). |
| 2 | Every feature in the feature list, one by one. |
| 3 | Voices, Stop and Undo, speaking speed, no internet, slow internet. |
| 4 | Accessibility: the Linggo LLMs accessibility test pass (WCAG 2.2 AA, brand rules, AAC needs), plus the app's own list in `docs/testing-context.md`. Ends with a Good / Needs work / Blocking rating and rows ready for the Accessibility Audit Results database. |
| 5 | Languages (Arabic right to left) and ways of using it (keyboard only, switch, large text). |
| 6 | Retest named findings: Fixed, Still there or Changed. |

There is no Companion app or Coach app yet: the learner and the family
member or carer setting up share one account (the family member uses the
Dashboard, behind the PIN). The agents test only who each repo's
`docs/testing-context.md` lists, so add companion and coach accounts there
once those apps exist.

Each agent works on **one app per run**: the Aphasia app in
`linggollms-aphasia-webapp`, the ASD/IDD app in `linggollms-asd-idd-webapp`.
What differs between the apps (beta address, personas, features, voices,
what matters most for accessibility) lives in that repo's
`docs/testing-context.md`, so the agent files stay the same in both.

Stage 4 and the tutorial checks follow the Notion page "Run the Linggo LLMs
accessibility test pass" (Company Brain). If that page changes, update
stage 4 of `linggo-user-tester.md` and the accessibility checks in
`linggo-tutorial-writer.md` here, then copy them to both app repos.

Findings are rated P0 to P3 and numbered (P0-1, P1-2…) so a later stage 6
or a developer can point at them.

## What neither agent will do

- Change app code or data. Testing and writing only. Fixes are a separate
  task for a developer agent, with the report attached.
- Use real accounts or real people's data, or open the live app families
  use. Test accounts and the beta only.
- Describe anything it has not done in the live app during that run.
- Tick "I agree to the beta", create accounts, or put a password, PIN or
  email into a report, screenshot or commit.

## Audio needs AWS

The tutorial writer makes narration with Amazon Polly through the AWS
command line, using the app's own voices. Web sessions have no AWS access,
so audio and video are made from a Mac with AWS access; in a web session the
agent stops with the scripts ready and the command to run.
