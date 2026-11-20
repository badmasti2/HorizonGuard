# HorizonGuard Native

## Installation tutorial

1. Download the Windows x64 build from [HorizonX Releases](https://github.com/SaphirePay/HorizonX/releases), extract it, and run `HorizonX.exe`. For a new server, launch Minecraft Java Edition 26.3 once on the host PC, then create a HorizonsX server in the launcher. [HorizonX on GitHub](https://github.com/SaphirePay/HorizonX)
2. Check that your server has the native `paper_events` component available. HorizonGuard 1.0.27 targets Windows x64 with Minecraft Java Edition 26.3.
3. Stop the server. Download `HorizonGuard-Native-1.0.27.dll` from the [latest HorizonGuard release](https://github.com/badmasti2/HorizonGuard/releases/latest) and put it in your server's `plugins` folder. If upgrading, replace the previous HorizonGuard Native DLL.
4. Start the server. Look for **HorizonGuard Native applied config.yml** in the console. Join with a matching client to check the installation.
5. Edit `plugins/HorizonGuardNative/config.yml` to change settings. HorizonGuard creates the file automatically and carries over values from an existing `config.cfg`. Save to apply changes while the server is running.

Direct connections use the DLL's built-in filters. If you use ViaProxy, matching the tested proxy setup also requires separate block-entity and lighting filters on the proxy.

## Features

- **Anti-freecam and base hiding** — Masks concealed underground areas and likely bases separately for each player.
- **Anti-X-ray and ore ESP** — Hides buried ores and ancient debris until the reveal rules are met.
- **Storage, spawner, and geode protection** — Masks hidden chests, other containers, spawners, and amethyst markers.
- **Overworld surface protection** — Hides underground players from above-ground viewers while keeping the player list.
- **Live configuration** — 70 documented settings for toggles, reveal distances, scan limits, timings, and disguise materials.

## What ESP looks for and how HorizonGuard handles it

| What cheat clients try to find | How HorizonGuard handles it |
| --- | --- |
| Hidden rooms using freecam or a base finder | Sends disguised terrain to viewers who haven't met the reveal rules. A player inside the room can see it normally. |
| Ores through stone using X-ray or ore ESP | Sends ordinary rock in place of buried ores and reveals exposed ores according to the configured distance and visibility rules. |
| Chests and spawners through walls or from block-entity metadata | Sends a disguise for hidden blocks and filters metadata that could reveal where they are. |
| Location clues from lighting, sounds, or particles | Filters eligible updates that could give away masked blocks or areas. |
| Underground players viewed from the Overworld surface | Uses full skylight at eye level as the surface indicator. Withholds underground player entities from surface viewers while keeping the player list. |

## How visibility works

HorizonGuard makes masking decisions separately for each viewer. Two players in the same area can receive different versions of the same chunk. For example, a player inside a base can see the room while a player outside sees a terrain disguise. The physical world still uses the original blocks.

- **Terrain and bases** use separate rules. Connected-cave scans help keep the cave you're exploring visible. Base detection uses clues such as storage clusters and crafted blocks. The YAML controls scan size, reveal distance, and how long a base stays visible after you leave.
- **Ores and storage** have independent settings. Ores can reveal when exposed within range. Storage has adjustable sightline tolerance and a short visibility grace period. Spawners and amethyst markers can be toggled separately.
- **Player visibility** in 1.0.27 uses Overworld skylight at eye level to identify surface viewers. Roofs and other cover can affect that classification. Players remain in the player list.

## Performance testing

**Test rig** — AMD Ryzen 7 7800X3D (8 cores, 16 threads), 16 GiB installed RAM, Windows 11 Pro 64-bit. HorizonsX and the test bots ran on the same PC.

| Workload | Observed TPS | Average tick time in sampled windows | 95th-percentile tick time in sampled windows |
| --- | ---: | ---: | ---: |
| 50 walking bots with mixed surface and cave starts, sampled from 40 to 66 seconds once all bots were online | 19.55–20.55 | 38.5–50.5 ms | 45.0–63.5 ms |
| 10 bots in underground spectator flight, sampled from 30 to 60 seconds | 19.54–20.35 | 39.0–50.4 ms | 55.1–76.6 ms |

These are whole-server measurements from earlier native development builds. TPS is the sampled tick rate, and tick time shows how long each tick took to process. The 50-bot walking run stayed near 20 TPS after the join ramp, with some slow ticks. An earlier live benchmark with 49 connected bots recorded about 14.85–16.44 TPS in its final three loaded samples. Results depend on the workload and server settings. Version 1.0.27 passed the build's unit tests and has yet to run through these load scenarios.
