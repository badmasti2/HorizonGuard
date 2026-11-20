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
