# HorizonGuard Native

## Installation guide

1. Download the Windows x64 build from [HorizonX Releases](https://github.com/SaphirePay/HorizonX/releases), extract it, and run `HorizonX.exe`. For a new server, launch Minecraft Java Edition 26.3 once on the host PC, then create a HorizonsX server in the launcher. [HorizonX on GitHub](https://github.com/SaphirePay/HorizonX)
2. Check that your server has the native `paper_events` component available. HorizonGuard 1.0.28 targets Windows x64 with Minecraft Java Edition 26.3.
3. Stop the server. Download `HorizonGuard-Native-1.0.30.dll` from the [latest HorizonGuard release](https://github.com/badmasti2/HorizonGuard/releases/latest) and put it in your server's `plugins` folder. If upgrading, replace the previous HorizonGuard Native DLL.
4. Start the server. Look for **HorizonGuard Native applied config.yml** in the console. Join with a matching client to check the installation.
5. Edit `plugins/HorizonGuardNative/config.yml` to change settings. HorizonGuard creates the file automatically and carries over values from an existing `config.cfg`. Save to apply changes while the server is running.

Direct connections use the DLL's built-in filters. If you use ViaProxy, matching the tested proxy setup also requires separate block-entity and lighting filters on the proxy.

## Features

- **Anti-freecam and base hiding** — Masks concealed underground areas and likely bases separately for each player it does this by searching for something that looks like a base and when there is nobody there or nobody could physically see into your base it shows up as deepslate so it doesnt show up on and base finders/freecams.

- **Anti-X-ray and ore ESP** — Hides buried ores and ancient debris until the reveal rules are met - sutch as (this is changable) currently touching air (aka vissable to someone not cheating) + other culling to minamize xray.

- **Storage, spawner, and geode protection** — Masks hidden chests, other containers, spawners, and amethyst markers This masks storage blocks and amethyst markers as normal blocks so they cant be seen with an esp unless your next to them and there visible by you serverside.

- **Overworld surface protection** — Hides underground players from above-ground viewers while keeping the player list.

- **Live configuration** — 70 documented settings for toggles, reveal distances, scan limits, timings, and disguise materials.

## Why the source is private

HorizonGuard is distributed as a compiled Windows plugin. The source is kept private so the protection logic is less straightforward to study and target. A compiled DLL can still be reverse-engineered. HorizonGuard's primary protection is filtering hidden information on the server before it reaches clients, Im doing this to attempt to minamise the bypassability of this plugin to keep servers safe from cheaters finding your base and if your mad your a cheater.

to keep this trusted i will send the Source code to trusted people to inspect.

## Performance testing

### November 21, 2026 — Native 1.0.45 and 1.0.46

Live-server runs used the test rig below, a view distance of 10, a simulation distance of 3, and one additional connected player. The figures measure the whole server.

| Workload | 1.0.45 TPS | 1.0.46 TPS |
| --- | ---: | ---: |
| Seven stationary surface bots | 20.00 | 20.00 |
| Walking clients during the final loaded samples | 14.82–18.04 (50 connected) | 16.09–19.88 (49 connected) |
| Ten underground spectator bots, 60-second server report | 10.93 | 18.54 |

The surface figures are medians from seven snapshots per build and pass. The reverse pass measured median CPU use of 0.82 → 0.70 logical cores and resident memory of 1,034 → 1,028 MiB. The two test orders disagree on the direction of the tick-time change, so the surface runs do not establish a speedup. Windows exposed no GPU engine counters for the dedicated game process during the sampled surface and flight intervals.

The walking rerun started 50 clients, and one was kicked by the server's floating check. The game process reached about 2.9 GiB resident memory in sampled walking intervals as loaded chunks grew to about 32,000. The flight rerun reused the same route after the first pass generated terrain. Terrain decoration accounted for about 2.08 seconds of the worst tick on the first flight pass, and the timing difference cannot be isolated to the plugin.

Version 1.0.46 skips decoding outgoing packets that have no active HorizonGuard filter and rechecks chunk residency before requesting an uncached base-scan tile. All eight unit tests passed.

### Improving server performance

- **Pregenerate your main play area during downtime.** This moves terrain generation and feature placement out of normal play. Players exploring beyond the pregenerated area will still generate chunks. Pregeneration uses disk space, and the server still has to load, tick, and send existing chunks.
- **Reduce how many chunks the server handles per player.** If TPS dips under load, try lowering server view distance from 10 to 8 while keeping simulation distance at 3. This gives players a shorter view but reduces chunk loading, streaming, and packet filtering. Compare average and 95th-percentile tick time under the same workload before changing another setting.
- **Choose CPU hardware for the workload.** More fast cores can help HorizonsX when chunk generation and multiple player regions run in parallel. Strong per-core performance still matters for work that must finish on a single thread. A higher core count alone does not guarantee steadier TPS. Test on an equivalent world before buying hardware for this workload.

**How much does HorizonGuard cost?** Both native builds held about 20 TPS with seven stationary surface bots and one additional player. The flight profile identified terrain decoration as the largest contributor to the worst measured tick. Those runs used HorizonGuard throughout, so they do not establish a percentage difference versus running without it. To measure plugin overhead, repeat the same bot routes on an isolated, pregenerated copy with identical settings, once with HorizonGuard enabled and once without it. Compare tick times as well as TPS, since both runs can reach 20 TPS while using different amounts of CPU time.

### Earlier runs

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

| Workload | Observed TPS | Average tick time in sampled windows | 95th-percentile tick time in sampled windows |
| --- | ---: | ---: | ---: |
| 50 walking bots with mixed surface and cave starts, sampled from 40 to 66 seconds once all bots were online | 19.55–20.55 | 38.5–50.5 ms | 45.0–63.5 ms |
| 10 bots in underground spectator flight, sampled from 30 to 60 seconds | 19.54–20.35 | 39.0–50.4 ms | 55.1–76.6 ms |

These are whole-server measurements from earlier native development builds. TPS is the sampled tick rate, and tick time shows how long each tick took to process. The 50-bot walking run stayed near 20 TPS after the join ramp, with some slow ticks. An earlier live benchmark with 49 connected bots recorded about 14.85–16.44 TPS in its final three loaded samples. Results depend on the workload and server settings.

### The PC might have 16GB of RAM, but the server itself was using less than 1GB. It's written entirely in Rust, with no Java runtime overhead, which helps keep memory usage and performance overhead low.

That's how it can perform such extensive culling with very little performance impact. I know how resource-intensive these types of plugins can be, so I've spent a lot of time optimizing how the system hides information from clients without compromising its effectiveness.

### 1.0.30 surface comparison

One short A/B run on the same PC used seven stationary surface bots in spectator mode and one additional connected player. Both versions ran on the same server with the same settings. The table reports the median of five server snapshots taken two seconds apart after warm-up.

| Version | TPS | Average tick time | 95th-percentile tick time |
| --- | ---: | ---: | ---: |
| 1.0.29 | 20.00 | 11.36 ms | 24.98 ms |
| 1.0.30 | 20.00 | 11.70 ms | 24.41 ms |

Both versions held about 20 TPS. The new build had slightly shorter slow ticks and slightly higher average tick time. This workload did not establish an overall speedup. It did not measure dropped-item load or repeat the earlier 50-bot walking test.
