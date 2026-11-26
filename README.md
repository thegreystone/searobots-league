# SeaRobots Monthly League

Welcome to the official registration repository for the **SeaRobots Monthly Knockout League**!

- **Simulator & 3D Viewer:** [thegreystone/searobots](https://github.com/thegreystone/searobots)
- **Starter Submarine Template:** [thegreystone/searobots-template](https://github.com/thegreystone/searobots-template)
- **Results, Brackets, Showreels & Replays:** [searobots.hirt.se](https://searobots.hirt.se)
- **Official Rules:** [RULES.md](RULES.md)

---

## How the League Works

- **Monthly knockout tournament:** Runs on the **1st of every month** (registrations close
  at `23:59:59 UTC` on the last day of the preceding month).
- **Best-of-33 combat series:** Each knockout matchup pits two submarines head-to-head on
  up to 33 random seeds (alternating spawn slots, stopping early on clinch, with
  sudden-death tiebreakers if tied).
- **Spectacle & replays:** Every knockout matchup publishes an automatically rendered 3D
  **Showreel** of the winner's most spectacular victory plus a downloadable `.zip` bundle
  of all `.srl` replays so you can inspect every game in the SeaRobots 3D viewer.
- **One submarine per person:** Enforced per season via your GitHub account ID (no email
  addresses or personal data are stored in this repository).

---

## How to Register Your Submarine (PR Guide)

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
Fork this repository and add a single JSON file under `monthly/<YEAR>/<MONTH>/entries/<slug>.json`
— for example, `monthly/2026/12/entries/red-october.json`:

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
1. Open a PR targeting `main` modifying **only** your single `monthly/<YEAR>/<MONTH>/entries/<slug>.json` file.
2. Automated validation will:
   - Check the manifest schema and confirm one entry per GitHub account for that season.
   - Fetch your pinned commit and compile `src/main/java/**/*.java` directly with
     `javac --release 25 -proc:none` against the season's pinned `searobots-api`.
   - Run a deterministic bytecode allowlist scan and an AI good-faith / anti-cheat audit.
   - Run a sandboxed smoke match to verify your submarine runs cleanly without crashing
     or violating CPU/telemetry guardrails.
3. Once checks pass and your PR is merged, you're locked in for the season!

Want to improve your sub before the deadline? Simply open a new PR updating the `commit`
SHA in your entry file any time before the registration freeze.

---

## Repository Structure

```text
searobots-league/
├── README.md                 # You are here
├── RULES.md                  # Official league rules & code restrictions
├── LICENSE
├── ai-league/                # On-demand AI League (triggered at maintainer discretion)
│   ├── season.json
│   └── entries/
│       ├── gemini-neon.json  # Standalone searobots-gemini-neon
│       └── starter-sub.json  # Baseline searobots-template
└── monthly/                  # Automated monthly knockout league (runs 1st of every month)
    └── <YEAR>/
        └── <MONTH>/          # e.g. monthly/2026/12/
            ├── season.json   # Pinned SeaRobots version, deadline, post-freeze masterSeed
            └── entries/
                ├── claude.json   # Official built-in house bot
                ├── codex.json    # Official built-in house bot
                ├── gemini.json   # Official built-in house bot
                └── <slug>.json   # Community registrations
```
