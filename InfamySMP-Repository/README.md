# InfamySMP

A Paper plugin for Minecraft 1.21.11, built for Java 21. It tracks player-kill reputation, titles, effects, and transferable Infamy Shards.

## Requirements

- Paper 1.21.11 (or a compatible 1.21.x server)
- Java 21 for both the server and build
- Apache Maven 3.9 or newer to compile

Paper 26.1 and newer require Java 25; this project intentionally targets the 1.21.11 line so it runs on Java 21.

## Build

### Build without installing Java or Maven on your Windows 8 PC

The included GitHub Actions workflow builds the jar using Java 21 in GitHub's hosted build environment. Create a new empty GitHub repository (do not initialize it with a README or other files), then upload the contents of this project ZIP into the repository root, including `.github/workflows/build.yml`. Commit the upload to the `main` branch. The build starts automatically. When it completes, open the successful run under **Actions** and download the `InfamySMP-Paper-1.21.11` artifact. Extract `InfamySMP.jar` from that downloaded ZIP; upload that jar to your server.

### Build locally

Open a terminal in this directory and run:

```text
mvn clean package
```

The plugin jar is created at `target/InfamySMP.jar`. Copy it into the server's `plugins` folder and restart the server. On first startup, the plugin creates `plugins/InfamySMP/config.yml`.

## Install on a hosted Paper server

In the server host's file manager, open or create the `plugins` folder and upload `InfamySMP.jar`. Restart the server (a full stop and start, not `/reload`). On startup, check the console for `InfamySMP` and confirm `plugins/InfamySMP/config.yml` was created. No client-side mod is needed.

## Commands

- `/infamy` — show your Infamy, title, and score
- `/infamy <player>` — show a known player's score
- `/infamy leaderboard` — list the top players (configurable, 10 by default)
- `/infamy withdraw <amount>` — exchange positive Infamy for a physical shard
- `/infamy set <player> <score>` — set a known player's score to a whole number from -10 to 10 (admin only)
- `/infamy reset <player>` — set a known player's score back to 0 (admin only)

The `infamy.use` permission defaults to everyone and controls personal score viewing and withdrawal. Score lookup and the leaderboard are available to all senders. The `infamy.admin` permission defaults to server operators and controls the set/reset commands.

## Rules and behavior

- Players start at 0. A credited player kill adds 1 to the killer and removes 1 from the victim. Both values clamp to -10..+10. Environmental deaths do not change Infamy; normal Minecraft kill credit determines the killer.
- The title bands and names match the concept. A public announcement is sent when a score change crosses into another title band.
- At +5 players receive Strength I and Speed I, at +8 Resistance I, and at +10 Haste I. Effects are refreshed on join, after respawn, and after Infamy changes. The plugin removes and reapplies these four effect types while refreshing, so other sources of these same effects are replaced while a player has Infamy rewards.
- Titles appear in chat, the player list, and the overhead nametag. Overhead prefixes use the main server scoreboard; another plugin that also manages the main scoreboard may conflict with nametag formatting.
- Withdrawals require a positive whole number no greater than the player's current positive score. One uniquely tagged shard is created for each withdrawal. Shards use persistent item data, so changing the name or lore does not forge one. Right-click deposits as much of the shard value as fits below +10. Any remainder stays on the shard. At +10, the shard is left untouched.
- Shards are ordinary inventory items and follow the server's normal death-drop rules. Players can store them in Ender Chests, trade them, or drop them. A credited PvP kill always gives the killer +1 Infamy, clamped to the -10..+10 range, and removes 1 from the victim. At -10, a killer rises to -9 as normal. Once a killer is already at +10, each further credited PvP kill drops the otherwise-capped +1 point as a shard at the victim's death location; the killer remains at +10.
- Scores and last known player names are stored in the plugin's `config.yml` and saved after score changes and during shutdown.

## Configuration

`plugins/InfamySMP/config.yml` controls the leaderboard length, milestone announcements, and reward thresholds. The intended values are 5, 8, and 10. Restart the server after editing the configuration.

## Source layout

```text
pom.xml
src/main/java/dev/infamy/smp/InfamyPlugin.java
src/main/resources/plugin.yml
src/main/resources/config.yml
```
