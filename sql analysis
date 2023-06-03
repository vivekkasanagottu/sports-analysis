-- 1.Show the percentage of wins of each bidder in the order of highest to lowest percentage.
select bdr_dt.bidder_id 'Bidder ID', bdr_dt.bidder_name 'Bidder Name', 
(select count(*) from ipl_bidding_details bid_dt 
where bid_dt.bid_status = 'won' and bid_dt.bidder_id = bdr_dt.bidder_id) /
(select no_of_bids from ipl_bidder_points bdr_pt 
where bdr_pt.bidder_id = bdr_dt.bidder_id)*100 as 'Percentage of Wins (%)'
from ipl_bidder_details bdr_dt order by 3 desc;


-- 2.	Display the number of matches conducted at each stadium with the stadium name and city.
select s.stadium_id,s.stadium_name,s.city,count(m.match_id) 'total_matches' 
from ipl_stadium s inner join ipl_match_schedule m
on s.stadium_id=m.stadium_id
group by s.stadium_name,s.city,s.stadium_id
order by s.stadium_id;



-- 3.	In a given stadium, what is the percentage of wins by a team which has won the toss?
select stadium_id 'Stadium ID', stadium_name 'Stadium Name',
(select count(*) from ipl_match m join ipl_match_schedule ms on m.match_id = ms.match_id
where ms.stadium_id = s.stadium_id and (toss_winner = match_winner)) /
(select count(*) from ipl_match_schedule ms where ms.stadium_id = s.stadium_id) * 100 
as 'Percentage of Wins by teams who won the toss (%)'
from ipl_stadium s;


-- 4.	Show the total bids along with the bid team and team name.
select t.team_name,bid_team,count(bid_team) 'total bids' from ipl_bidding_details bd join  ipl_team t
on t.team_id=bd.bid_team
group by t.team_id;

-- 5.	Show the team id who won the match as per the win details.
select distinct t.team_id,m.win_details 
from ipl_team t join ipl_match m
where t.team_id=m.match_winner;

-- 6.	Display total matches played, total matches won and total matches lost by the team along with its team name.
select distinct t.team_name,tournmt_id,ts.matches_played,ts.matches_won,ts.matches_lost 
from ipl_team_standings ts join ipl_team t
on  t.team_id=ts.TEAM_ID ;


-- 7.	Display the bowlers for the Mumbai Indians team.
select * from ipl_team;
select * from ipl_team_players;
select tp.player_id,ip.player_name,tp.PLAYER_ROLE,t.team_name from ipl_team_players tp join ipl_team t
on tp.team_id=t.team_id
join ipl_player ip on ip.player_id=tp.player_id
where tp.player_role='Bowler' and t.team_name='Mumbai Indians' ;

-- 8.	How many all-rounders are there in each team, Display the teams with more than 4 all-rounders in descending order.
select t.team_name,count(tp.player_role) 'All-Rounder' 
from ipl_team_players tp join ipl_team t
on tp.team_id=t.team_id
where tp.player_role='all-rounder'
group by t.team_name
having count(tp.player_role)>4
order by tp.player_role desc;

	
-- 9.	 Write a query to get the total bidders points for each bidding status of those bidders who bid on CSK when it won the match in M. Chinnaswamy Stadium bidding year-wise.
 -- Note the total bidders’ points in descending order and the year is bidding year.
 --              Display columns: bidding status, bid date as year, total bidder’s points

select bd.bid_status as bidding_status, 
year(bd.BID_DATE) as bid_year, 
sum(bp.TOTAL_POINTS) as total_points
FROM ipl_bidding_details bd
join ipl_bidder_points bp  using(bidder_id) 
join  ipl_match_schedule ms using(schedule_id)
join ipl_match im using(match_id)
where bd.bid_team = 1 --  CSK's team ID is 1
and ms.STADIUM_ID = 7 --  M. Chinnaswamy Stadium's ID is 7
and im.MATCH_WINNER = 1 --  CSK's team ID is 1
group by  bd.BID_STATUS, year(bd.BID_DATE)
order by  total_points desc, bid_year;



/* 10.	Extract the Bowlers and All Rounders those are in the 5 highest number of wickets.
Note 
1. use the performance_dtls column from ipl_player to get the total number of wickets
 2. Do not use the limit method because it might not give appropriate results when players have the same number of wickets
3.	Do not use joins in any cases.
4.	Display the following columns teamn_name, player_name, and player_role.*/

with t1 as (select team_id,player_id,player_role from ipl_team_players),
t2 as (select team_name,team_id from ipl_team),
t3(player_id,player_name,wickets,ranks) as (select player_id,player_name,
replace(substr(replace(PERFORMANCE_DTLS,' ',''),(instr(replace(PERFORMANCE_DTLS,' ',''),'k')+3),2),'D','')+0 wickets,
dense_rank()over(order by(replace(substr(replace(PERFORMANCE_DTLS,' ',''),(instr(replace(PERFORMANCE_DTLS,' ',''),'k')+3),2),'D','')+0)  desc) ranks from ipl_player)
select team_name,player_name,player_role,wickets,ranks from t1,t2,t3 
where t2.team_id=t1.team_id and t1.player_id = t3.player_id and player_role not like '%bat%' having ranks between 1 and 5 order by ranks;


