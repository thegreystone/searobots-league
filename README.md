# SeaRobots Leagues

Welcome to the home of the **SeaRobots Open League**
and the **SeaRobots AI League**!

- **Simulator & 3D Viewer:** [thegreystone/searobots](https://github.com/thegreystone/searobots)
- **Starter Submarine Template:** [thegreystone/searobots-template](https://github.com/thegreystone/searobots-template)
- **Results, Brackets, Showreels & Replays:** [searobots.hirt.se](https://searobots.hirt.se)
- **Official Rules:** [RULES.md](RULES.md)

---

## The Leagues

| League | Schedule | Match format | Entries |
|---|---|---|---|
| **[Open League](open-league/)** | Monthly — draw on the **1st of every month**, then rounds with 48-hour viewing periods | Best-of-17 | Open to community submissions; one submarine per person per season |
| **[AI League](ai-league/)** | Monthly — draw on the **1st of every month**, then rounds with 48-hour viewing periods | Best-of-17 | Closed; the maintainer's unguided hill-climbing submarines |

### Open League

The Open League runs monthly and is open to community submissions. You can open a
registration PR at any time once the upcoming month's folder exists — automated CI checks
run immediately so you can iterate on your PR branch freely. Maintainer review and merges
happen during the **last week of the month** (`reviewWindowStartUtc` through
`registrationDeadlineUtc`, typically the final 7 days through `23:59:59 UTC` on the last
day of the preceding month). To guarantee review before the freeze, make sure your PR is
open and passing CI at least **24 hours before `registrationDeadlineUtc`**; last-minute
PRs are reviewed on a best-effort basis. The exact dates and pinned SeaRobots version are
recorded in `open-league/<YEAR>/<MONTH>/season.json`.

Each season includes the official built-in **Claude**, **Codex**, and **Gemini** house
submarines alongside community entries. The one-submarine limit is enforced per season
via your GitHub account ID (no email addresses or personal data are stored in this repository).

### AI League

The AI League is a **closed monthly league** comprising the maintainer's unguided
hill-climbing submarine repositories, such as `submarine-claude-opus55`. It is not open
to community submissions.

Each monthly season lives under `ai-league/<YEAR>/<MONTH>/` (with settings in
`season.json` and entries in `entries/`) on the same monthly schedule as the Open League.
During the pre-freeze window (`prepWindowStartUtc` through `registrationDeadlineUtc`),
each AI submarine is given a chance — using a simple standardized prompt — to improve
itself against the previous month's registered submarines (pinned to the exact commit
hashes from the prior month's battles).

### Combat, Showreels & Replays

- **Follow the draw:** All opening matchups and byes are randomly drawn, recorded, and
  published before combat. Round 1 starts at least **48 hours after the draw is published**.
  Each later round starts at least **48 hours after the previous round's results and winning
  reels are published**, giving everyone time to watch and discuss them. Upcoming pairings and
  the earliest next start time appear on the bracket site. Winners advance through the recorded
  bracket using the same pinned submarine commits throughout the season.
- **Head-to-head combat series:** Each knockout matchup pits two submarines against each
  other across seeded games, alternating spawn slots and stopping early on clinch, with
  sudden-death tiebreakers if tied. The series length is set by each league's `season.json`.
- **Spectacle & replays:** Every knockout matchup publishes an automatically rendered 3D
  **Showreel** of the winner's most spectacular victory plus a downloadable `.zip` bundle
  of all `.srl` replays so you can inspect every game in the SeaRobots 3D viewer.

---

## How to Register for the Open League (PR Guide)

Community registration is available only for the Open League. Follow the steps below
to submit your submarine.

### Step 1: Build and test your submarine
Start from [searobots-template](https://github.com/thegreystone/searobots-template)
(click **Use this template** on GitHub) and make sure your repository is **public**.

Your submarine under `src/main/java/` must:
- Compile cleanly on **Java 25** against `se.hirt.searobots:searobots-api` alone (zero
  third-party dependencies; Maven is **not** executed by the league for security reasons).
- Have a public class implementing `se.hirt.searobots.api.SubmarineController` with a
  public no-arg constructor.
- Follow the security and good-faith rules in [RULES.md](RULES.md).

Push your code to GitHub and copy the **full 40-character commit SHA** you want to enter:

```bash
git rev-parse HEAD
```

### Step 2: Add your entry file for the upcoming month
Fork this repository and add a single JSON file under `open-league/<YEAR>/<MONTH>/entries/<slug>.json`
— for example, `open-league/2026/12/entries/red-october.json`:

```json
{
  "slug": "red-october",
  "name": "Red October",
  "shortName": "RO",
  "githubUser": "your-github-username",
  "repo": "https://github.com/your-github-username/red-october-sub",
  "commit": "0123456789abcdef0123456789abcdef01234567",
  "controllerClass": "com.example.RedOctoberSub",
  "license": "MIT",
  "description": "Deep-water silent stalker with staggered dual-torpedo salvos."
}
```

#### Field Reference
| Field | Rules |
|---|---|
| `slug` | Must match the filename (`<slug>.json`); lowercase `a-z`, `0-9`, `-` only (3–32 chars). |
| `name` | Display name shown on the bracket and HUD (2–24 printable characters). |
| `shortName` | 2-character hull sail marking (`A-Z` / `0-9` only, e.g. `"RO"`). Must be unique in the season. |
| `githubUser` | Must match the GitHub username opening the PR. |
| `repo` | Public HTTPS GitHub repository URL (`https://github.com/<owner>/<repo>`). |
| `commit` | Full 40-character lowercase hex Git commit SHA (immutable). |
| `controllerClass` | Fully-qualified Java class name implementing `SubmarineController` (public no-arg constructor). |
| `license` | SPDX license identifier (e.g. `MIT`, `BSD-3-Clause`, `Apache-2.0`). |
| `description` | Short plain-text description of your submarine (max 160 characters). |

### Step 3: Open a Pull Request
1. Open a PR targeting `main` modifying **only** your single `open-league/<YEAR>/<MONTH>/entries/<slug>.json` file.
2. Automated validation will:
   - Check the manifest schema and confirm one entry per GitHub account for that season.
   - Fetch your pinned commit and compile `src/main/java/**/*.java` directly with
     `javac --release 25 -proc:none` against the season's pinned `searobots-api`.
   - Run a deterministic bytecode allowlist scan and an AI good-faith / anti-cheat audit.
   - Run a sandboxed smoke match to verify your submarine runs cleanly without crashing
     or violating CPU/telemetry guardrails.
3. Maintainer review and merges happen during the **last week of the month** (submit and
   pass CI at least **24 hours before `registrationDeadlineUtc`** to guarantee review
   before the freeze). Once merged, you're registered for the Open League season!

Want to improve your sub before the deadline? While your PR is still open, simply update
the `commit` SHA on the same PR branch; once merged, open a new PR updating `commit` any
time before the registration freeze.

---

## Repository Structure

```text
searobots-league/
├── README.md                 # You are here
├── RULES.md                  # Official rules & code restrictions
├── LICENSE
├── ai-league/                # Closed AI League (monthly best-of-17; runs on the 1st)
│   └── <YEAR>/
│       └── <MONTH>/          # e.g. ai-league/2026/12/
│           ├── season.json   # Pinned SeaRobots version, deadline, post-freeze masterSeed
│           └── entries/
│               └── <slug>.json   # Maintainer-managed unguided AI entries
└── open-league/              # Open League (monthly best-of-17; runs on the 1st)
    └── <YEAR>/
        └── <MONTH>/          # e.g. open-league/2026/12/
            ├── season.json   # Pinned SeaRobots version, deadline, post-freeze masterSeed
            └── entries/
                ├── claude.json   # Official built-in house bot
                ├── codex.json    # Official built-in house bot
                ├── gemini.json   # Official built-in house bot
                └── <slug>.json   # Community registrations
```
