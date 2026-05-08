# Multi-Team Battle Mod API
## Author: Jellobenes

- BepInEx mods (C#): `MultiTeamBattle.MultiTeamBattleApi`
- RavenScript mutators: `MultiTeamBattle` wrapper (`Lua.Wrapper.WMultiTeamBattle`)

## RavenScript (Mutators)

Use logical team indices (`0..N-1`)

```lua
-- team count / player team
local n = MultiTeamBattle.GetTeamCount()
MultiTeamBattle.SetPlayerTeamIndex(2)

-- target extra team using index (eagle 0, raven 1, first extra team 2, and so on)
local team3 = MultiTeamBattle.TeamFromIndex(2)
ColorScheme.SetTeamColor(team3, Color(0.2, 0.8, 0.2, 1.0))

-- mutator helper calls that take logical team index directly
local bots = MultiTeamBattle.GetNumberOfBotsInTeamIndex(2)
local spawn = MultiTeamBattle.RandomSpawnPointForTeamIndex(2)
local actor = MultiTeamBattle.CreateAIActorOnTeamIndex(3)

-- blood color (separated from team skin/flags color)
MultiTeamBattle.SetTeamBloodColor(2, Color(0.1, 1.0, 0.1, 1.0))
local blood = MultiTeamBattle.GetTeamBloodColor(2)

-- team info and alliances
local info = MultiTeamBattle.GetTeamInfo(2)
MultiTeamBattle.SetTeamInfo(2, info)
MultiTeamBattle.SetTeamsAllied(2, 4, true)

-- mutator-affinity routing for extra teams
MultiTeamBattle.SetTeamMutatorAffinitySourceSlot(4, 0) -- follow EAGLE mutators
MultiTeamBattle.SetTeamMutatorAffinitySourceSlot(5, 2) -- affected by RS Mutator
```

### RavenScript methods

- `GetIsActive()`
- `GetTeamCount()`
- `GetPlayerTeamIndex()`
- `SetPlayerTeamIndex(int teamIndex)`
- `TeamFromIndex(int teamIndex)` for existing `Team`-based APIs
- `GetTeamMutatorAffinitySourceSlot(int teamIndex)`
- `SetTeamMutatorAffinitySourceSlot(int teamIndex, int sourceSlot)` (`-1`, `0`, `1`, `2`)
- `TeamExists(int teamIndex)`
- `GetActorsOnTeamIndex(int)`, `GetAliveActorsOnTeamIndex(int)`
- `GetNumberOfBotsInTeamIndex(int)`, `IsTeamDeadByIndex(int)`
- `GetSpawnPointsOwnedByTeamIndex(int)`, `GetCapturePointsOwnedByTeamIndex(int)`
- `TeamHasAnySpawnPointByIndex(int)`
- `RandomSpawnPointForTeamIndex(int)`, `RandomFrontlineSpawnPointForTeamIndex(int)`, `RandomEnemySpawnPointForTeamIndex(int)`
- `CreateAIActorOnTeamIndex(int)`, `GetSpawnableAIActorOnTeamIndex(int)`
- `SpawnVehicleForTeamIndex(int, VehicleSpawnType, Vector3, Quaternion)`
- `SpawnTurretForTeamIndex(int, TurretSpawnType, Vector3, Quaternion)`
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
- `bool SetPlayerTeamIndex(int teamIndex)`
- `bool TeamExists(int teamIndex)`
- `LogicalTeam GetLogicalTeamReference(int teamIndex)` (live object)
- `int GetMutatorAffinitySourceSlot(int teamIndex)`
- `bool SetMutatorAffinitySourceSlot(int teamIndex, int sourceSlot, bool refreshRuntime = true)`

Mutator affinity slots:

- `-1`: affected by NONE mutators
- `0`: affected by EAGLE mutators
- `1`: affected by RAVEN mutators
- `2`: affected by RS Mutator

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

- RavenScript `WGameInfo.GetTeamInfo/SetTeamInfo` supports extra team indices
- `WColorScheme.SetTeamBloodColor` updates MTB blood data independent of team colors
