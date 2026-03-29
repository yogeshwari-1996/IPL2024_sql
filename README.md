# IPL2024_sql
This project analyzes IPL 2024 match data using SQL to uncover insights related to team performance, match outcomes, and competitive matches.


Create Database IPL_project;

CREATE TABLE matches (
    id INT,
    date TEXT,
    team1 TEXT,
    team2 TEXT,
    toss_winner TEXT,
    toss_decision TEXT,
    score1 INT,
    wickets1 INT,
    score2 INT,
    wickets2 INT,
    winner TEXT,
    player_of_match TEXT,
    player2 TEXT,
    venue TEXT
);

Select * from matches;

Select team1, team2, winner
from matches;

select count(*) as total_matches
from matches;

---Matches won by each Team----
select winner, count(*) as wins
from matches
group by winner
order by wins Desc;


----matches per venue---
select venue, count(*) as matches
from matches
group by venue 
order by matches desc;


--Player of the match----
select player_of_match, count(*) awards
from matches
group by player_of_match
order by awards desc;


-----Toss decison analysis---
select toss_decision, count(*) as count
from matches
group by toss_decision;


--High scoring matches---
select team1, team2, score1, score2
from matches
where score1>180 or score2>180;


---Total Matches played by each team---
select team, count(*) as matches_played
from (
select team1 as team from matches
union all
select team2 from matches
)t
group by team;


---Win Percentage--
select winner,
count(*)as wins,
round(count(*)*100/sum(count(*))over(),2) as win_percentage
from matches
group by winner;


--Ranking Team----
select winner,
count(*) as wins,
rank() over (order by count(*) desc) as rank
from matches
group by winner;


--close matches---
select *, abs(score1-score2) as margin
from matches
order by margin asc
limit 5;


-- Case when---
select team1, team2,
case
when score1> score2 then team1
when score2> score1 then team2
else 'Tie'
end as calculated_winner
from matches;


---Team Performance View---
create view Team_stats as
select team,
count(*) as matches_played,
sum(case when team=winner then 1 else 0 end) as wins
from(
select team1 as team, winner from matches
union all
select team2,winner from matches
)t
Group by team;


SELECT * FROM team_stats;


--View---
select *, round(wins*100.0 /matches_played, 2)as win_percentage
from team_stats;

---Toss impacted----
select toss_decision,
count(*) as matches,
sum(case when toss_winner = winner then 1 else 0 end) as wins
from matches
group by toss_decision;
