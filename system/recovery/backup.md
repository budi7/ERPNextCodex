#  Backup

### Steps
1. SSH to your server, and login as non root account to stop your frappe applications

        sudo service supervisor stop
        sudo service nginx stop

2. Run backup script. Your backup files by default will be on `home/{userErp}/{benchDiirName}/sites/{site}/private/backups`
    
        bench --site {site} backup --with-files --backup-path [your path]

3. copy backup to your drive (if needed)

        scp {userErp}@6{serverIp}:/home/{userErp}/{benchDiirName}/sites/{site}/private/backups /{YourLocalPath}


### Reference
https://frappeframework.com/docs/v14/user/en/bench/reference/backup
https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/#:~:text=SCP%20(secure%20copy)%20is%20a,system%20to%20your%20local%20system.
