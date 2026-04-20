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

### Result Reframing
A key issue in the original data is that both `FT Result` and `HT Result` are recorded from the **home team perspective**.

For the AWAY table, this had to be inverted using conditional logic:

powerquery
if [FT Result] = "H" then "A" 
else if [FT Result] = "A" then "H" 
else "D"

The same logic was applied to `HT Result`.

This ensured that:

- a home win becomes a loss from the away team perspective,
- an away win becomes a win from the away team perspective,
- draws remain unchanged.

### Final Analytical Table

After both tables were standardised, they were appended into a single dataset.

This final structure ensured that:

- both tables shared the same final columns,
- each row represented **one team in one match**,
- home and away performances could be analysed together.

---

## Derived Columns and Measures

### Results Column

A new categorical result variable was created in **W / D / L** format:

- **W** – Win  
- **D** – Draw  
- **L** – Loss  

This column was used throughout the dashboard for result distribution and recent form analysis.

### Results Points

A new variable called **Results Points** was created based on the result logic:

- **Win** → 3 points  
- **Draw** → 1 point  
- **Loss** → 0 points  

The purpose of this variable was to make season-level analysis more intuitive and to reproduce the logic of a real football league table.

### Goal Difference

A dedicated measure was created to calculate goal difference:

DAX: Goal Difference = SUM('England 2 FULL'[Goals]) - SUM('England 2 FULL'[Goals Conceded])

### Score Column

A new score column was created for tooltip purposes by concatenating goals scored and goals conceded:

DAX: Score = FORMAT('England 2 FULL'[Goals], "0") & "-" & FORMAT('England 2 FULL'[Goals Conceded], "0")

Example:
- **4-1**

---
## Dashboard Structure

To improve navigation, a **homepage** was created with three main buttons:

- **Team Stats**
- **Season Stats**
- **Referee Stats**

Each button redirects the user to its respective analysis page.

Additionally, every page includes a smaller **return button** in the upper-right corner that redirects back to the homepage. This smaller button was intentionally chosen to preserve space for the main visuals and overall page design.

---

## Team Stats

The **Team Stats** page was designed to support detailed analysis of a selected club.

### Filters

Two slicers were included:

- a **Team slicer**, restricted to a single team selection,
- a **Time slicer**, allowing the user to define the analysis window.

### Visuals

This page includes:

- a **line chart** showing the evolution of:
  - goals scored,
  - goals conceded;

- a **100% stacked chart** showing the proportion between:
  - goals scored,
  - goals conceded;

- a **pie chart** displaying the distribution of:
  - wins,
  - draws,
  - losses;

- a **Top 5 opponents chart** for:
  - teams against whom the selected team scored the most goals;

- a **Top 5 opponents chart** for:
  - teams against whom the selected team conceded the most goals.

### Summary Card

A summary card was created to display key team indicators:

- total shots,
- shots on target,
- red cards,
- yellow cards.

---

## Season Stats

The **Season Stats** page was designed to provide a broader competition-level view by season.

### Filter

This page includes a **Season slicer**.

### Main League Table

The first table presents, for each team:

- team name,
- goals scored,
- goals conceded,
- goal difference,
- points.

Teams are sorted from **highest to lowest points**, reproducing a realistic league table structure.

This table also includes **embedded bar visuals inside cells**, improving the visual comparison of performance across teams.

### Match Results Table

A second table was created to display:

- date,
- team,
- result,

sorted from **most recent to oldest**.

This table is interactive: selecting a team filters the remaining visuals accordingly.

### Tooltip Page

A dedicated **tooltip page** was created and linked to the first season table.

This tooltip includes:

- a **team card**,
- a table with the **5 most recent matches**,
- opponent,
- result,
- score in `Goals-Goals Conceded` format.

This feature allows users to hover over a team row and immediately inspect its most recent performances without occupying additional space in the main layout.

---

## Referee Stats

The **Referee Stats** page was designed to analyse referee behaviour and disciplinary tendencies.

### Filters

This page includes:

- a **Referee slicer**,
- a **Time slicer**.

### Main Cards

A card was created to show the total number of officiated matches using:

DAX: Number of Games Refereed = COUNTROWS('England 2 FULL')

Additional cards were also created for:

- total yellow cards,
- total red cards.

### Referee Measures

To reduce bias caused by unequal sample sizes across teams, the referee analysis was based on **average cards per match**, rather than raw totals alone.

The following measures were created:
- DAX: Number of Games Refereed = COUNTROWS('England 2 FULL')
- DAX: Total Red Cards = SUM('England 2 FULL'[Red Cards])
- DAX: Matches by Team = COUNTROWS('England 2 FULL')
- DAX: Average Red Cards = DIVIDE([Total Red Cards], [Matches by Team])
- DAX: Total Yellow Cards = SUM('England 2 FULL'[Yellow Cards])
- DAX: Average Yellow Cards = DIVIDE([Total Yellow Cards], [Matches by Team])
- DAX: Average Total Cards = [Average Yellow Cards] + [Average Red Cards]
- DAX: Eligible Team = IF([Matches by Team] >= 3, 1, 0)

The `Eligible Team` measure was created to avoid misleading comparisons based on very small samples.

### Main Visuals

Using these measures, three main bar charts were created:

- **Top 5 clubs by average red cards**
- **Top 5 clubs by average yellow cards**
- **Top 5 clubs by average total cards**

The page also includes:

- a **stacked chart** showing the distribution between yellow and red cards,
- a **line chart over time** displaying:
  - number of games refereed,
  - yellow cards,
  - red cards.

This makes it possible to analyse both overall disciplinary patterns and referee behaviour over time.

---

## Limitations and Future Improvements

Although the dashboard already provides a strong level of analysis across teams, seasons, and referees, several improvements could be implemented with access to richer datasets.

Most detailed football data sources are currently paid and are typically more accessible in professional contexts. Ideally, it would be possible to build an additional **player analytics page** if player-level data were available by match, month, or division.

This would open the door to more advanced analyses such as:

- player performance against specific clubs,
- player evolution across seasons,
- comparisons across divisions,
- more detailed individual trends over time.

Because this project focuses on the **English second division**, the availability of complete and detailed data is more limited than for top-tier leagues. Even so, the final dashboard demonstrates a solid analytical and interactive framework.

---

## Dashboard Walkthrough

In the video below, I present the dashboard, explain the reasoning behind each analytical page, and demonstrate the main interactive features and insights.

<!-- Add video link or embedded preview here -->

You can download the dashboard here: [Download Dashboard](https://github.com/JoaoSaraiva99/EFL-Championship-Performance-Analytics-Dashboard/blob/main/Second%20League%20Dashboard.pbix)
