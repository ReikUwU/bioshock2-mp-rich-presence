# Discord artwork this plugin expects

The plugin publishes to one Discord application, whose id is compiled in
(`DEFAULT_CLIENT_ID`) and can be overridden per installation with a `BioRPC.ini` next to
the DLL. That is the usual arrangement for Rich Presence: the application id is a public
identifier, the application's **name is what Discord prints above the card**, and its
**art assets are shared by everyone running the plugin**.

Consequences worth knowing before forking:

* every player using the shipped build appears under the owner's application name;
* renaming or deleting an asset changes or removes the picture for all of them;
* an image key the application does not hold is silently dropped by Discord, so the card
  loses its picture rather than falling back.

## Running it under your own application

1. Create an application at <https://discord.com/developers/applications>. Name it
   exactly as you want it to read on the card — Discord shows the name verbatim.
2. Upload the art below under **Rich Presence -> Art Assets**. The key is taken from the
   file name, lowercased, with anything but `a-z 0-9 _` replaced.
3. Drop a `BioRPC.ini` next to `BioRPC.dll`:

   ```ini
   client_id = your application id
   ```

4. Relaunch the game. `BioRPC.log` states which id it used.

## Keys the plugin asks for

### Generic

| key | used for |
|---|---|
| `menu` | main menu and lobbies |
| `bigdaddy` | a level with no postcard of its own |
| `mercury_suites_postcard` | the player's apartment |

### Maps

| level package | key |
|---|---|
| `DE_Arcadia` | `arcadia_postcard` |
| `DE_Fisheries` | `neptune_s_bounty_postcard` |
| `DE_FortFrolic` | `fort_frolic_postcard` |
| `DE_Freezers` | `fontaine_fisheries_postcard` |
| `DE_Gallery` | `dionysus_park_postcard` |
| `DE_Ghetto` | `pauper_s_drop` |
| `DE_Hephaestus` | `hephaestus_postcard` |
| `DE_HomeforthePoor` | `fontaine_s_home_for_the_poor` |
| `DE_Kashmir` | `kashmir_restaurant_postcard` |
| `DE_Market` | `farmer_s_market_postcard` |
| `DE_Medical` | `medical_pavilion_postcard` |
| `DE_MercurySuites` | `mercury_suits_postcard_multiplayer` |
| `DE_Museum` | `point_prometheus_postcard` |
| `DE_RedLightAlley` | `siren_alley_postcard` |
| `DE_Subbay` | `smuggler_s_hideout_postcard` |
| `DE_Tavern` | `fighting_mcdonagh_s_postcard` |

### Characters

| character class | key |
|---|---|
| `ShockMPActress` | `blanche` |
| `ShockMPBusinessMan` | `buck` |
| `ShockMPFisherman` | `zigo` |
| `ShockMPFootballer` | `danny` |
| `ShockMPHouseWife` | `barbara` |
| `ShockMPJailbird` | `louie` |
| `ShockMPPilot` | `naledi` |
| `ShockMPPlayboy` | `oscar` |
| `ShockMPSwami` | `suresh` |
| `ShockMPWelder` | `jacob` |

A missing key is not fatal: the card simply shows no image for that case. The test
`tools/test_payloads.py` asks Discord for the application's real asset list and fails if
the plugin would reference a key the application does not hold.
