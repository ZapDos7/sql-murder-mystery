# SQL Murder Mystery

You can try it yourself [here](https://mystery.knightlab.com/)

## Overview

A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that <i>the crime was a **​murder**​ that occurred sometime on **​Jan.15, 2018**​ and that it took place in **​SQL City**</i>​.

![image](https://github.com/user-attachments/assets/daab4de8-60f6-4461-b8e1-c31b1ff4f062)


1. Crime description
```sql
select description from crime_scene_report where type = 'murder' and city = 'SQL City' and date = 20180115
```
<details>
    <summary>Answer</summary>
    <i>Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".</i>
</details>

2. Witnesses

a. Person on *Northwestern Dr*

```sql
select * from person where address_street_name = 'Northwestern Dr' order by address_number desc limit 1
```
<details>
    <summary>Answer</summary>
    <table style="width:100%">
  <tr>
    <th>id</th>
    <th>name</th>
    <th>license_id</th>
    <th>address_number</th>
    <th>address_street_name</th>
    <th>ssn</th>
  </tr>
  <tr>
    <td>14887</td>
    <td>Morty Schapiro</td>
    <td>118009</td>
    <td>4919</td>
    <td>Northwestern Dr</td>
    <td>111564949</td>
  </tr>
</table>
</details>

  b. Annabel

```sql
select * from person where name like '%Annabel%' and address_street_name = 'Franklin Ave'
```
<details>
    <summary>Answer</summary>
    <table style="width:100%">
  <tr>
    <th>id</th>
    <th>name</th>
    <th>license_id</th>
    <th>address_number</th>
    <th>address_street_name</th>
    <th>ssn</th>
  </tr>
  <tr>
    <td>16371</td>
    <td>Annabel Miller</td>
    <td>490173</td>
    <td>103</td>
    <td>Franklin Ave</td>
    <td>318771143</td>
  </tr>
</table>
</details>

3. Testimonies
```sql
select interview.transcript, person.name from interview inner join person on person.id = interview.person_id where person_id in (14887, 16371)
```

<details>
    <summary>Answer</summary>
    <table style="width:100%">
  <tr>
    <th>transcript</th>
    <th>name</th>
  </tr>
  <tr>
    <td>I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".	</td>
    <td>Morty Schapiro</td>
  </tr>
  <tr>
    <td>I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th.	</td>
    <td>Annabel Miller</td>
  </tr>
</table>
</details>

4. Gym Memberships
```sql
select m.person_id, m.name, ci.check_in_date, ci.check_in_time, ci.check_out_time
from get_fit_now_member m
join get_fit_now_check_in ci
on m.id = ci.membership_id
where m.id like '48Z%' 
and m.membership_status = 'gold'
and ci.check_in_date = 20180109
```

<details>
    <summary>Answer</summary>
    <table style="width:100%">
  <tr>
    <th>person_id</th>
    <th>name</th>
    <th>check_in_date</th>		
    <th>check_in_time</th>
    <th>check_out_time</th>
  </tr>
  <tr>
    <td>28819</td>
    <td>Joe Germuska</td>
    <td>20180109</td>
    <td>1600</td>
    <td>1730</td>
  </tr>
  <tr>
    <td>67318</td>
    <td>Jeremy Bowers</td>
    <td>20180109</td>
    <td>1530</td>
    <td>1700</td>
  </tr>
</table>
</details>

5. Car Plate Number
```sql
select person.name from drivers_license
join person on drivers_license.id = person.license_id
where person.id in (28819, 67318)
and drivers_license.plate_number like '%H42W%'
```
<details>
    <summary>Answer</summary>
    <i>Jeremy Bowers</i>
</details>

### The Real Villain
> Congrats, you found the murderer! But wait, there's more... If you think you're up for a challenge, try querying the interview transcript of the murderer to find the real villain behind this crime. If you feel especially confident in your SQL skills, try to complete this final step with no more than 2 queries. Use this same INSERT statement with your new suspect to check your answer.

<details>
    <summary>SQL 1</summary>
    <pre>
      <code>select transcript from interview 
      join person
      on interview.person_id = person.id
      where person.name = 'Jeremy Bowers'</code>
    </pre>
</details>

<details>
    <summary>Answer</summary>
    <i>I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017.</i>
</details>

<details>
    <summary>SQL 2</summary>
    <pre>
      <code>select person.name, count(*) as concert_attendance
      from person
      join drivers_license on drivers_license.id = person.license_id
      join facebook_event_checkin on facebook_event_checkin.person_id = person.id
      where drivers_license.height between 65 and 67
        and drivers_license.hair_color = 'red'
        and drivers_license.car_make = 'Tesla'
        and drivers_license.car_model = 'Model S'
        and facebook_event_checkin.event_name = 'SQL Symphony Concert'
        and facebook_event_checkin.date between '20171201' AND '20171231'
      group by person.name
      having count(*) = 3;</code>
    </pre>
</details>

<details>
    <summary>Answer</summary>
    *Miranda Priestly*
</details>

> Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. Time to break out the champagne!
