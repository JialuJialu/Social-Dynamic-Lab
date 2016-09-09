# How to transfer a collection from one database to another
You must have ownership or admin privileges over each database to do this. While there are potentially solutions in 
Mongo shell or pymongo it is actually quite difficult to do stuff that requires access to multiple databses.
Usually you should be creating multiple "collections" within a database and so will not encounter this issue.

If you find yourself needing to do this then you can type a single line of code into the command line on the machine where
the database is located (split onto two lines for readability).:

mongoexport -d source_db -c collection_to_copy --port 27017 -u “username" -p “password"  --authenticationDatabase “auth_db"|
mongoimport -d target_db -c collection_target --port 27017 -u “ username" -p “password"  --authenticationDatabase “auth_db”

If you have admin privileges you can use the same username, password, and auth_db both times but if you do not then you should
enter the credentials specific to each db, e.g. source_db and target_db (I did not do this so it may not work!)

DO NOT TRY TO DO ANY OTHER OPERATIONS ON THE DATABASES WHILE THIS PROCESS IS RUNNING.

This command will export a collection from the source database and pipes it to a command to import it into a specific 
collection in the target database. Mongo will print out statements showing the progress of the transfer every couple 
of seconds, including information on the number and percentage of records transfered. For a 4GB collection it took 
about 15 minutes. 
