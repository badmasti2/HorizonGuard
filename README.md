# HorizonGuard Native

Native anti-ESP, anti-X-ray, and base-concealment plugin for HorizonsX.

## Installation guide

1. Download the Windows x64 build from [HorizonX Releases](https://github.com/SaphirePay/HorizonX/releases), extract it, and run `HorizonX.exe`. For a new server, launch Minecraft Java Edition 26.3 once on the host PC, then create a HorizonsX server in the launcher. [HorizonX on GitHub](https://github.com/SaphirePay/HorizonX)
2. Check that your server has the native `paper_events` component available. HorizonGuard 1.0.58 targets Windows x64 with Minecraft Java Edition 26.3.
3. Stop the server. Download the current HorizonGuard DLL from the [latest HorizonGuard release](https://github.com/badmasti2/HorizonGuard/releases/latest) and put it in your server's `plugins` folder. If upgrading, replace the previous HorizonGuard Native DLL.
4. Start the server. Look for **HorizonGuard Native applied config.yml** in the console. Join with a matching client to check the installation.
5. Edit `plugins/HorizonGuardNative/config.yml` to change settings. HorizonGuard creates the file automatically and carries over values from an existing `config.cfg`. Save to apply changes while the server is running.

Direct connections use the DLL's built-in filters. If you use ViaProxy, matching the tested proxy setup also requires separate block-entity and lighting filters on the proxy.

## Features

- **Anti-freecam and base hiding** — Masks concealed underground areas and likely bases separately for each player it does this by searching for something that looks like a base and when there is nobody there or nobody could physically see into your base it shows up as deepslate so it doesnt show up on and base finders/freecams.

- **Anti-X-ray and ore ESP** — Hides buried ores and ancient debris until the reveal rules are met - sutch as (this is changable) currently touching air (aka vissable to someone not cheating) + other culling to minamize xray.

- **Storage and spawner protection** — Masks hidden chests, other containers, and spawners. This masks storage blocks as normal blocks so they cant be seen with an esp unless your next to them and there visible by you serverside.

- **Overworld surface protection** — Masks eligible terrain below configurable height cutoffs.

- **Live configuration** — Documented settings for toggles, reveal distances, scan limits, timings, and disguise materials.

## Why the source is private

HorizonGuard is distributed as a compiled Windows plugin. The source is kept private so the protection logic is less straightforward to study and target. A compiled DLL can still be reverse-engineered. HorizonGuard's primary protection is filtering hidden information on the server before it reaches clients, Im doing this to attempt to minamise the bypassability of this plugin to keep servers safe from cheaters finding your base and if your mad your a cheater.

to keep this trusted i will send the Source code to trusted people to inspect.

## Performance testing

#### HorizonGuard has a small performance cost under extreme testing conditions. It makes base-finding and xray cheats far less effective while keeping gameplay as close to vanilla as possible.

### Latest 1.0.58 optimization benchmarks (before vs. after)

These tests compare the **previous HorizonGuard build against the optimized 1.0.58 build**, with HorizonGuard enabled in both. They do **not** measure plugin-on versus plugin-off overhead.

Each workload ran for **five minutes after staggered joins and warm-up**, with clients occupying separate chunks. The mixed run had **22 stationary underground, 22 stationary above ground, and 22 moving through pre-generated terrain**; all 22 moving routes were checked against **1,600 saved chunks**. The reported values are medians from the sustained measurement windows, not join-time performance.

| Metric | 50 above ground | 50 underground | 66 mixed* |
| --- | ---: | ---: | ---: |
| **TPS before** | 20.00 | 20.01 | 19.96 |
| **TPS after** | 20.00 | 20.00 | 20.00 |
| Median tick time before | 7.88 ms | 16.60 ms | 11.82 ms |
| Median tick time after | 7.95 ms | 9.66 ms | 10.15 ms |
| Tick-time change | +0.9% | **41.8% lower** | **14.1% lower** |

*66 mixed = 22 underground, 22 above ground, and 22 moving clients.*


For the 50-client underground workload, median server CPU use also fell from **0.49 to 0.33 equivalent cores** (about **32.7% lower**). The small surface tick-time increase is not enough on its own to establish a meaningful regression. All **17 automated tests passed**. These are simulated-client workloads; they do not establish performance with the same number of real players performing arbitrary gameplay actions.

#### What changed in optimized 1.0.58

- **Batch nearby block reads** instead of repeatedly querying individual positions.
- **Cache collision facts by block state** to avoid repeating equivalent calculations.
- **Invalidate cached positions when blocks change** to keep visibility decisions current.
- **Reuse player position and configuration data** during visibility checks.
- **Batch nearby chest reveals** to reduce repeated work and packet handling.
- **Skip effect parsing for unrelated packets** to avoid unnecessary processing.

The shared safety cap remains **300,000 block-fact queries per rolling 10 seconds**, configurable in `plugins/HorizonGuardNative/config.yml`. The optimization reduces query demand before the cap is reached; the cap is not a benchmark result. The optimized 1.0.58 DLL and this cap setting were applied to `my-server` after testing.

### Improving server performance

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

Fresh chunk generation can place a substantial load on the server. The latest five-minute comparisons above use pre-generated terrain for moving clients and compare the earlier build against the optimized 1.0.58 build. They do not measure HorizonGuard against an empty plugin directory.

**Recommendation:** Pre-generate the areas players are likely to explore where practical, whether or not you use HorizonGuard. This reduces live terrain-generation work and should help server performance, although it does not remove all chunk-loading or packet-processing costs. The mixed benchmark above uses pre-generated routes, but additional plugin-on versus plugin-off tests would be needed to quantify HorizonGuard overhead.
