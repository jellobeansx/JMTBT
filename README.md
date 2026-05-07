# Multi-Team Battle Mod API
## Author: Jellobenes  
This plugin now exposes a stable API for both:

- BepInEx mods (C#): `MultiTeamBattle.MultiTeamBattleApi`
- RavenScript mutators: `MultiTeamBattle` wrapper (`Lua.Wrapper.WMultiTeamBattle`)

## RavenScript (Mutators)

Use logical team indices (`0..N-1`) directly.

```lua
-- team count / spectator
local n = MultiTeamBattle.GetTeamCount()
MultiTeamBattle.SetPlayerTeamIndex(-1) -- spectator

-- target extra team by index
local team3 = MultiTeamBattle.TeamFromIndex(2)
ColorScheme.SetTeamColor(team3, Color(0.2, 0.8, 0.2, 1.0))

-- blood color (separated from team color)
MultiTeamBattle.SetTeamBloodColor(2, Color(0.1, 1.0, 0.1, 1.0))
local blood = MultiTeamBattle.GetTeamBloodColor(2)

-- team info and alliances
local info = MultiTeamBattle.GetTeamInfo(2)
MultiTeamBattle.SetTeamInfo(2, info)
MultiTeamBattle.SetTeamsAllied(2, 4, true)
```

### RavenScript methods

- `GetIsActive()`
- `GetTeamCount()`
- `GetPlayerTeamIndex()`
- `SetPlayerTeamIndex(int teamIndex)` (`-1` = spectator)
- `TeamFromIndex(int teamIndex)` for existing `Team`-based APIs
- `TeamExists(int teamIndex)`
- `GetTeamName(int)`, `SetTeamName(int, string)`
- `GetTeamColor(int)`, `SetTeamColor(int, Color)`
- `GetTeamBloodColor(int)`, `SetTeamBloodColor(int, Color)`
- `SetUseRedBloodColor(int, bool)`
- `GetTeamBotCount(int)`, `SetTeamBotCount(int, int)`
- `GetTeamInfo(int)`, `SetTeamInfo(int, TeamInfo)`
- `AreTeamsAllied(int, int)`, `SetTeamsAllied(int, int, bool)`

## BepInEx C# API

Namespace: `MultiTeamBattle`

Primary type: `MultiTeamBattleApi`

### Core

- `bool IsActive`
- `int TeamCount`
- `int GetPlayerTeamIndex()`
- `bool SetPlayerTeamIndex(int teamIndex)` (`-1` = spectator)
- `bool TeamExists(int teamIndex)`
- `LogicalTeam GetLogicalTeamReference(int teamIndex)` (live object)

### Team data

- `bool TryGetTeamData(int teamIndex, out MultiTeamTeamData data)`
- `List<MultiTeamTeamData> GetAllTeamData()`
- `bool TrySetTeamData(MultiTeamTeamData data, bool updatePresentation = true)`

`MultiTeamTeamData` includes:

- `logicalIndex`
- `teamName`
- `teamColor`
- `bloodColor`
- `useRedBloodColor`
- `botCount`
- `mutatorAffinitySourceSlot`
- `rtcConfigPath`
- `allies`
- `configuredTeamInfoJson`
- `teamInfoSnapshotJson`

### Team presentation and blood

- `bool TrySetTeamName(int teamIndex, string name)`
- `bool TrySetTeamColor(int teamIndex, Color color)`
- `bool TryGetTeamBloodColor(int teamIndex, out Color bloodColor)`
- `bool TrySetTeamBloodColor(int teamIndex, Color bloodColor, bool useRedBloodColor)`

### TeamInfo and alliances

- `TeamInfo GetTeamInfo(int teamIndex)` (cloned snapshot)
- `bool TrySetTeamInfo(int teamIndex, TeamInfo teamInfo, bool updatePresentation = true)`
- `bool AreAllied(int teamA, int teamB)`
- `bool SetAlliance(int teamA, int teamB, bool allied)`

## Notes

- Scoreboard stats are now preserved when MTB rebuilds scoreboard entries.
- RavenScript `WGameInfo.GetTeamInfo/SetTeamInfo` now supports extra-team indices while MTB runtime is active
- `WColorScheme.SetTeamBloodColor` now updates MTB logical blood data instead of being overwritten
