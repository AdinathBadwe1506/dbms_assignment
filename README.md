# dbms_assignment
### SPPU MCA CA105 assignment 2023-2024 

### MCA 2023-24 CA105 assignment

## Group Members 
### Adinath Badwe - 23112003
### Samarth Kulkarni - 23112019
### Shaikh Mohammed Fazle Haque Amin - 23112035
-------------------------------------------------------------------------------------------------------------------------------------------------------------
// **Steps taken**

- first the important entities were noted down.
- then the important relations between each entities were noted down.
- individual relations between entities were created.
- er diagram was created in accordance with above steps.
- from er database schema was created.
- database was created in accordance with the schema including all the constraints.
- relevant data for the needs of the assignment was created.
- given queries were executed.

--------------------------------------------------------------------------------------------------------------------------------------------------------------
// **ASSUMPTIONS**

1 - Commission of agent is calculated on gross amount of booking price.\
2 - Operations of the months of September,October and November are shown.\
3 - It is assumed for every route from A to B,there exists a route from B to A which is distinct from A to B.\
4 - In case a train, going from A to B, is delayed for greater than 30 min,
	is it assumed that a standby train,driver,codriver and standby coaches are present at every such B location for prompt departure of the next schedule.\
5 - In case of a delayed train, the ticket table remains unaffected, and the passenger gets corresponding seat in the standby coaches.\
6 - A train in given databse means a train engine and a coach is a train coach where passengers seat.\
7 - mileage in coach table is updated after its every trip.\
8 - Data is generated in such a way that a ticket is booked by a travel agent 40 percent of all tickets booked.\
9 - Expected times for schedule_stations are updated at the start of the year, whereas actual times are updated when the train reaches the respective station.\
10 - Data is generated in such a way that a ticket is confirmed only 80 percent of times.\
11 - In case a ticket is not booked by a travel agent, it is assumed that the ticket is booked by the passenger himself.\
12 - It is assumed that the train which makes its journey from A to B, also makes the journey from B to A, most of the times on the same day.\
13 - One ticket is for one seat.\
14 - It is assumed that the data provided by the train company is valid so as to not generate any conflicts in database.\
15 - A schedule is for a train and a set of coaches for a specific route on a specific date, with a driver and a codriver.\

---------------------------------------------------------------------------------------------------------------------------------------------------------------

// **CREATE STATEMENTS**

create database assignment;
use assignment;

create table coach(
coach_id int primary key,
mileage int
);

create table station (
station_id int primary key,
name varchar(120) not null
);

create table train (
train_id int primary key,
name text not null
);

create table travel_agent (
travel_agent_id int primary key,
name varchar(120) not null,
contact bigint not null
);

create table discount (
type varchar(30) primary key,
discount real not null
);

create table driver (
driver_id int primary key,
name varchar(120) not null,
city_of_residence varchar(120) not null,
rest_day varchar(10) not null
);

create table passenger (
passenger_id int primary key,
name varchar(120) not null,
age int not null,
contact bigint not null,
type varchar(30) not null
);

alter table passenger add foreign key (type) references discount(type);

create table route (
route_id int primary key,
start_id int not null,
destination_id int not null,
distance int not null
);

alter table route add foreign key (start_id) references station(station_id);
alter table route add foreign key (destination_id) references station(station_id);

create table schedule (
schedule_id int primary key,
date date not null,
route_id int not null,
train_id int not null,
driver_id int not null,
codriver_id int not null,
standby_driver_id int,
standby_codriver_id int,
standby_train_id int,
);

alter table schedule add foreign key (route_id) references route(route_id);
alter table schedule add foreign key (train_id) references train(train_id);
alter table schedule add foreign key (driver_id) references driver(driver_id);
alter table schedule add foreign key (codriver_id) references driver(driver_id);
alter table schedule add foreign key (standby_driver_id) references driver(driver);
alter table schedule add foreign key (standby_codriver_id) references driver(driver_id);
alter table schedule add foreign key (standby_train_id) references train(train_id);

create table schedule_station(
schedule_id int,
station_id int,
exp_arrival_time datetime not null,
exp_departure_time datetime not null,
actual_arrival_time datetime,
actual_departure_time datetime,
primary key (schedule_id,station_id)
);

