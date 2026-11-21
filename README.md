# HorizonGuard Native

## Installation guide

1. Download the Windows x64 build from [HorizonX Releases](https://github.com/SaphirePay/HorizonX/releases), extract it, and run `HorizonX.exe`. For a new server, launch Minecraft Java Edition 26.3 once on the host PC, then create a HorizonsX server in the launcher. [HorizonX on GitHub](https://github.com/SaphirePay/HorizonX)
2. Check that your server has the native `paper_events` component available. HorizonGuard 1.0.47 targets Windows x64 with Minecraft Java Edition 26.3.
3. Stop the server. Download `HorizonGuard-Native-1.0.47.dll` from the [latest HorizonGuard release](https://github.com/badmasti2/HorizonGuard/releases/latest) and put it in your server's `plugins` folder. If upgrading, replace the previous HorizonGuard Native DLL.
4. Start the server. Look for **HorizonGuard Native applied config.yml** in the console. Join with a matching client to check the installation.
5. Edit `plugins/HorizonGuardNative/config.yml` to change settings. HorizonGuard creates the file automatically and carries over values from an existing `config.cfg`. Save to apply changes while the server is running.

Direct connections use the DLL's built-in filters. If you use ViaProxy, matching the tested proxy setup also requires separate block-entity and lighting filters on the proxy.

## Features

- **Anti-freecam and base hiding** — Masks concealed underground areas and likely bases separately for each player it does this by searching for something that looks like a base and when there is nobody there or nobody could physically see into your base it shows up as deepslate so it doesnt show up on and base finders/freecams.

- **Anti-X-ray and ore ESP** — Hides buried ores and ancient debris until the reveal rules are met - sutch as (this is changable) currently touching air (aka vissable to someone not cheating) + other culling to minamize xray.

- **Storage, spawner, and geode protection** — Masks hidden chests, other containers, spawners, and amethyst markers This masks storage blocks and amethyst markers as normal blocks so they cant be seen with an esp unless your next to them and there visible by you serverside.

- **Overworld surface protection** — Masks eligible terrain below configurable height cutoffs.

- **Live configuration** — Documented settings for toggles, reveal distances, scan limits, timings, and disguise materials.

## Why the source is private

HorizonGuard is distributed as a compiled Windows plugin. The source is kept private so the protection logic is less straightforward to study and target. A compiled DLL can still be reverse-engineered. HorizonGuard's primary protection is filtering hidden information on the server before it reaches clients, Im doing this to attempt to minamise the bypassability of this plugin to keep servers safe from cheaters finding your base and if your mad your a cheater.

to keep this trusted i will send the Source code to trusted people to inspect.

## Performance testing

### Native 1.0.48 — 30-client fresh-terrain test

Thirty scripted spectator clients traveled in separate regions for five minutes after a 30-second warm-up. Ten moved at sprint speed, ten at elytra speed, and ten flew underground. The test used view distance **10**, simulation distance **3**, and a four-chunk-per-player-per-tick send cap. The scripts reproduced travel speeds without running survival or elytra physics.

| Test | HorizonGuard off | HorizonGuard 1.0.48 on |
| --- | ---: | ---: |
| Same-seed run, average TPS | 20.0 | 20.0 |
| Same-seed run, P95 tick time | 48.29 ms | 48.38 ms |
| Same-seed run, average server CPU | 13.91 logical cores | 14.12 logical cores |
| Reverse-order repeat, average TPS | 20.0 | 20.0 |
| Reverse-order repeat, P95 tick time | 67.58 ms | 60.21 ms |
| Reverse-order repeat, average server CPU | 13.00 logical cores | 13.28 logical cores |

The enabled runs used about **0.21–0.28 additional logical cores** in these paired tests. Windows CPU sampling during one enabled run found **0.31%** of server samples executing inside the HorizonGuard DLL and **0.38%** of sampled server stacks containing it. The paired CPU figures include the rest of the server and vary with generation and chunk delivery. The fastest clients regularly outran chunk delivery with the plugin enabled and disabled.

**Compared with 1.0.47:** Version 1.0.48 reuses event-listener lists for packet dispatch to avoid building a new list per event. On the live server, 1.0.47 and 1.0.48 both averaged **20.0 TPS** on five-minute fresh-terrain runs, with **54.46 ms** and **63.19 ms** P95 tick times respectively. These runs traversed different regions, so they do not establish a version-to-version speedup or regression. Version 1.0.47 already had earlier optimizations and serves as the baseline before this dispatch change.

### November 21, 2026 — Native 1.0.47, 20-bot flight test

Twenty underground spectator bots flew in different directions for each 60-second run on a Ryzen 7 7800X3D. Server view distance was **10** and simulation distance was **3**.

| Run | TPS | 95th-percentile tick time | Ticks above 50 ms |
| --- | ---: | ---: | ---: |
| First pass over the main test routes | 20.00 | 64.58 ms | 131 |
| Warm pass with a four-chunk-per-player-per-tick send cap | 20.01 | 41.74 ms | 12 |
| Separate routes requiring fresh generation, same send cap | 20.01 | 43.09 ms | 19 |

Warm-route clients received about **4,760 chunk packets per bot**, compared with about **361 per bot** on fresh routes. The fresh run held TPS while terrain delivery fell behind max-speed flight. The warm route and send cap changed together, so the table does not isolate how much the cap improved timings. The cap is a HorizonsX setting used for the benchmark and is separate from HorizonGuard's background-refresh setting.

### November 21, 2026 — Native 1.0.45 and 1.0.46

Live-server runs used the test rig below, a view distance of 10, a simulation distance of 3, and one additional connected player. The figures measure the whole server.

| Workload | 1.0.45 TPS | 1.0.46 TPS | Average tick time (1.0.45 → 1.0.46) |
| --- | ---: | ---: | ---: |
| Seven stationary surface bots | 20.00 | 20.00 | 9.87 → 10.74 ms, then 10.94 → 10.38 ms in reverse order |
| Walking clients during the final loaded samples | 14.82–18.04 (50 connected) | 16.09–19.88 (49 connected) | 55.40–67.03 → 50.23–61.76 ms |
| Ten underground spectator bots, 60-second server report | 10.93 | 18.54 | 90.62 → 47.16 ms |

The surface figures are medians from seven snapshots per build and pass. The reverse pass measured median CPU use of 0.82 → 0.70 logical cores and resident memory of 1,034 → 1,028 MiB. The two test orders disagree on the direction of the tick-time change, so the surface runs do not establish a speedup. Windows exposed no GPU engine counters for the dedicated game process during the sampled surface and flight intervals.

The walking rerun started 50 clients, and one was kicked by the server's floating check. The game process reached about 2.9 GiB resident memory in sampled walking intervals as loaded chunks grew to about 32,000. The flight rerun reused the same route after the first pass generated terrain. Terrain decoration accounted for about 2.08 seconds of the worst tick on the first flight pass, and the timing difference cannot be isolated to the plugin.

Version 1.0.46 skips decoding outgoing packets that have no active HorizonGuard filter and rechecks chunk residency before requesting an uncached base-scan tile. All eight unit tests passed.

### Improving server performance

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

I have worked on improving the performance of this plugin and from what i have tested pregenerating the chunks would be the biggest tps saver the plugin doesnt effect tps in a notable way the only time tps goes down is when i have tests of bots flying around generating chunks all at the same time never when theres 50 bots just stood still in a cave where things are being culled by the server, In my tests world gen is the most harmfull thing for tps not my plugin so if your thinking of running a server even without this plugin you should pregen your chunks.
