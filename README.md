# HorizonGuard Native

## Installation guide

1. Download the Windows x64 build from [HorizonX Releases](https://github.com/SaphirePay/HorizonX/releases), extract it, and run `HorizonX.exe`. For a new server, launch Minecraft Java Edition 26.3 once on the host PC, then create a HorizonsX server in the launcher. [HorizonX on GitHub](https://github.com/SaphirePay/HorizonX)
2. Check that your server has the native `paper_events` component available. HorizonGuard 1.0.28 targets Windows x64 with Minecraft Java Edition 26.3.
3. Stop the server. Download `HorizonGuard-Native-1.0.28.dll` from the [latest HorizonGuard release](https://github.com/badmasti2/HorizonGuard/releases/latest) and put it in your server's `plugins` folder. If upgrading, replace the previous HorizonGuard Native DLL.
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

## Performance testing

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

| Workload | Observed TPS | Average tick time in sampled windows | 95th-percentile tick time in sampled windows |
| --- | ---: | ---: | ---: |
| 50 walking bots with mixed surface and cave starts, sampled from 40 to 66 seconds once all bots were online | 19.55–20.55 | 38.5–50.5 ms | 45.0–63.5 ms |
| 10 bots in underground spectator flight, sampled from 30 to 60 seconds | 19.54–20.35 | 39.0–50.4 ms | 55.1–76.6 ms |

These are whole-server measurements from earlier native development builds. TPS is the sampled tick rate, and tick time shows how long each tick took to process. The 50-bot walking run stayed near 20 TPS after the join ramp, with some slow ticks. An earlier live benchmark with 49 connected bots recorded about 14.85–16.44 TPS in its final three loaded samples. Results depend on the workload and server settings. The current release has not been load-tested in these scenarios.

## The pc might have 16gb of ram but the server was using less than 1gb of ram it is coded in rust with no java layer so its very well optimized thats how it can have sutch good culling with very little performance drop.
