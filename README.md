# The SQL Murder Mystery

There's been a Murder in SQL City! The [SQL Murder Mystery](https://mystery.knightlab.com/) is designed to be both a self-directed lesson to learn SQL concepts and commands and a fun game for experienced SQL users to solve an intriguing crime.

### Experienced SQL sleuths start here
A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ***murder*** that occurred sometime on ***Jan.15, 2018*** and that it took place in ***SQL City***. Start by retrieving the corresponding crime scene report from the police department’s database.

### Schema diagram
![image](https://user-images.githubusercontent.com/28622956/186943656-35e4cff4-4742-471b-ae72-20bd7b905d76.png)

### Use your knowledge of the database schema and SQL commands to find out who committed the murder.

* Read description from the police.
```sql
SELECT description 
FROM crime_scene_report 
WHERE city='SQL City' 
AND type='murder' 
AND date=20180115;
```
Output
```
Security footage shows that there were 2 witnesses. The first witness lives at the last house 
on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".
```
----------
* Find 1st witness (assume last house is the highest address_number).
```sql
SELECT id, name, address_number 
FROM person 
WHERE address_street_name='Northwestern Dr' 
ORDER BY 3 DESC 
LIMIT 1;
```
Output
```
id	  name            address_number
14887	  Morty Schapiro  4919
```
---------
* Find 2nd witness.
```sql
SELECT id, name 
FROM person 
WHERE address_street_name='Franklin Ave' 
AND name LIKE 'Annabel%';
```
Output
```
id	  name
16371	  Annabel Miller
```
--------
* Read the transcipts of the witnesses' interviews with the police.
```sql
SELECT p.name, i.transcript 
FROM interview i 
INNER JOIN person p 
ON i.person_id=p.id 
WHERE person_id IN (14887,16371);
```
Output
```
name		  transcript
Morty Schapiro	  I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. 
                  The membership number on the bag started with "48Z". Only gold members have 
                  those bags. The man got into a car with a plate that included "H42W".
Annabel Miller	  I saw the murder happen, and I recognized the killer from my gym when I was 
                  working out last week on January the 9th.
```
-----------
* Use Morty's report and find the potential murderer(s).
```sql
SELECT p.id, p.name 
FROM person p
INNER JOIN drivers_license d
ON p.license_id=d.id
INNER JOIN get_fit_now_member g
ON p.id=g.person_id
WHERE d.gender='male'
AND d.plate_number LIKE '%H42W%'
AND g.id LIKE '48Z%'
AND g.membership_status='gold';
```
Output
```
id	  name
67318	  Jeremy Bowers
```
---------
* Since we found only one name, we should check this solution. Fill the provided query with the found name to check.
```sql
INSERT INTO solution VALUES (1, 'Jeremy Bowers');
SELECT value FROM solution;
```
Output
```
Congrats, you found the murderer! But wait, there's more... If you think you're up for a challenge, 
try querying the interview transcript of the murderer to find the real villain behind this crime. 
If you feel especially confident in your SQL skills, try to complete this final step with no more 
than 2 queries. Use this same INSERT statement with your new suspect to check your answer.
```
----------
* Read the interview transcript of the murderer to find out more.
```sql
SELECT transcript 
FROM interview 
WHERE person_id = 67318;
```
Output
```
transcript
I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") 
or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL 
Symphony Concert 3 times in December 2017.
```
----------
* Use this information to determine the real villain behind this crime.
```sql
SELECT p.name 
FROM person p 
INNER JOIN drivers_license d
ON p.license_id=d.id
INNER JOIN facebook_event_checkin f
ON p.id=f.person_id
WHERE d.gender='female'
AND d.height BETWEEN 65 AND 67
AND d.hair_color='red'
AND d.car_make='Tesla'
AND d.car_model='Model S'
AND f.event_name='SQL Symphony Concert'
AND f.date LIKE '201712%'
GROUP BY p.id HAVING COUNT(*)==3;
```
Output
```
name
Miranda Priestly
```
-----------
* Let's check if that is correct.
```sql
INSERT INTO solution VALUES (1, 'Miranda Priestly');
SELECT value FROM solution;
```
Output
```
value
Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. 
Time to break out the champagne!
```
