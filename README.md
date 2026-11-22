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

Both builds ran the same five-minute, 30-client exploration workload at view distance **10** and simulation distance **3**. Version 1.0.47 is the baseline before the 1.0.48 packet-dispatch optimization.

| Version | Average TPS | 95th-percentile tick time |
| --- | ---: | ---: |
| 1.0.47 | 20.0 | 74.46 ms |
| 1.0.48 | 20.0 | 53.19 ms |

The runs explored different fresh regions, so the tick-time difference does not establish a slowdown or speedup from the plugin change. In a separate CPU trace of 1.0.48 under load, **0.31% of sampled server CPU time** was inside HorizonGuard’s DLL The plugin horizon guard was using 0.31% of my cpu in a extreme test so im sure any server would be able to run it its very light weight but very powerfull.

### Improving server performance

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

I have worked on improving the performance of this plugin and from what i have tested pregenerating the chunks would be the biggest tps saver the plugin doesnt effect tps in a notable way the only time tps goes down is when i have tests of bots flying around generating chunks all at the same time never when theres 50 bots just stood still in a cave where things are being culled by the server, In my tests world gen is the most harmfull thing for tps not my plugin so if your thinking of running a server even without this plugin you should pregen your chunks.