-- 11.	show the percentage of toss wins of each bidder and display the results in descending order based on the percentage
select bidder_id,
(count(case when im.toss_winner= im.match_winner then 1 end )/
count(*) )*100 as 'percentage of toss wins'
from ipl_bidder_details bd join ipl_bidding_details bdd using(bidder_id)
join ipl_match_schedule using(schedule_id)
join ipl_match im using(match_id)
group by BIDDER_ID
order by 2 desc;




-- 12.	find the IPL season which has min duration and max duration.
select tournmt_name,datediff(to_date,from_date) as duration 
from ipl_tournament where 
datediff(to_date,from_date)=(select min(datediff(to_date,from_date)) from ipl_tournament) or
datediff(to_date,from_date)=(select max(datediff(to_date,from_date)) from ipl_tournament);



/* 13.	Write a query to display to calculate the total points month-wise for the 2017 bid year.
 sort the results based on total points in descending order and month-wise in ascending order.
Note: Display the following columns:
1.	Bidder ID, 2. Bidder Name, 3. bid date as Year, 4. bid date as Month, 5. Total points
Only use joins for the above query queries.*/

select b.bidder_id,bd.bidder_name,year(b.bid_date) as bid_year,month(b.bid_date) as bid_month,sum(bp.total_points) as total_points
from ipl_bidding_details b 
join ipl_bidder_details bd using(bidder_id)
join ipl_bidder_points bp using(bidder_id)
where year(b.bid_date)=2017
group by b.bidder_id,bd.bidder_name,year(b.bid_date) ,month(b.bid_date)
order by total_points desc, bid_year asc,bid_month asc;


/*14.	Write a query for the above question using sub queries by having the same constraints as the above question.*/
select bidder_id,bidder_name,bid_year,bid_month,total_points from
(select b.bidder_id,bd.bidder_name,year(b.bid_date) as bid_year,month(b.bid_date) as bid_month,sum(bp.total_points) as total_points
from ipl_bidding_details b 
join ipl_bidder_details bd using(bidder_id)
join ipl_bidder_points bp using(bidder_id)
where year(b.bid_date)=2017
group by b.bidder_id,bd.bidder_name,year(b.bid_date) ,month(b.bid_date)
) as tab1
order by total_points desc, bid_year asc,bid_month asc;


/*15.	Write a query to get the top 3 and bottom 3 bidders based on the total bidding points for the 2018 bidding year.
Output columns should be:
like:
Bidder Id, Ranks (optional), Total points, Highest_3_Bidders --> columns contains name of bidder, Lowest_3_Bidders  --> columns contains name of bidder;*/
 select t.BIDDER_ID,t.BIDDER_NAME,Total_points,t_to_b 'Ranks(desc)',case
 when t.t_to_b <=3 then 'Highest_3_Bidders'
 when t.b_to_t <=3 then 'Lowest_3_Bidders'
 end as bidders from 
(select BIDDER_ID,BIDDER_NAME,year(bid_date) 'year',sum(total_points) Total_points, dense_rank() over(order by sum(total_points) desc) t_to_b,
dense_rank() over(order by sum(total_points) asc) b_to_t from 
ipl_bidder_details bd join ipl_bidding_details bid using(BIDDER_ID) join ipl_bidder_points bp using(bidder_id)
where year(bid_date)=2018 group by BIDDER_ID,BIDDER_NAME,year(bid_date)) t
where t.t_to_b <=3 or t.b_to_t<=3 order by Total_points desc;


/* 16.	Create two tables called Student_details and Student_details_backup.

Table 1: Attributes 		Table 2: Attributes
Student id, Student name, mail id, mobile no.	Student id, student name, mail id, mobile no.

Feel free to add more columns the above one is just an example schema.
Assume you are working in an Ed-tech company namely Great Learning where you will be inserting and modifying the details of the students in the Student details table. Every time the students changed their details like mobile number,
 You need to update their details in the student details table.  Here is one thing you should ensure whenever the new students' details come , you should also store them in the Student backup table so that if you modify the details in the student details table, you will be having the old details safely.
You need not insert the records separately into both tables rather 
Create a trigger in such a way that It should insert the details into the Student back table when you inserted the student details into the student table automatically.*/



-- creating database greatlearning
create database greatlearning;
use greatlearning;

-- Create Student_details table
create table  Student_detailsss (
    student_id INT PRIMARY KEY,
    student_name VARCHAR(50),
    mail_id VARCHAR(100),
    mobile_no VARCHAR(20));

-- Create Student_details_backup table
create table Student_details_backupp (
    student_id INT,
    student_name VARCHAR(50),
    mail_id VARCHAR(100),
    mobile_no VARCHAR(20),
    backup_date datetime default current_timestamp);
    

-- Create trigger to insert new or updated student details into backup table

DELIMITER //
CREATE TRIGGER insert_student_details_trigger
AFTER INSERT ON Student_detailsss
FOR EACH ROW
BEGIN
    INSERT INTO Student_details_backup (Student_id, Student_name, mail_id, mobile_no)
    VALUES (NEW.Student_id, NEW.Student_name, NEW.mail_id, NEW.mobile_no);
END //
DELIMITER ;