alter table schedule_station add foreign key (schedule_id) references schedule(schedule_id);
alter table schedule_station add foreign key (station_id) references station(station_id);

create table seat (
seat_id int primary key,
coach_id int not null
);

alter table seat add foreign key (coach_id) references coach(coach_id);

create table ticket (
ticket_id int primary key,
seat_id int not null,
board_id int not null,
alight_id int not null,
schedule_id int not null,
travel_agent int,
booking_date date not null,
passenger_id int not null,
gross_amount int not null,
confirmed bool not null
);

alter table add foreign key (seat_id) references seat(seat_id);
alter table add foreign key (board_id) references station(station_id);
alter table add foreign key (alight_id) references station_id(station_id);
alter table add foreign key (schedule_id) references schedule(schedule_id);
alter table add foreign key (travel_agent) references travel_agent(travel_agent_id);
alter table add foreign key (passenger_id) references passenger(passenger_id);

create table coach_schedule (
coach_id int,
schedule_id int,
primary key(coach_id,schedule_id)
);

alter table coach_schedule add foreign key (coach_id) references coach(coach_id);
alter table coach_schedule add foreign key (schedule_id) references schedule(schedule_id);

create table maintenance(
maintenance_id int primary key,
coach_id int not null,
date date int not null,
previous_maintenance_id int not null,
done bool not null
);

alter table maintenance add foreign key coach_id references coach(coach_id);
alter table maintenance add foreign key (previous_maintenance_id) references maintenance(maintenance_id);

----------------------------------------------------------------------------------------------------------------------------------------------------------
// **INSERT AND EXPORT DATA QUERIES**

Insert data into coach:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/coach.csv" into table coach fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into coach_schedule:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/coach_schedule.csv" into table coach_schedule fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into route:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/route.csv" into table route fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into schedule:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/schedule.csv" into table schedule fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into train:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/train.csv" into table train fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into schedule_station:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/schedule_station.csv" into table schedule_station fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into maintenance:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/maintenance.csv" into table maintenance fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into ticket:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/ticket.csv" into table coach fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into passenger:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/passenger.csv" into table passenger fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into discount:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/discount.csv" into table discount fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into travel_agent:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/travel_agent.csv" into table travel_agent fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into driver:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/driver.csv" into table driver fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into station:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/station.csv" into table station fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;

Insert data into seat:

load data local infile "F:/MCA SPPU/SEM 1/cs105/assignment1/actual/project files/seat.csv" into table seat fields terminated by "," enclosed by '"' lines terminated by "\r\n" ignore 1 lines;


Export data from passengers

select * from passenger into outfile "C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/passengers.csv" fields terminated by "," enclosed by '"' lines terminated by "\r\n";

Same repeated for the other tables

---------------------------------------------------------------------------------------------------------------------------------------------------------
// **TABLES EXPLAINED IN BRIEF**

coach - (coach_id,mileage)
coach_id is the primary key for coach.
mileage gives the current of the coach. After each trip of a coach, its current mileage is to be updated.

-schedule (schedule_id,route_id,train_id,driver_id,codriver_id,standby_train_id,standby_driver_id,standby_codriver_id)
schedule_id is the primary key.
route_id gives the route_id on which the schedule is planned and the train travels. route_id comes from route(route_id) .route_id cannot be null.
train_id gives the train engine which is used for the given schedule. train_id comes from train(train_id).train_id cannot be null.
driver_id gives the main driver driver_id. driver_id comes from driver(driver_id). driver_id cannot be null.
codriver_id gives the co driver driver_id. codriver_id comes from driver(driver_id).
standby_train_id gives the replacement train_id which is used when the schedule previous to current one was late. standby_train_id comes from train(train_id).
standby_driver_id gives the replacement main driver_id which is used when the schedule previous to current one was late. standby_driver_id comes from driver(driver_id).
standby_codriver_id gives the replacement co driver_id which is used when the schedule previous to current one was late. standby_codriver_id comes from driver(driver_id).

-coach_schedule (coach_id,schedule_id)
(coach_id,schedule_id) is the primary key.
coach_id comes from coach(coach_id).
schedule_id comes from schedule(schedule_id).
coach_schedule gives the coaches for a schedule or schedules of a coach.

-discount (type,discount)
type is the primary key.
discount gives the discount rate.
discount table gives the discount rate for a type of passenger.
none of the columns can be null.

