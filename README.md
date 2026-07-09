# NBA Player Archetype Classifier — CSUF Data Science 2026

A rule-based data science project that classifies every NBA player in the 2025–26 season into a primary and secondary **playing archetype** using per-game and advanced statistics. The results are exported as player-level and team-level CSV files and can be queried interactively by team.

---

## What It Does

The notebook takes NBA player statistics, normalizes and merges multiple stat sheets (per-game, advanced, totals), then scores each player against ten basketball archetypes using a weighted formula. Each player receives the top two matching archetypes, and those assignments are aggregated across all 30 NBA rosters.

---

## Archetypes

| Archetype | Key Stats Weighted |
|---|---|
| **Playmaker** | AST, USG%, AST/TOV ratio, PER |
| **Scorer** | PTS, USG%, 3P%, FG% |
| **Ball_Handler** | AST, PTS, 3P%, USG% |
| **Slasher** | PTS, TRB, STL, FG% |
| **Three_Level_Shooter** | 3P%, FG%, FT%, PTS |
| **Interior_Post** | TRB, BLK, FG%, VORP |
| **Defensive_Anchor** | BLK, TRB, STL, BPM |
| **Versatile_Forward** | TRB, PTS, AST, STL |
| **Two_Way_Wing** | STL, BLK, PTS, 3P% |
| **Facilitator** | AST, USG%, AST/TOV, BPM |

Each archetype receives a composite score based on its formula. The highest-scoring archetype becomes the player's **Primary Archetype** and the second-highest becomes the **Secondary Archetype**.

---

## Algorithm

### 1. Data Loading (`Cell 0`)
- Tries multiple file paths to locate CSVs (local, Google Colab `/content/`, relative paths, subdirectories).
- Falls back to a 10-player hardcoded sample if no files are found, so the notebook runs end-to-end without any data.

### 2. Data Standardization (`Cell 2`)
- Detects the player name column regardless of case or naming convention.
- Renames stat columns to a canonical schema (`PTS`, `AST`, `TRB`, `STL`, `BLK`, `FG%`, `3P%`, `FT%`).
- Merges in advanced metrics (`VORP`, `BPM`, `WS`, `USG%`) from a separate Advanced CSV when available.
- Merges per-game stats from a "Player Per Game" sheet when available.
- Fills missing values with sensible league-average defaults so no player is left unclassified.

### 3. Archetype Scoring (`archetype_scores` function)
Each player row is passed through a scoring function that returns a dictionary of ten scores:

```python
'Scorer':    pts * 1.4 + usg * 0.9 + tp * 2.4 + fg * 1.4
'Playmaker': ast * 1.6 + usg * 0.8 + ast_tov * 1.4 + per * 0.4
# ... and so on for all ten archetypes
```

The two highest-scoring archetypes are assigned as Primary and Secondary.

### 4. Roster Assembly
- Looks for a team–player mapping in the loaded CSVs.
- If none is found, falls back to a hardcoded 30-team roster dictionary with ~15 players per team.
- Players not found in the scored stats table get a name-based heuristic fallback (e.g., "Gobert" → Interior_Post).
- The final roster table is sorted alphabetically by team and player name.

### 5. Team Lookup (`Cell 3`)
- Set `team_abbreviation` (e.g., `'LAL'`, `'GSW'`) to filter and display the full archetype roster for any team.
- Reads from `team_roster_archetypes.csv` if it exists, otherwise uses the in-memory table.

---

## Output Files

| File | Description |
|---|---|
| `player_archetypes.csv` | One row per player: `Player`, `Primary_Archetype`, `Secondary_Archetype`, plus raw scores for all ten archetypes |
| `team_roster_archetypes.csv` | One row per team–player pair: `Team`, `Player`, `Primary_Archetype`, `Secondary_Archetype` |

---

## Project Structure

```
data_science_projectCSUF-1/
├── Data_Science_2026.ipynb       # Main notebook
├── nba_player_stats_2026.csv     # Primary stats dataset (2025-26 season)
├── player_archetypes.csv         # Output: per-player archetype results
├── team_roster_archetypes.csv    # Output: per-team roster with archetypes
└── README.md
```

---

## Requirements

```
pandas
numpy
```

No ML libraries are required — the classifier is fully deterministic and rule-based.

---

## Usage

1. Open `Data_Science_2026.ipynb` in Jupyter or Google Colab.
2. Place `nba_player_stats_2026.csv` in the same directory (or let it fall back to the built-in sample).
3. Run all cells in order.
4. To look up a specific team, change `team_abbreviation` in Cell 3 to any valid NBA abbreviation (e.g., `'OKC'`, `'BOS'`, `'MIA'`).

---

## Supported Team Abbreviations

`ATL` `BOS` `BKN` `CHA` `CHI` `CLE` `DAL` `DEN` `DET` `GSW` `HOU` `IND` `LAC` `LAL` `MEM` `MIA` `MIL` `MIN` `NOP` `NYK` `OKC` `ORL` `PHI` `PHX` `POR` `SAC` `SAS` `TOR` `UTA` `WAS`

---

## Authors
John Yohannan

For: CSUF Data Science — Summer 2026
