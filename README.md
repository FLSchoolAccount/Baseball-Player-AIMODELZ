Baseball-Player-AIMODELZ
ML-powered OVR/potential ratings for MLB players, plus next-season stat and salary projections, built from the Lahman Baseball Database.
Overview
This project turns historical MLB stats into video-game-style player ratings. It computes a heuristic Formula OVR for hitters and pitchers from real season stats (OBP, SLG, ERA, strikeout rate, etc.), then trains RandomForestRegressor models on years of player-seasons to predict:
•	Current-season OVR rating
•	Next-season Projected OVR
•	Long-term Potential (best OVR over the next few seasons)
•	Next-season counting/rate stats (hits, home runs, BA, OBP, SLG, OPS)
•	Pitcher/hitter salary estimates and contract-length projections
The idea is to answer: given how a player performed this year, how good are they right now, and how good could they become?
Data Source
Built on the Lahman Baseball Database, specifically:
•	Batting.csv — season batting stats per player/team
•	Pitching.csv — season pitching stats per player/team
•	Fielding.csv — fielding stats, used for primary position and defensive stats
•	People.csv — player biographical info (used to calculate age and full names)
•	Salaries.csv — historical player salaries
The dataset covers MLB seasons from 1990 onward. 2020 is excluded from hitter models because the shortened 60-game season distorts rate and counting stats.
How It Works
1. Stat aggregation
battingStats() and pitchingStats() load the raw CSVs, filter to a given year, combine stats for players traded mid-season (removing duplicate "TOT" rows), merge in age and primary position, and compute derived stats (BA, OBP, SLG, OPS, ERA, opponent batting average).
2. Formula OVR (heuristic baseline)
A weighted formula converts real stats into a 70–99 overall rating:
•	Hitters: weighted combination of OBP, SLG, HR rate, hits, walks, strikeouts, and fielding.
•	Pitchers: weighted combination of ERA, strikeout rate, opponent BA, wins/saves, and role (starter vs. reliever).
This heuristic rating is the training target for the machine learning models below — it gives every historical player-season a consistent label to learn from.
3. Machine learning models
Using RandomForestRegressor (scikit-learn), the project trains separate models per prediction target:
•	OVR model — predicts current Formula OVR from the previous season's stats.
•	Projected OVR model — predicts next season's Formula OVR.
•	Potential model — predicts the highest Formula OVR a player reaches within the next 3 seasons.
•	Next-season stat model — predicts next year's H, HR, BA, OBP, SLG, and OPS directly.
•	Salary model — predicts next season's salary using performance, league salary inflation, and current OVR/Potential.
•	Contract-length model — estimates how many more consecutive seasons a player stays under contract.
Each model is retrained per target year on historical player-seasons before/after it, so predictions reflect only information available up to that point.
4. Interactive dashboard
Built with ipywidgets inside Google Colab:
•	Year and team dropdowns to build hitter/pitcher rating tables on demand.
•	OVR and Potential distribution histograms.
•	Player-specific OVR progression charts (history + next-season projection).
•	Salary and contract-length estimate tables.
Getting Started
This project is built and run in Google Colab.
1.	Open the notebook in Colab.
2.	Run the first cell and upload Batting.csv, Pitching.csv, Fielding.csv, People.csv, and Salaries.csv from the Lahman Database when prompted.
3.	Run all cells in order — later cells depend on functions and dataframes defined earlier (battingStats, pitchingStats, hittingStats, the Formula OVR functions, and the trained RandomForest models).
4.	Use the dropdown widgets that appear under each @title cell to select a year (and team, for hitters) and generate ratings, projections, or salary tables.
Dependencies
•	pandas, numpy
•	scikit-learn (RandomForestRegressor)
•	matplotlib, seaborn
•	ipywidgets
•	Google Colab (google.colab.files, google.colab.data_table)
Limitations
•	Formula OVR is a custom heuristic, not an official MLB or video-game rating — it reflects the weighting choices made in this project, not a validated industry standard.
•	Salary predictions are estimates based on historical pay patterns and league-wide inflation; they don't account for arbitration rules, free agency timing, or team payroll constraints.
•	The 2020 season is excluded from hitter analysis due to its shortened schedule.
•	Model accuracy depends on how many historical seasons are available near the target year — very recent or very old years have less training data.
Credits
•	Data: Lahman Baseball Database, maintained by Sean Lahman and SABR.
•	Project built and maintained by Felix Li.
License
Add a license of your choice (for example, MIT) if you plan to let others reuse or modify this code.
