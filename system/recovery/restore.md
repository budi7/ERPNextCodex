#  Restore

### Steps
1. copy backup to your server (if needed)

        scp /{YourLocalBackupPath} {userErp}@6{serverIp}:/home/{userErp}/{benchDiirName}


2. SSH to your server, and login as non root account and make sure frappe applications & services stopped

    su - {erpUser}


3. Check your site `site_config.json` first. You have to make sure that the `site_config.json` on the server is equal to your downloaded `site_config.json` back up file. Otherwise, replace your `site_config.json` on the server with your downloaded `site_config.json`. (skip this step if your downloaded and the file on your server is equal)

    first: replace your `site_config.json` file

        nano /home/{erpUser}/{BenchDirName}/sites/{sitesName}/site_config.json

    * and then copy paste with your downloaded `site_config.json` file
    * use `ctrl` + `shift` + `6` and select your cursor to end document, hit `ctrl` + `k` to cut the text, and then open then copy your downloaded files, and paste to your nano editor. Hit `ctrl` + `x`, type `y`, hit `enter` twice

    second: fix the database
    open your mysql terminal

        mysql -u root -p  
        Enter password: YOURPASSWORD  

    open your `site_config.json` and you can find key called `db_name`. make sure that the value of `db_name` is available on your table list. If not. create the new one

        mysql create database {db_name};

    or better if database allready exist, you can recreate that one.

        mysql drop database {db_name};
        mysql create database {db_name};


    next step is check your mysql username. the user used in sites is the database name itself. Type following code and if  you cant find `db_name` as user listed on table, please continue to next step. 

        use mysql;  
        Database changed  
        SELECT user FROM user;  


    create new user in mysql according to user listed on your downloaded `site_config.json` file. Follow this step. (you need to read `db_name` and `db_password` key from your downloaded `site_config.json` file.

        CREATE USER '{db_name}'@'localhost' IDENTIFIED BY '{db_password}';

    grant access to your newly added user (if user allready exist, skip this step)

        GRANT ALL PRIVILEGES ON *.* TO '{db_name}'@'localhost' WITH GRANT OPTION;


    exit your mysql terminal and restar mysql service

        exit; // on the sql terminal
        sudo service mysqld restart // on ssh terminal


4. Run restore script.
    
        bench --site {sitename} --force restore {path to database backup file .sql} --with-private-files {relative-path-to-private-files-backup-file .tar} --with-public-files {relative-path-to-public-files-backup-file .tar}

    just in case you don't back up the private and public files

        bench --site {sitename} --force restore {path to database backup file .sql}

5. Once process done, try to run your frappe application again

        sudo service mysqld restart
        sudo service nginx start
        sudo service supervisor start

6. If you had any errors after restore, try to run `bench migrate` script

    run bench migrate

         bench --site {site} migrate

    re-run services

        sudo service mysqld restart
        sudo supervisorctl restart all

    if the error persists, please check error logs here 

        cd /home/{erpUser}/{BenchDirName}/sites/{sitesName}/error_snapshots

### Reference
https://github.com/frappe/erpnext/wiki/Restoring-From-ERPNext-Backup
https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql
https://frappeframework.com/docs/v14/user/en/bench/reference/migrate