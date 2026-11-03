# Claude Code agents for the Linggo LLMs web apps

The master copies of two Claude Code subagents. They run inside each app
repo (`linggollms-aphasia-webapp` and `linggollms-asd-idd-webapp`), in
`.claude/agents/`. Claude Code reads the code, drives the live beta in a
browser with Playwright, and writes reports and tutorials into the repo.

| Agent | What it does | Where it writes | When it stops |
| --- | --- | --- | --- |
| `linggo-user-tester` | Tests the beta as a learner, companion or coach, in stages 0 to 6 | `docs/testing/` | After every stage |
| `linggo-tutorial-writer` | Does a task in the beta and writes a step-by-step tutorial, then other languages, Polly audio, captions and video | `docs/tutorials/` | After the English draft, and again before audio and video |

Change the agents here first, then copy them to both app repos, so the two
stay the same.

## Install (in each app repo)

1. Copy `linggo-user-tester.md` and `linggo-tutorial-writer.md` into
   `.claude/agents/`.
2. Add `docs/testing-context.md`: the product, the beta address, the test
   accounts (environment variable names only, never passwords), made-up
   details to type in, the feature list and the Polly voice settings. Both
   agents read it first.
3. Set the test account environment variables where Claude Code runs (on
   the Mac, or in the Claude Code environment's settings for web sessions).
4. Playwright is **not** added to the app's `package.json`: changing it
   would change the lockfile, which web sessions must not do. The agents use
   Playwright if it is already there (it is in Claude Code web sessions), or
   install it in `~/.cache/linggo-testing`, outside the repo.
5. Commit on a branch and open a pull request, as with every change.

## Use

Open Claude Code in the app repo and say, for example:

- "Use the linggo-user-tester agent. Stage 0, then stage 1 as the parent, on a phone."
- "Use the linggo-user-tester agent. Stage 5, Arabic and Mandarin, keyboard only."
- "Use the linggo-user-tester agent. Stage 6 for the three P0s from yesterday's report."
- "Use the linggo-tutorial-writer agent. Task: add a new word to My Words. English first."
- "Use the linggo-tutorial-writer agent. Make the audio and video for the five tutorials I approved, all five languages."

### The tester's stages

| Stage | What |
| --- | --- |
| 0 | Get ready: read the context, check the browser, sign in to each test account, write a plan. No testing. |
| 1 | First time, start to finish, as one person (sign in, setup, first sentence, Dashboard, lock). |
| 2 | Every feature in the feature list, one by one. |
| 3 | Voices, Stop and Undo, speaking speed, no internet, slow internet. |
| 4 | Accessibility: axe (WCAG 2.1 AA) and checks by hand (keyboard, focus, labels, tap size, zoom, themes). |
| 5 | Languages (Arabic right to left) and ways of using it (keyboard only, switch, large text). |
| 6 | Retest named findings: Fixed, Still there or Changed. |

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
