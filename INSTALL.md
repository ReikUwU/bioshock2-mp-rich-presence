# BioRPC — Discord Rich Presence for BioShock 2 Multiplayer

Shows what you are doing in BioShock 2 Multiplayer on your Discord profile: game mode,
map (with its postcard), the character you play, how the session is hosted and how many
players are in it.

Everything it shows is read from the running game. Nothing is guessed, nothing is sent
anywhere except to your own Discord client on this machine.

## What you need

1. **BioShock 2** on Steam, with the **multiplayer** build installed
   (`...\steamapps\common\BioShock 2\MP\Builds\Binaries\Bioshock2.exe`).
   The plugin is built for that 32-bit executable and will not load into anything else.
2. **The plugin loader** `dxgi.dll` from
   [Bioshock-2-Multiplayer-Plugins](https://github.com/SnowTempest/Bioshock-2-Multiplayer-Plugins),
   placed next to `Bioshock2.exe`. It creates the `BioPlugins` folder on first launch.
   If another mod already ships its own `dxgi.dll`, only one of them can be used.
3. **The Discord desktop application, running.** The plugin talks to it through the local
   pipe `discord-ipc-0`. The browser version of Discord cannot receive a presence.
   In Discord: *Settings → Activity Privacy → Share your detected activities*, enabled.
4. **Windows 8 or later** (64-bit Windows is fine — the game and the plugin are 32-bit).

That is the whole list.

**You do not need** a Visual C++ redistributable: the plugin links its runtime
statically, and imports nothing but `kernel32`, `ntdll` and one Windows API set
(verified on the shipped file's import table).
**You do not need** a Discord developer account or an application of your own: like
every Rich Presence integration, the plugin ships the id of the application that owns
the artwork, and that application's name is what Discord prints above the card. If you
would rather publish under your own application, drop a `BioRPC.ini` next to the DLL
with `client_id = <your id>` and upload the art listed in `docs/discord-assets.md`.
**You do not need** to run the game as administrator.

## Install

1. Close the game.
2. Copy `BioRPC.dll` into
   `...\steamapps\common\BioShock 2\MP\Builds\Binaries\BioPlugins\`.
3. Start BioShock 2 Multiplayer.

Within a few seconds your Discord profile shows the game.

## Uninstall

Delete `BioPlugins\BioRPC.dll`. Nothing else is touched: the plugin writes no registry
key and no file outside its own `BioRPC.log`.

## Checking it works

`BioPlugins\BioRPC.log`, next to the DLL, is written by the plugin itself:

```
[...] DllMain attach
[...] BioRPC 0.2.0 worker started
[...] Connected to Discord IPC
[...] SET_ACTIVITY {"details":"Survival of the Fittest","state":"In a private match",...}
```

* `Discord IPC unavailable` — Discord is not running, or it is the browser version.
* `Discord refused the handshake` — the application id is not reachable; the exact reply
  from Discord is on the same line.
* No `BioRPC.log` at all — the loader did not pick the plugin up. Check that
  `Plugins.log` (same folder) lists `BioRPC.dll`, and that `dxgi.dll` sits next to
  `Bioshock2.exe`.

## What it reads, and what it does not

The plugin reads the game's own script objects through the engine's reflection data, in
the game's own process, **read-only**: it never writes to the game's memory, never
touches the network, and never reads another process.

It publishes: where you are (menu, apartment, lobby, match), the game mode, the map, the
character class you selected, the player count, and whether the session is public,
private or LAN. It does not publish your account name, your friends, or anything typed
in chat.

## Known limits

* Built against the current Steam build of the multiplayer executable. A game patch that
  moves engine globals would stop the reading (the card would simply stay on
  "Starting up" — the plugin logs and gives up rather than guessing).
* The map of a **private lobby** is not published until the match starts.
* Antivirus software is suspicious of any DLL loaded into a game. The source is in this
  repository and the file can be rebuilt from it.
