# Changelog

## v0.2.0 — 2026-10-25

**A friend can now join your match from Discord.**

* The card carries a join secret pointing at the Steam lobby behind your session
  (`OnlineSubsystemSteamworks.LobbyServerID` plus the session's host id), so Discord
  shows a **Join** button on your friends' side.
* The plugin subscribes to `ACTIVITY_JOIN`. When a friend presses the button, their own
  plugin receives the secret and hands it to Steam as `steam://joinlobby/8850/...` —
  the same path as an invite from the friends list. Nothing is written into the game.
* The Discord party is now the Steam lobby itself (`steam-lobby-<id>`), so everyone in
  the same match is grouped together and nobody else is.
* Discord may push frames at any time once subscribed; replies are now matched by their
  nonce instead of being assumed to be the next frame.

For the button to do something, the friend needs the game running with this plugin.
Pressed with the game closed, Discord has nothing to hand the secret to.

Verified in a public lobby: subscription accepted, party and secret published and
accepted by Discord, and the invite seen on a friend's side with its Join button.

## v0.1.1 — 2026-10-25

Both limitations listed in v0.1.0 are gone, and neither was closed by guessing.

* **A lobby now shows the mode and the map of the match being set up**, private ones
  included. They are not exposed as named properties: the game keeps them in
  `OnlineGameSettings.Properties`, the UE3 settings array (`0x1000000F` for the mode,
  `0x10000010` for the map, `0x10000011` for the slots). Checked twice against the lobby
  screen — "Turf War / Hephaestus", then "Turf War / Farmer's Market" with the matching
  postcard on the card.
* **`EGameMode` is no longer assumed.** The written-down list of modes is gone; the
  plugin resolves the engine's own enum object and reads its entries. The order turned
  out to be right, but it is now read rather than believed.
* Player slots come from the session settings first, the connection counts second.

Modes seen in a real match so far: Survival of the Fittest, Civil War, Turf War.

## v0.1.0 — 2026-10-25

First packaged build. Everything below was observed in a running game, not inferred.

### The card

* Game mode as the headline, read from the engine's own `EGameMode` label in a match.
* Map postcard as the large image, its name on hover; the character portrait as the small
  badge, their name on hover. No name is written twice.
* Menu, apartment, lobby and match told apart; a session is announced as public, private
  or LAN from the session settings themselves.
* Party size from the lobby roster, and from `PRIArray` once a match starts.
* The elapsed timer restarts whenever the player moves between menu, lobby and match.

### Robustness

* The CRT is linked statically: no Visual C++ redistributable on the player's machine.
* The running executable's PE identity is checked at startup; a different build is called
  out in the log instead of being read blind.
* Discord replies are awaited with a deadline, so a stalled client can no longer freeze
  the worker thread forever.
* A refused handshake is reported instead of being mistaken for a healthy connection.
* Name indices are cached between polls and revalidated by their text; a poll above 50 ms
  is logged. None has been observed.
* `client_id` can be overridden per installation through `BioRPC.ini`.

### Defects found and fixed before release

* An empty `TArray` has a null data pointer, which made the player count disappear from
  the card on one poll out of three.
* The frontend's own replication info was overwriting the lobby's game mode.
* An unset quickmatch mode (`-1`) was being published as Survival of the Fittest.
* Party sizes outside Discord's `1 <= current <= max` rule were rejecting the whole frame.
* A constant party id put every player of the game in a single Discord party.
* The apartment was announced as a match, with a borrowed mode and a raw `DE_` level name.
* A private lobby was showing the mode of the previous public search.

### Known limits

* The map and the mode of a **private lobby** are not published before the match starts.
* `EGameMode` indices other than the two observed (Survival of the Fittest, Civil War)
  are not confirmed.
* Player counts above two have never been seen in a match.