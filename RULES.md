# SeaRobots League — Official Rules

## 1. Eligibility & Registration
- **One submarine per person per season**, enforced via the PR author's GitHub account ID.
- Each monthly season lives under `monthly/<YEAR>/<MONTH>/` (e.g. `monthly/2026/12/`).
- Registrations close at the `registrationDeadlineUtc` specified in `monthly/<YEAR>/<MONTH>/season.json`
  (typically `23:59:59 UTC` on the last day of the prior month).
- The separate `ai-league/` bracket is triggered on demand at maintainer discretion.
- Every submission must pin an immutable **40-character commit SHA** on a public GitHub
  repository.
- Built-in SeaRobots AI submarines (`Claude`, `Codex`, `Gemini`) participate as official
  house entries (`"house": true`, maintainer-managed).

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
  with `B - N` Round 1 byes awarded to top seeds (returning finalists/semifinalists from
  the prior season, then deterministic seeded draw).
- **Fair seeds:** After registration freezes, the tournament `masterSeed` is derived from
  the frozen entries commit SHA combined with a public verifiable randomness beacon pulse.
- **Best-of-33 combat series (`Bo33`):**
  - Standard combat match config: 2-hour max simulation time (50 Hz), 8 torpedoes, 1000 HP,
    plus 25 seconds of post-decision aftermath recorded for Showreels.
  - Spawn slots alternate every game (`(A, B)` on odd games, `(B, A)` on even games).
  - **Game win:** sole surviving submarine (opponent destroyed or forfeited). Mutual kills
    and 2-hour timeouts count as draws.
  - **Early clinch:** a series ends as soon as one side cannot be caught or tied
    (`wins > opponentWins + remainingGames`).
  - **Sudden death & tiebreakers:** if tied after 33 games, up to 6 sudden-death seeds
    are played (alternating spawns; first decisive win advances). If still tied, tiebreakers
    apply in order: cumulative HP remaining → total damage dealt → faster average kill
    time → deterministic seeded coin flip.

## 5. Replays & Showreels
- Every matchup's full `.srl` replay set is published as a downloadable `.zip` bundle at
  [searobots.hirt.se](https://searobots.hirt.se) so all players can watch and learn in
  the SeaRobots 3D viewer.
- Each knockout matchup also gets an automatically generated 3D **Showreel** featuring
  the series winner's highest-scoring decisive victory.
