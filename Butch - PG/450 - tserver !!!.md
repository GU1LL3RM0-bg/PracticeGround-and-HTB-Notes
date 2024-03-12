#### Findings
- Server is running a MSSQL server
- Just by issuing `test' OR 1=1 we can confirm the page is vulnerable to sql injection.
![[Pasted image 20231113221046.png]]


By using `ORDER BY` we can determine the number of columns present in the current database.

![[Pasted image 20231113221010.png]]
> order by 2 accepts the query without errors

Order by 3 doesn't work, which means this table only has 2 columns:
![[Pasted image 20231113221126.png]]


#### Most probable 2 columns are: username and password

Note: Since we don’t see any output of the SQL statements we’re making, the classic `' UNION ALL SELECT 1,2--` won’t work here.

It seems to be only exploitable via `Blind SQL Injection`.

If we run `1' WAITFOR DELAY '0:0:10'` we can see the page has approximately a 10 seconds delay.

Knowing this, we can craft a query that return `true OR false` and try to enumerate the database using only the response time. 10 sec = true and 0 secons = false.

This payload confirms that there is a table in the database called `users`.

```
test ' IF (( SELECT count(name) from sys.tables WHERE name = 'users' )=1) waitfor delay '0:0:10'-- -
```
