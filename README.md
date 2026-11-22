# HorizonGuard Native|

### its in a working relese state but if you experence any bugs report it to me and il fix it i hasent had alot of gameplay testing.

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

#### HorizonGuard has a small performance cost under extreme testing conditions. It makes base-finding cheats far less effective while keeping gameplay as close to vanilla as possible.

### HorizonsX without HorizonGuard vs. optimized 1.0.58

Six protocol clients on the same Ryzen 7 7800X3D host, with view distance 10, simulation distance 3, and fresh worlds from the same seed. The baseline uses the same HorizonsX engine with the plugin directory empty. The moving workload has two clients at running speed, two at elytra speed, and two underground spectators in separate regions.

**What the six-client tests did**

- **Stationary.** Six spectator protocol clients joined, were teleported to six starting positions around a 4,000-block-radius circle, and held still while the server loaded their surrounding chunks. The reported result uses the final three samples after the initial warm-up.
- **Moving into new chunks.** The same clients moved simultaneously in separate directions from those positions. Two followed a straight route at running speed (about 5.6 blocks/s, starting at Y110), two followed an undulating route at elytra speed (30 blocks/s, starting at Y165), and two travelled underground at spectator speed (about 44.4 blocks/s at Y−32). Spectator mode let the clients follow repeatable routes without terrain collisions. These are scripted movement speeds, not six survival players.
- **How we measured.** Each phase warmed up for about 10 seconds before five samples spaced roughly two seconds apart. The table reports median server TPS, CPU use in equivalent logical cores, and resident process memory. The comparison used fresh worlds with the same seed and the same view and simulation distances. The only intended configuration difference was whether HorizonGuard was installed.

| Workload | Configuration | TPS | CPU cores used | RAM used |
| --- | --- | ---: | ---: | ---: |
| Stationary | No plugin | 20.00 | 0.26 | 630 MiB |
| Stationary | HorizonGuard | 20.00 | 0.44 | 724 MiB |
| Moving into new chunks | No plugin | 20.01 | 11.63 | 793 MiB |
| Moving into new chunks | HorizonGuard | 18.66 | 11.35 | 840 MiB |

These are whole-server medians from the last three stationary samples and all five moving samples. The no-plugin baseline ran separately from the optimized 1.0.58 run. Both stationary tests maintained 20 TPS. During fresh-chunk exploration, the plugin-on run measured 18.66 TPS versus 20.01 without the plugin (about 6.7% lower), while CPU use was slightly lower and resident memory was 47 MiB higher. Because there was only one run per configuration, this is an observed difference, not a repeatable estimate of HorizonGuard overhead. Repeated alternating on/off runs would help establish how much of the difference is consistent.


Both builds ran the same five-minute, 30-client exploration workload at view distance **10** and simulation distance **3**. Version 1.0.47 is the baseline before the 1.0.48 packet-dispatch optimization.

| Version | Average TPS | 95th-percentile tick time |
| --- | ---: | ---: |
| 1.0.47 | 20.0 | 74.46 ms |
| 1.0.48 | 20.0 | 53.19 ms |

The runs explored different fresh regions, so the tick-time difference does not establish a slowdown or speedup from the plugin change. In a separate CPU trace of 1.0.48 under load, **0.31% of sampled server CPU time** was attributed directly to HorizonGuard’s DLL. This is a low sampled share for that workload, but it is not the same as total plugin overhead: work triggered elsewhere in the server may not appear inside the DLL, and results may differ on other hardware or player workloads.

### Improving server performance

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

My tests suggest that **fresh chunk generation is a major source of server load**. Stationary clients, including earlier tests with 50 bots standing in a cave, maintained full TPS, while groups exploring new terrain at high speed placed much more demand on the server. The six-client no-plugin comparison also showed a TPS difference during fresh-chunk exploration, so I cannot claim HorizonGuard has zero impact in that workload.

**Recommendation:** Pre-generate the areas players are likely to explore where practical, whether or not you use HorizonGuard. This reduces live terrain-generation work and should help server performance, although it does not remove all chunk-loading or packet-processing costs. A repeat test on pre-generated terrain would help separate those costs from world generation.
