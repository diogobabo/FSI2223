## SQL Injection Attack Lab

### Task 1

After logging into the MySQL container we can just enter a simple SELECT query to get all the info of the user Alice.

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1044215513313710150/image.png">
<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1044215577335562260/image.png">

### Task 2
#### Task 2.1

Taking a quick glance at the php server's code, it's noticeable that there's a vulnerability in the query statement, as the input is not validated or sanitized.

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1044218107729809468/image.png">

Since we know the exact query, we can just build or payload without the need to guess. <br>
There's multiple ways to inject SQL code in order run a previously prepared SQL query. One of those ways is to make that statement always evaluate to true. This can be done with a `or '='` inside the SELECT statement, between both AND clauses. <br>
By passing `admin' or'=` as the username and anything as the password we successfully inject SQL code, bypassing the unknown password.

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1044217583706054666/image.png">
<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1044217714153111583/image.png">

#### Task 2.2

Similarly, one can achieve the same exact result using curl and basic knowledge of HTTP requests, without the need for a Web interface. <br>
All we needed was to encode the special characters in our payload (`admin' or '=`), like the whitespace and the single quote and pass our new string (`admin%27%20or%20%27=`) as a parameter for the username, aswell as a random password. 

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045412814539456512/image.png">
<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045412960232820736/image.png">

#### Task 2.3

In this task we're asked to try and run two SQL queries at a time. <br>
This can be done using another technique other than make the statement always true. <br>
In this case, we can end the first statement earlier by using a `;`, write another statement right after the first one and comment the rest of the original statement by adding a comment symbol (# or -- in MySQL) after the second statement. <br>
Following this logic, our username input could be something like `admin';DELETE * FROM credential;#`, which would Delete the entirity of the credential table.<br>
(Un)fortunately, [mysql_query() doesn't allow running more than one statement](https://www.php.net/manual/en/function.mysql-query.php#description), which means our payload will have no effect on the database.

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045417693467119646/image.png">

### Task 3
#### Task 3.1

After analizing the source code, we can clearly tell that it's possible to add any extra columns to be updated. <br>
Because we want to update Alice's salary, all we need to do is add the `salary = 'X'` update statement to the original statement and set X to be whatever we want, in our case 9.999.999

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045424631114387488/image.png">

Knowing that the original salary for Alice was 20.000, after sending our payload, we managed to change that to 9.999.999

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045425127854178355/image.png">
<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045424714622967959/image.png">

#### Task 3.2

At this point it's just a matter of playing around with everything we've done so far. <br>
In this task, since we want to change Boby's salary to 1 and we're originally updating Alice's info, we can keep the salary statement and add a WHERE clause affecting Boby's info, instead of Alice's, not forgetting to add a comment symbol to discard the WHERE clause selecting Alice's row in the table.

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045426122097164438/image.png">
<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1045426258344939552/image.png">

#### Task 3.3

## Week 8 CTF Challenge
### Challenge 1

In this challenge, we're told that if we login into the ctf-fsi.fe.up.pt:5003 website using the admin username, we'll get access to the flag. <br>
Glancing over the part of the index.php code that queries the database, we noticed that it was vulnerable to SQL Injection attacks. <br>
Since the inputs are not sanitized whatsoever, one could build a string such that the query asks the database for all the information of the admin user, without checking if the password is valid.

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1042503044295819384/image.png">

Now that we know this, we need to build our input to exploit this vulnerability. <br>
- There's many things we can do:
    - Cut the query short, selecting only the username = 'admin' and discarding the password
        - Inputs:
        - username: admin'; 
        - password (can be anything): abc 
    - Change the query to something similar to "Select * from user where username = 'admin' OR True;"
        - Inputs:
        - username: admin' or '=
        - password (can be anything): abc

In both cases, the main difficulty in building the input is the number of quotes we need to include, in order to match with other quotes present in the query string. <br>
All that's left to do is pwn the website, obtaining the flag (flag{21e977547fb42f61bb1c94035c0faf64}).

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1042509560226795620/image.png">
<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1042508682845499453/image.png">

### Challenge 2