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
