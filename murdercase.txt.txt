--BEGIN
/* You vaguely remember that the crime was a murder that occured sometime on
Jan.15.2018 and it took place in SQL City, start by retrieving the corresponding 
crime scene report from the police Department database */

SELECT * FROM crime_scene_report

--It occured on Jan.15 2018 and in SQL city

SELECT * FROM crime_scene_report
WHERE date = '20180115' AND city = 'SQL City'

/*"Security footage shows that there were 2 witnesses. The first witness lives at the last house on ""Northwestern Dr"".
The second witness, named Annabel, lives somewhere on ""Franklin Ave"" */

/*A question comes to mind and that would be that, where do we find the details of 
these witnesses ? 
The two witnesses are 
1) One lives on Northwestern Dr
2) The other one is annabel and she lives on Franklin Ave */

--First, Find the witness that lives in the last house in Northwestern Dr

SELECT * FROM person
WHERE address_street_name = 'Northwestern Dr' 
ORDER BY address_number DESC

--Ans: Our first witness is Morty Schapiro.

/*Our first witness is Morty Schapiro with Id 14887, License_id 118009,
address_street_name Northwestern Dr and SSN 111564949 */

-- Task two, find the second witness, the ladys name is 'Annabel' and lives on Franklin Avenue

SELECT * FROM person
WHERE Name LIKE 'Annabel%' AND address_street_name = 'Franklin Ave'

/*Our second witness is Annabel Miller, Id 16371, License_id 490173,
address_street_name Franklin Ave and SSN 318771143 */

-- Next thing to do is to check out for the interview session of the two witness
--Using Morty and Annabels ID's to filter

  SELECT * FROM Interview 
  WHERE person_id IN ('14887', '16371')

/* The first witness Schapiro said; "I heard a gunshot and then saw a man run out. 
He had a ""Get Fit Now Gym"" bag. The membership number on the bag started with ""48Z"". 
Only gold members have those bags.The man got into a car with a plate that included ""H42W""." 

The second witness Annabel said; "I saw the murder happen, and 
I recognized the killer from my gym when I was working out last week on January the 9th." */

--Querying the two similar tables for clarity 

SELECT * FROM get_fit_now_check_in
SELECT * FROM get_fit_now_member

--get_fit seems to be the one 

SELECT * FROM get_fit_now_member
WHERE id LIKE '48Z%' AND membership_status = 'gold'

/*From the first witness account, we have two suspects-
Joe Germuska with Id 48Z7A, person_id 28819, membership start date 20160305 and gold membership status.
Jeremy Bowers with Id 48Z55 person_id 67318, membership start date 20160101 with gold status */

SELECT * FROM get_fit_now_check_in
WHERE check_in_date = 20180109 AND membership_id IN ('48Z7A','48Z55')

/* we want to use the drivers plate number to futher find the killer.
--we are joining drivers license table and person tables */

SELECT * FROM drivers_license

SELECT dl.age, dl.height, dl.hair_color, dl.gender, dl.plate_number, dl.car_make, dl.car_model, p.name, p.ssn, p.address_street_name 
FROM drivers_license AS dl
LEFT JOIN Person AS p
ON dl.id = p.license_id
WHERE plate_number LIKE '%H42W%' or plate_number LIKE '%H42W' or plate_number LIKE 'H42W%'

--The MUDERER is Jeremy Bowers 

--ALTERNATIVELY you can try the below query to find the murderer

/*To check the most corresponding tsble with common ids to use
query drivers_license table and person */

SELECT * FROM drivers_license
SELECT * FROM person

SELECT * FROM drivers_license 
WHERE plate_number LIKE '%H42W%'

-- Three persons id numbers returned; 183779,423327,664760

SELECT * FROM person
WHERE license_id IN ('183779','423327','664760')

/*THe killer is JEREMY BOWERS 
with license id 664760, address_number 312, address_street_name Phi St. and SSN 137882671 */

/* If you think you are up for the challenge, try querying the interview transcript of 
the murderer to find the real villain behind this crime */
--To query what Jeremy said we have to use his id which is person_id 67318

SELECT * FROM interview
WHERE person_id = '67318'

/*The murderers transcript; "I was hired by a woman with a lot of money. I don't know her name but I know she's 
around 5'5"" (65"") or 5'7"" (67""). She has red hair and she drives a Tesla Model S. 
I know that she attended the SQL Symphony Concert 3 times in December 2017." */

SELECT * FROM drivers_license
WHERE height BETWEEN 65 and 67
AND hair_color = 'red'
AND gender = 'female'
AND car_make = 'Tesla'
AND car_model ='Model S'

-- The id of the major suspects are 202298, 291182, 918773

--Lets CREATE a table for the 3 suspects

CREATE Table Suspect AS (SELECT * FROM drivers_license
WHERE height BETWEEN 65 and 67
AND hair_color = 'red'
AND gender = 'female'
AND car_make = 'Tesla'
AND car_model ='Model S')

SELECT * FROM suspect

--Lets JOIN the suspect and person tsble to find the Real person behind the murder

SELECT s.id, s.age, s.height, p.id AS person_id, p.name, p.address_street_name, p.ssn
FROM suspect AS s
RIGHT JOIN person AS p
ON s.id = p.license_id

/* To further find who sent Jeremy, query the facebook event checking table to check who
came to SQL concert thrice in Dec. 2017 */

SELECT * FROM facebook_event_checkin
WHERE date BETWEEN 20171201 AND 20171231 
AND event_name = 'SQL Symphony Concert'
AND person_id IN (99716,90700,78881)

-- 99716 is suspected to be the sponsor and behind the killing
-- To find the name create Murder_sponsor table from the JOIN query

CREATE Table  Murder_Sponsor AS ( SELECT s.id, s.age, s.height, p.id AS person_id, p.name, p.address_street_name, p.ssn
FROM suspect AS s
RIGHT JOIN person AS p
ON s.id = p.license_id
)

SELECT * FROM Murder_agent 
WHERE person_id = 99716

__ "Miranda Priestly" sponsored jeremy for the Murdering. 

