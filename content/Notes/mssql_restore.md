---
title: "Restoring MSSQL Database on Docker"
author: ["Omer Kurt"]
draft: false
---

Docker often comes in handy for managing databases. Here, I’ll walk through the steps I used to restore an MSSQL database backup on Docker.


## Setting up the MSSQL Container {#setting-up-the-mssql-container}

First, we create an MSSQL container with the necessary configurations. Here’s how to run the container and mount the backup file:

```sh
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=YourPassword123" -p 1433:1433 --name mssql \
    -v /path/to/your/backup.bak:/var/opt/mssql/backup/backup.bak \
    -d mcr.microsoft.com/mssql/server:2022-latest
```

The \`-v\` parameter is used to mount our local backup file to the specified path within the container. Replace \`/path/to/your/backup.bak\` with the actual path to your backup file.


## Verifying the Backup File {#verifying-the-backup-file}

After the container is running, we can verify the backup file's presence and adjust permissions to ensure MSSQL can access it:

```sh
docker exec -it mssql ls /var/opt/mssql/backup/
sudo chmod 644 /path/to/your/backup.bak
```

This command lists the contents of the backup directory, allowing us to confirm the file is correctly mounted.


## Retrieving File Details from the Backup {#retrieving-file-details-from-the-backup}

Before restoring, it’s helpful to check the logical names of the files within the \`.bak\` file. Use the following command to retrieve this information:

```sh
docker exec -it mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U SA -P YourPassword123 -Q "RESTORE FILELISTONLY FROM DISK = '/var/opt/mssql/backup/backup.bak';" -C
```

The output will show the logical names and paths. Here’s an example of what this output might look like:

```text
LogicalName       PhysicalName                                                    Type  FileGroupName  Size       ...
output_v2         C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\...\DATA\outputdev2.mdf  D     PRIMARY         ...
output_v2_log     C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\...\DATA\outputdev2_log.ldf  L     NULL           ...
```


## Restoring the Database {#restoring-the-database}

With the logical names in hand, you’re ready to restore the database. Here’s the command to execute the restore operation, specifying the logical file names and paths:

```sh
docker exec -it mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U SA -P YourPassword123 -Q \
    "RESTORE DATABASE outputdev2 FROM DISK = '/var/opt/mssql/backup/backup.bak' WITH MOVE 'output_v2' TO '/var/opt/mssql/data/outputdev2.mdf', MOVE 'output_v2_log' TO '/var/opt/mssql/data/outputdev2.ldf';" -C
```

This command restores the database from the backup, moving the MDF and LDF files to the specified paths within the container.

---

By following these steps, you should have a fully restored MSSQL database running in Docker. Adjust any paths or passwords as necessary for your environment.

> [!Note] Note
>
> "Note to self: Never touching MSSQL again—PostgreSQL all the way!"
