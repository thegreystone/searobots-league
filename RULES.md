# SeaRobots Leagues — Official Rules

## 1. Eligibility & Registration
- **Two monthly leagues:**
  - **Open League (`open-league/<YEAR>/<MONTH>/`):** Open to community submissions.
    **One submarine per person per season**, enforced via the PR author's GitHub account ID.
    Built-in SeaRobots AI submarines (`Claude`, `Codex`, `Gemini`) participate as official
    house entries (`"house": true`, maintainer-managed).
  - **AI League (`ai-league/<YEAR>/<MONTH>/`):** Closed league for the maintainer's
    unguided hill-climbing submarines (`"allowDuplicateOwners": true`). Before each new
    month's freeze, entries may run a standardized improvement pass against the previous
    month's registered submarines (using the pinned commit SHAs from the prior month's
    battles).
- Both leagues run monthly: registrations close at the `registrationDeadlineUtc` specified
  in `<league>/<YEAR>/<MONTH>/season.json` (typically `23:59:59 UTC` on the last day of
  the prior month), with the draw on the 1st of the month.
- **PR submission & review cadence (Open League):** Registration PRs may be opened as soon
  as the upcoming season folder exists, and automated CI validation runs immediately.
  Human good-faith review and PR merges happen during the **last week of the month**
  (`reviewWindowStartUtc` through `registrationDeadlineUtc`). PRs opened and passing CI at
  least **24 hours before `registrationDeadlineUtc`** are guaranteed review before the
  freeze; later PRs are best-effort and may wait for the following month's season.
- Every submission must pin an immutable **40-character commit SHA** on a public GitHub
  repository.

## 2. Technical & Security Rules (v1 — Java 25)
- Submissions must be **pure, self-contained Java 25 source files** under
  `src/main/java/**/*.java`.
- **No build scripts are executed.** The league compiles your sources directly with
  `javac --release 25 -proc:none` against the season's pinned `searobots-api` JAR alone.
- **Zero third-party dependencies**, zero precompiled JARs, zero native code, and zero
  `searobots-engine` imports in `src/main/java/`.
- **Source size limits:** at most 64 `.java` files and 512 KB total source size.
- **Allowed JDK APIs only:** your own classes, `se.hirt.searobots.api.*`, and safe
  standard packages (`java.lang` math/primitives/strings/records, `java.util`,
  `java.util.function`, `java.util.stream`, `java.math`, `java.time`).
- **Strictly prohibited APIs** (enforced by deterministic bytecode scanning):
  - Reflection (`java.lang.reflect.*`) and non-compiler `java.lang.invoke.*`
  - Filesystem (`java.io.*`, `java.nio.*`) and networking (`java.net.*`)
  - Process/JVM control (`Runtime`, `Process`, `ProcessBuilder`, `System.exit`,
    `System.load*`, `ClassLoader`, `sun.*`, `jdk.*`, `se.hirt.searobots.engine.*`)
  - Spawning threads or executors (`Thread`, `Timer`, `java.util.concurrent.*` executors)
  - Native methods

## 3. Good-Faith & Sportsmanship
All entries undergo an automated AI review and human approval check for good-faith play:
- Source code must be **human-readable** (no obfuscation, minification, or hidden payloads).
- Submarines must actively attempt to hunt and defeat opponents — no deliberate passive
  stalling, timeout griefing, or engine/telemetry abuse.
- No cross-match static state smuggling or opponent-name-specific exploits.
- Runtime guardrails enforce exception-to-forfeit, shared submarine + torpedo CPU
  budgets, and per-tick telemetry caps (`SimulationLoop.ControllerLimits`).

## 4. Tournament Format
- **Single-elimination knockout bracket** sized to the next power of two (`B = nextPow2(N)`),
  giving `log2(B)` rounds (so total tournament duration depends on the number of registered
  entries). Every entrant is assigned a draw number by a reproducible random shuffle.
  Returning finalists take part in the same draw. The first `B - N` randomly drawn numbers
  receive Round 1 byes, distributed across the bracket's wings.
- **Fair seeds:** After registration freezes, the tournament `masterSeed` is derived from
  the frozen entries commit SHA combined with a public verifiable randomness beacon pulse.
  Individual matchup and game seeds are deterministically derived from `masterSeed` and
  recorded alongside the published results so any match can be independently replayed and
  verified.
- **Draw announcement:** Record and publish every opening matchup, bye, and future bracket slot
  before running tournament combat. The immutable draw also records the pinned entries,
  match settings, engine version, and seed proof. Later rounds advance the winners along
  this bracket without redrawing opponents.
- **Viewing periods:** Wait **48 hours after the draw is published before Round 1**. After
  each round, publish its results, winning Showreels, and replay bundles, then wait at least
  another **48 hours** before running the next round, including the Final. The interval is
  recorded as `roundIntervalHours` in `season.json`; a delayed match or publication shifts
  the next round later. The website shows upcoming pairings and the earliest next start time.
- **Frozen entries:** The same recorded commits and match settings apply to every round;
  participants cannot replace a submarine between rounds. Completed matchups are checkpointed
  so interrupted runs resume without repeating them.
- **Best-of-17 combat series (`Bo17`):**
  - Standard combat match config: 2-hour max simulation time (50 Hz), 8 torpedoes, 1000 HP,
    plus 25 seconds of post-decision aftermath recorded for Showreels.
  - Spawn slots alternate every game (`(A, B)` on odd games, `(B, A)` on even games).
  - **Game win:** sole surviving submarine (opponent destroyed or forfeited). Mutual kills
    and 2-hour timeouts count as draws.
  - **Early clinch:** a series ends as soon as one side cannot be caught or tied
    (`wins > opponentWins + remainingGames`).
  - **Sudden death & tiebreakers:** if tied after 17 games, up to 6 sudden-death seeds
    are played (alternating spawns; first decisive win advances). If still tied, tiebreakers
    apply in order: cumulative HP remaining → total damage dealt → faster average kill
    time → deterministic seeded coin flip.

## 5. Replays & Showreels
- Every matchup's full `.srl` replay set is published as a downloadable `.zip` bundle at
  [searobots.hirt.se](https://searobots.hirt.se) so all players can watch and learn in
  the SeaRobots 3D viewer.
- Each knockout matchup also gets an automatically generated 3D **Showreel** featuring
  the series winner's highest-scoring decisive victory.
