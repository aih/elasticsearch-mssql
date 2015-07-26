# elasticsearch-mssql
Setting up elasticsearch for an mssql database

It seemed like an impossible task. Use the open source elasticsearch engine to index and search a MS SQL Server database. It turns out that it is not that difficult, though as always there are tricks scattered across the internet that need to be combined to make it work.

The import is now done with an elasticsearch-jdbc importer, combined with a jdbc driver (from Microsoft). A previous method, called a 'river' has now (2015) been deprecated.

The general instructions for using the jdbc importer are [here](https://github.com/jprante/elasticsearch-jdbc#ms-sql-server). But it doesn't quite work. It starts by installing elasticsearch. That actually goes quite smoothly. I would recommend also installing the Marvel plugin (free developer trial) and starting the [Sense interface](http://www.elastic.co/guide/en/elasticsearch/guide/current/sense_widget.html?snippets/030_Data/45_Upsert.json), to monitor the elasticsearch index and test queries.

At step 6 (set up database, [make sure to allow TCP/IP connections](http://stackoverflow.com/questions/2388042/connect-to-sql-server-2008-with-tcp-ip)), there are a number of nuances:

    1. Enable TCP/IP in the SQL SERVER CONFIGURATION MANAGER ( (Start Menu > Microsoft SQL Server > Configuration Tools > SQL Server Configuration Manager > SQL Server Network Configuration)
    2. Start the SQL SERVER BROWSER Service. To do this, open the CONFIGURATION MANAGER as an administrator (right click to open). Once in the Manager, right click the BROWSER SERVICE, click Properties and change the Start Mode to Enabled. (http://stackoverflow.com/a/21378235)
    3. Explicitly set the TCP to 1433, as explained in these [two](http://stackoverflow.com/a/18850073) [answers](http://stackoverflow.com/a/24299346).
    
Step 7 is to start the elasticsearch engine. Easy enough. Then Step 8 has a script to import data into elasticsearch.  In order to run the script, I found it most convenient to use the bash terminal from cygwin. Also, for windows, the variables in path names need to be in quotes. So I changed these lines:
```
-cp "${lib}/*" \
-Dlog4j.configurationFile=${bin}/log4j2.xml \
```
to
```
-cp "$lib/*" \
-Dlog4j.configurationFile="$bin"/log4j2.xml \
```

I copied and pasted (shift-Insert) the script into the bash terminal, and then got a message that the user login failed.  I tried the local windows user+login, but that did not work. I then [changed authentication on the server to 'Mixed Mode'](https://msdn.microsoft.com/en-us/library/ms188670.aspx). I gave permissions on the database to a user whose password I set, and then re-ran the script. In about a minute, I had more than twenty thousand indexed records.

