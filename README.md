🌆 Smart City Analytics – Traffic, Energy
& Pollution Dashboard
		🎯 Project Objective
		Students will create an interactive Power BI Dashboard to analyze urban traffic patterns,
		energy consumption, and pollution levels. This will help city administrators make
		data-driven decisions to improve urban living conditions.
		
📂 Dataset Overview – "Smart_City_Data.xlsx"

💡 This dataset contains real-world-inspired data covering multiple aspects of a smart city.
	📅 Session Breakdown
	📌 Session 1: Understanding the Dataset & Project Goals
	🔹 Explain project scope – Why Smart City Analytics is important?
	🔹 Introduce dataset structure (Traffic, Energy, and Pollution data).
	🔹 Discuss key insights expected from the dashboard.
	🔹 Task: Students explore the dataset in Excel before importing it into Power BI.
	
📌 Session 2: Importing & Preparing Data in Power BI
	🔹 Load the dataset into Power BI.
	🔹 Check for missing values and correct data types.
	🔹 Create calculated columns where necessary.
	🔹 Task: Perform data cleaning and transformations.
	
📌 Session 3: Traffic Insights Visualization
	🔹 Create a heatmap of Traffic_Congestion_Level by Time and City_Zone.
	🔹 Build a line chart to track Avg_Speed_Kmph over time.
	🔹 Task: Add interactivity by using slicers (e.g., Filter by Time or City Zone).
	
📌 Session 4: Energy Consumption Analysis
	🔹 Visualize Energy_Consumed_MWh in a bar chart.
	🔹 Compare Household_Consumption_% vs Industrial_Consumption_% in a
	    stacked column chart.
	🔹 Create KPI cards to highlight key metrics (Total Energy, Renewable %).
	🔹 Task: Add filters and slicers for dynamic data views.
	
📌 Session 5: Pollution & Air Quality Dashboard
	🔹 Use a map visualization to display AQI values by City Zone.
	🔹 Track PM2.5_Level and PM10_Level trends in a line chart.
	🔹 Create a gauge chart to display CO2_Emissions_Tons with warning levels.
	🔹 Task: Format visuals for clarity and impact.
📌 Session 6: Dashboard Finalization & Presentation
	🔹 Optimize visuals & layout for a professional look.
	🔹 Ensure all filters, slicers, and interactions work smoothly.
	🔹 Task: Students present their dashboards, explaining key insights.
	👏 Keep exploring, keep learning, and keep building amazing dashboards! �





-- ************************************************* Smart City Analysis ************************************************************************* 

--  Create schema

-- These is part of DDL(Data Defination Language)


Create Database Smart_city;

use Smart_city;


Drop table if exists Traffic;

	Create table Accident(
			Date	datetime,
			Time	varchar(100),
			City_Zone	varchar(50),
			Vehicle_Count int,	
			Traffic_Congestion_Level	varchar(100),
			Avg_Speed_Kmph	int,
			Accident_Count int,
			Accident_Level varchar(100)
		);

	Create table Consumption(
			Date datetime,	
			City_Zone	varchar(100),
			Energy_Consumed_MWh int,
			Household_Consumption_percent	 int,
			Industrial_Consumption_percent	 int,
			Renewable_Energy_percent int
		);



	Create table Pollution(
			Date	datetime,
			City_Zone varchar(100),
			PM_2_5_Level float,
			PM_10_Level int,
			CO2_Emissions_Tons int,
			AQI	int,
			AQI_Category varchar(100)
		);

-- After the creating the schema then we need to import the data into the tables.


	  Select * from Accident;
	
	  Select * from Pollution;
	
	 Select * from consumption;




-- Basic EDA(Exploratory Data Analysis)


-- Q. Monthly Total_Accident


	With Monthly_Accident as 
	(
	Select 
	Month (Date) as Monthly_Accident,
	sum(Accident_count) as Total_Accident
	from Accident
	group by Monthly_Accident
	)
	   Select 
	   Monthly_Accident,
	   Total_Accident,
	   Dense_rank() over( order by Total_Accident desc) Ranks
	   from Monthly_Accident;

