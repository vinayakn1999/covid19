# covid19



----	First Vaccination Drive Conducted By: Identified the first country to start the vaccination campaign using SQL queries.

with combined as
(SELECT a.iso_code,a.location,a.date,a.new_cases,a.total_cases,a.total_deaths, b.total_vaccinations, b.total_vaccinations_per_hundred
FROM cd23 a
JOIN cv23 b ON a.iso_code = b.iso_code AND a.date = b.date)	select * from combined 
where date >= '2020-12-13' and total_vaccinations is not null order by date,location;

----	Mortality Rate: Calculated mortality rate as a ratio of deaths to cases.

select distinct location,date,total_deaths,total_cases,avg(round(total_deaths/total_cases*100,2)) mortality_rate 
from Cd23 where total_deaths/total_cases*100 is not null group by location,date,total_deaths,total_cases ;


----	Reaching Milestones in Vaccination by India

with combined as
(SELECT a.iso_code,a.location,a.date,a.new_cases,a.total_cases,a.total_deaths, b.total_vaccinations, b.total_vaccinations_per_hundred
FROM cd23 a
JOIN cv23 b ON a.iso_code = b.iso_code AND a.date = b.date)	select * from combined where total_vaccinations_per_hundred >=50 and 
location='india' order by date,location;


----	Global milestones for vaccination rates.

with combined as
(SELECT a.iso_code,a.location,a.date,a.new_cases,a.total_cases,a.total_deaths, b.total_vaccinations, b.total_vaccinations_per_hundred
FROM cd23 a
JOIN cv23 b ON a.iso_code = b.iso_code AND a.date = b.date)	select * from combined where total_vaccinations_per_hundred >=50  order by date,location;


----	Weekly Average of Cases and Deaths: Used rolling 7-day averages.

select iso_code,location,date,new_cases, avg(new_cases) over(partition by location order by date rows between 6 preceding and current row) seven_days_average_of_cases,
avg(new_deaths) over(partition by location order by date rows between 6 preceding and current row) seven_days_average_of_deaths from cd23 
order by location,date;


----	Countries with Case and Vaccination Data Available: Filtered for countries with complete datasets.

select count(distinct cd.iso_code) countries_which_have_both_covid_data_and_vaccination_data from cd23 cd 
inner join cv23 cv 
on cd.iso_code = cv.iso_code and cd.date = cv.date;


----	Total Vaccination Per Capita for India: Calculated as vaccinations per million population.

select cv.location, sum(cv.total_vaccinations) as Total_Vaccination,
sum(distinct cd.population) as total_Population, 
sum(cv.total_vaccinations)*1.0/sum (cd.population) as Vaccination_per_capita
from cv23 cv 
inner join cd23 cd on cv.iso_code=cd.iso_code and cv.date=cd.date where cv.total_vaccinations is not null and 
cd.population is not null and cv.location='india' group by cv.location 
order by Vaccination_per_capita desc;
