## Environment Variable and Set-UID Program Lab

### 2.1

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1029069913131860009/unknown.png">

The printenv or env commands display all the environment variables and their values.
By using printenv <environment_variable> (i.e printenv PWD) it will display the value of the given environment variable. (line 1)
Using unset <environment_variable> we can clear that value, as seen in the image. (line 2)
By doing export <environment_variable>=<value> (i.e export PWD=/home/seed) it's possible to create a new environment variable and set it's value (line 4)

### 2.2

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1029155420935303320/unknown.png">

Step 1:
Compiling the program and saving the output of the binary to a file called file1.txt, we can check which environment variables (and their values) the child process has access to.


Step 2:
Similarly, commenting the printenv() statement for the child process and uncommenting the one for the parent statement and recompiling the program, storing the output of the binary file to a file called file2.txt, we can check which environment variables (and their values) the parent process has access to.

Step 3:

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1029156131416846466/unknown.png">

By using the  "diff file1.txt file2.txt" command, it's possible to see the differences between both files. Since the command has no output, meaning that there is no difference between the files, we can assume that both the parent and child processes share the same environment variables.

### 2.3

Step 1:

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1029700732984770580/unknown.png">

Passing NULL to execve() running the env command prints nothing.

Step 2:

<img src="https://cdn.discordapp.com/attachments/1021902913079103488/1031958865413148722/unknown.png">

Passing environ to execve() running the env command prints the environment variables.

Step 3:
The environment variables are only inherited when the "environ" parameter is passed to the execve() function, otherwise they wont.

### 2.4


## Week 4 CTF Challenge 
Steps:
- Open the website
- Go through the available options skimming for information
- On the WordPress Hosting product in the shop notice the additional information tab
- Check Additional information tab and find Wordpress version 5.8.1 and WooCommerce v5.7.1 and Booster for WooCommerce v5.4.3 plugins
- In the product reviews, note that there's an admin user, likely with full system permission
- On the footer of the website, notice that there's the phrase "Build with Storefront & WooCommerce", so they're probably using Wordpress v5.8.1 and the  WooCommerce v5.7.1 and Booster for WooCommerce v5.4.3 plugins. Confirmed via adding the sufix /feed to the url (http://ctf-fsi.fe.up.pt:5001/feed/) in <generator>https://wordpress.org/?v=5.8.1</generator>
- While searching for vulnerabilities in CVE databases for the different plugins versions, we noticed that for the WooCommerce Plugin v.5.4.3 there was a vulnerability that lets you bypass the authentication system via the password recovery process
- The corresponding CVE is CVE-2021-34646 (flag1: flag{CVE-2021-34646}) and in exploit-db there's a python script to exploit it (https://www.exploit-db.com/exploits/50299)
- Follow the instructions on the python script:
1.  Goto: http://ctf-fsi.fe.up.pt:5001//wp-json/wp/v2/users/
2. Find that the user id for the admin is 1
3. Attack with python ./50299.py http://ctf-fsi.fe.up.pt:5001/ 1
4. See which of the 3 links that were output work and allow to bypass the authentication.
- After clicking the correct link and being logged as an admin, go to http://ctf-fsi.fe.up.pt:5001/wp-admin/edit.php and find the private post with the second flag: flag{please don't bother me}