-- Analysis Which City zone has the highest Accident According to Traffic_congestion_level

	With Traffic_Congestion as 
	(	Select 
		Traffic_congestion_level,
		City_Zone,
		sum(Accident_Count) as Total_Accident
		from Accident
		group by Traffic_congestion_level,City_Zone
		order by Traffic_congestion_level ,Total_Accident desc
	)
		Select *, 
		Rank() over(partition by Traffic_congestion_level order by Total_Accident desc) as Ranks
		from Traffic_Congestion;

-- Evaluate Total_no_Vehicle  are affecting  the Accident happened.

	Select Time,
	sum(Vehicle_count) as Total_Vehicle,
	sum(Accident_count) as Total_Accident
	from Accident
	group by Time
	order by Total_Vehicle desc,Total_Accident desc;


Select * from Accident;

-- Analyze Higher Avg_Speed is affecting the Accident 

	Select 
	City_Zone,
	avg(Avg_speed_kmph) as avg_speed,
	sum(Accident_count) as Total_Accident
	from
	Accident
	group by City_Zone
	Order by avg_speed desc;
	
	Select 
	Time,
	avg(Avg_speed_kmph) as avg_speed,
	sum(Accident_count) as Total_Accident
	from
	Accident
	group by Time
	Order by avg_speed desc;





	With Avg_speed_Accident as
	(
	Select 
	Time,
	City_Zone,
	Round(avg(Avg_speed_kmph),2) as avg_speed,
	sum(Accident_count) as Total_Accident
	from
	Accident
	group by Time,City_Zone
	order by Time Asc,Avg_speed desc
	)
	
	Select *,Dense_Rank() over (partition by Time order by avg_speed desc) avg_speed_rnk
	from Avg_speed_Accident;



	Select * from Accident;



-- Energy Consumption

	Select * from Consumption;

-- Total_Energy Consumed by City_Zone.

	Select 
		city_zone,
		sum(energy_consumed_mwh) as energy_consumption
	from Consumption
	group by city_zone
	order by energy_consumption desc;

-- Monthly Energy consumption

	
	Select 
		Month(Date) as Monthly_consumption_energy,
		sum(Energy_consumed_mwh) as energy_consumption,
	    round((sum(Energy_consumed_mwh) *100/ (select sum(Energy_consumed_mwh) from Consumption)),2) as percentage_energy_consumption
	from Consumption
	group by Month(Date);



	
	 Select City_Zone,sum(Household_consumption_percent) as Total_Household_consumption,
	                  sum(Industrial_consumption_percent) as Total_Industrail_percent,
	                  sum(Renewable_Energy_percent) as Total_Industrail_percent
	from Consumption
	group by City_Zone;



-- Pollution

Select * from Pollution;

-- Analyze the PM 2.5 level (particulate Matter) which is harmful for living being or sometime cause death, if proper measure are not taken. 

	Select 
	City_Zone,
	Round((sum(PM_2_5_LEVEL) *100 /(select sum(PM_2_5_LEVEL) from Pollution)),2) as PM_2_5 
	from Pollution
	Group by City_Zone
	order by PM_2_5 DESC;

-- PM 10 level which is less harmfull than PM 2.5 but gave little bit breathing issue.

	Select 
	City_Zone,sum(PM_10_LEVEL)as Total_PM_10,
	Round((sum(PM_10_LEVEL) *100 /(select sum(PM_10_LEVEL) from Pollution)),2) as PM_10
	from Pollution
	Group by City_Zone
	order by PM_10 DESC ;

-- AQI (Air Quality Index) Higher the AQI ,higher the  chance of breathing issue ,which create drastic situation for human being.

	Select 
	City_Zone,
	Round(Avg(AQI),2) as Avg_AQI
	FROM Pollution
	group by City_Zone
	order by Avg_AQI DESC
	;
