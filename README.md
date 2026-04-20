# EFL Championship Performance Analytics Dashboard

## Background and Overview
This project presents an interactive Power BI dashboard built to analyse the **EFL Championship** using match data from the **last 22 years**. The goal was to create a structured analytical environment capable of exploring performance from three complementary perspectives: **teams**, **seasons**, and **referees**.

The original dataset was organised at match level, with separate variables for home and away teams. While this structure is useful for recording results, it is not ideal for team-level analysis. To solve this, the data was reshaped so that the final analytical table represents **one team in one match per row**, making it possible to aggregate performance consistently across both home and away games.

---

## Data Structure Overview

The original dataset includes the following variables:

- **Date** – Match date  
- **Season** – Season identifier (e.g., 2022/2023)  
- **HomeTeam** – Home team  
- **AwayTeam** – Away team  
- **FTH Goals** – Full-time goals scored by the home team  
- **FTA Goals** – Full-time goals scored by the away team  
- **FT Result** – Full-time result (`H`, `D`, `A`)  
- **HTH Goals** – Half-time goals scored by the home team  
- **HTA Goals** – Half-time goals scored by the away team  
- **HT Result** – Half-time result (`H`, `D`, `A`)  
- **Referee** – Match referee  
- **H Shots / A Shots** – Total shots  
- **H SOT / A SOT** – Shots on target  
- **H Fouls / A Fouls** – Fouls committed  
- **H Corners / A Corners** – Corners won  
- **H Yellow / A Yellow** – Yellow cards  
- **H Red / A Red** – Red cards  
- **Display_Order** – Technical match index  
- **League** – Competition name  

---

## Data Preparation and Transformation

### Initial Restructuring
The original table was duplicated into two separate queries:

- **HOME**
- **AWAY**

The purpose of this split was to isolate each team perspective before recombining them into a cleaner analytical structure.

### HOME Table Transformations
The HOME table was transformed so that all variables represented the home team perspective:

- `HomeTeam` → `Team`
- `AwayTeam` → `Opponent`
- `FTH Goals` → `Goals`
- `FTA Goals` → `Goals Conceded`
- `HTH Goals` → `HT Goals`
- `HTA Goals` → `HT Goals Conceded`
- `H Shots` → `Shots`
- `H SOT` → `Shots on Target`
- `H Fouls` → `Fouls`
- `H Corners` → `Corners`
- `H Yellow` → `Yellow Cards`
- `H Red` → `Red Cards`

All away-side performance variables were removed from this query.

### AWAY Table Transformations
The AWAY table was reshaped to reflect the away team perspective:

- `AwayTeam` → `Team`
- `HomeTeam` → `Opponent`
- `FTA Goals` → `Goals`
- `FTH Goals` → `Goals Conceded`
- `HTA Goals` → `HT Goals`
- `HTH Goals` → `HT Goals Conceded`
- `A Shots` → `Shots`
- `A SOT` → `Shots on Target`
- `A Fouls` → `Fouls`
- `A Corners` → `Corners`
- `A Yellow` → `Yellow Cards`
- `A Red` → `Red Cards`

All home-side performance variables were removed from this query.
