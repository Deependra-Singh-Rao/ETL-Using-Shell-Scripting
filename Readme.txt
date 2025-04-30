Step 1: Open the Postgres SQL CLI, if it is not already open.

Step 2: At the postgres=# prompt, run the following command to connect to the database ‘template1’

```sql
> \c template1; 
```

Step 3: Once you connect to the database, run the command to create the table called ‘access_log’
	
	CREATE TABLE access_log(timestamp TIMESTAMP, latitude float, longitude float, visitor_id char(37));

Step 4: Create a shell script named cp-access-log.sh and add commands to complete the remaining tasks to extract and copy the data to the database.
	
	>touch cp-access-log.sh

Step 5: Add the wget command to the script to download the file.

	> wget "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Bash%20Scripting/ETL%20using%20shell%20scripting/web-server-access-log.txt.gz"

Step 6: Add the code, to run the gunzip command to unzip the .gz file and extract the .txt file, to the script.

	> gunzip -f web-server-access-log.txt.gz

Step 7: Write the extraction phase commands in the script file.

	> cut -d"#" -f1-4 web-server-access-log.txt

Step 8: Run the script.

	> bash cp-access-log.sh

Step 9: Redirect the extracted output into a file.

	> # In the script file, replace the "Step 7" command with 
	> cut -d"#" -f1-4 web-server-access-log.txt > extracted-data.txt

Step 10: Transform the data into CSV format.

	> # Update the script file and add:
	> tr "#" "," < extracted-data.txt > transformed-data.csv

Step 11: Load the data into the table access_log in PostgreSQL

	> # Update the script file and add:
	> echo "\c template1;\COPY access_log  FROM '/home/project/transformed-data.csv' DELIMITERS ',' CSV HEADER;" | psql --username=postgres --host=localhost

Step 12: Execute the final script:

	> bash cp-access-log.sh

Step 13: To verify the status go to the pg admin CLI:

	> SELECT * from access_log;

********************************************************************************************************************************************************************

OPTIONAL 

Step 14: To automate it, make the shell script executable and assign a crontab

	> chmod u+x cp-access-log.sh
	> #Add shebang in the script file: #! \bin\bash
	> crontab -e
	> #AT the end of crontab, add: 
	> 1 * * * * path/cp-access-log.sh
	> #Note that this will make the script run every minute on a daily basis