-driver (driver_id,name,city_of_residence,rest_day)
driver_id is the primary key.
rest_day is rest_day of the driver.
none of the columns can be null.

-route (route_id,start_id,destination_id,distance)
route_id is the primary key.
start_id gives the station_id of the starting location of the route.start_id comes from station(station_id).
destination_id gives the station_id of the destination location of the route.destination_id comes from station(station_id).
distance gives the distance of the route
none of the columns can null.

-schedule_station(schedule_id,station_id,exp_arrival_time,exp_departure_time,actual_arrival_time,actual_departure_time)
(schedule_id,station_id) is the primary key.
schedule_id comes from schedule and station_id comes from station(station_id).
exp_arrival_time gives the expected arrival time of the train at a given station on a given schedule. exp_arrival_time cannot be null.
actual_arrival_time gives the actual arrival time of the train at a given station on a given schedule.
exp_departure_time gives the expected departure time of the train at a given station on a given schedule. exp_departure_time cannot be null.
actual_departure_time gives the actual departure time of the train at a given station on a given schedule.

-seat (seat_id,coach_id)
seat_id is the primary key.
coach_id comes from coach(coach_id). coach_id cannot be null.
seat gives the seat_id and which coach the seat belongs to.

-station (station_id,name)
station_id is the primary key.
name cannot be null.

-train (train_id,name)
train_id is the primary key.
name cannot be null.

-travel_agent(travel_agent_id,name,contact)
travel_agent_id is the primary key.
name and contact cannot be null.

-ticket (ticket_id,seat_id,board_id,alight_id,schedule_id,travel_agent,booking_date,passenger_id,gross_amount,confirmed)
ticket_id is primary key.
seat_id comes from seat, it cannot be null.
board_id gives the station at which passenger boards the train. It comes from station(station_id) and cannot be null.
alight_id gives the station at which passenger alights it.  It comes from station(station_id) and cannot be null.
schedule_id gives the schedule of the train for the ticket. It comes from schedule(schedule_id) and cannot be null.
travel_agent gives the travel_agent_id if the ticket is booked by a travel_agent otherwise null. It comes from travel_agent(travel_agent_id).
booking_date gives the date on which ticket was booked. It cannot be null.
passenger_id gives the passenger_id for the ticket and cannot be null.
confirmed gives whether the ticket was confirmed or not. It cannot be null.

-passenger (passenger_id,name,age,type,contact)
passenger_id is the primary key.
type gives whether a passenger is senior normal or child.
type comes from discount(type).
none of the columns can be null.

-maintenance (maintenance_id,coach_id,date,previous_maintenance_id,done)
maintenance_id is the primary key.
coach_id gives the coach on which maintenance was done.
date gives the date on which maintenance was done.
previous_maintenance_id gives the maintenance_id of the previous maintenance done on the coach. It comes from maintenance(maintenance_id).
done gives whether the maintenance is done or not.
none of the columns can be null.

--------------------------------------------------------------------------------------------------------------------------------------------------------------
// **TOOLS USED**

Python libraries
- csv,json,random,getindianname,mysql_connector_python used for dummy data generation 
- standard library used for dummy data generation

Javascript libraries
- file system module used for dummy data generation
- standard module used for dummy data generation 

https://generatedata.com/generator
- used for dummy data generation

https://www.mockaroo.com/
- used for dummy data generation 

https://indiarailinfo.com/
- used for schedule and station data generation
---------------------------------------------------------------------------------------------------------------------------------------------------------------
// **WORK DONE**

 - Creation of ER and Normalisation of database was done together by all the group members.
 - Creation of Database and execution of queries in MySQL done by Adinath.
 - Generation of Dummy data according to the constrains on the tables done together by Fazle and Adinath.
 - Javascript work done by Fazle.
 - Python work done by Adinath.
 - Online data generation work done together by all the members.
 - No extra steps were required to normalise the database since we tried to make sure to create the database in normalised form.
 - All Github work done by Adinath.

---------------------------------------------------------------------------------------------------------------------------------------------------------------
// **QUERIES** 
Set A done by Fazle\
Set B done by Samarth\
Set C done by Adinath

---------------------------------------------------------------------------------------------------------------------------------------------------------------
