# BlueMap External Web Server with Docker
You can use the SQL option as well. This docker-compose setup uses MariaDB, nginx, and php containers for the external web server. It also provides the BlueMap server to render your map if you want.

## Setup (Docker Setup)
Create a .env file with these variables
```
MARIADB_ROOT_PASSWORD=<REDACTED>
MARIADB_DATABASE=BlueMap
```

After that, configure your sql.php file (`web/sql.php`)
```
<?php

// !!! SET YOUR SQL-CONNECTION SETTINGS HERE: !!!

$driver   = 'mysql'; // 'mysql' (MySQL) or 'pgsql' (PostgreSQL)
$hostname = 'db';
$port     = 3306;
$username = 'root';
$password = '<REDACTED>';
$database = 'BlueMap';

// !!! END - DONT CHANGE ANYTHING AFTER THIS LINE !!!
```

If you don't have this file, you need the BlueMap server to generate it. Either generate it using the provided BlueMap server or the one in your Minecraft Server instance.

Once that's done it should work. Run `docker compose up -d` to launch the application.

> ⚠️ If you don't want to use the BlueMap Server, you can comment out the entire container in the `docker-compose.yml` file

## Setup (BlueMap Server Setup)
Follow these steps if you want to use the BlueMap server in this Docker application to render your maps.

### BlueMap SQL Configuration
First, you need to setup the SQL configuration. After following the [Docker Setup](#setup-docker-setup), make sure to launch the Docker application with `docker compose up -d` at least once to generate all the configuration files.

In `storages/sql.conf`, make sure these settings are set correctly.
```
##                          ##
##         BlueMap          ##
##      Storage-Config      ##
##                          ##

storage-type: sql
connection-url: "jdbc:mariadb://db:3306/bluemap"
connection-properties: {
    user: "root",
    password: "<REDACTED>"
}
driver-jar: "/app/data/mariadb-java-client-3.5.4.jar"
driver-class: "org.mariadb.jdbc.Driver"
```

Next you will need to download the MariaDB JDBC Connector and provide it to the BlueMap service.\

First, download the file using this [link](https://mariadb.com/downloads/connectors/). After that, place the file in `data`, alongside `minecraft-client.jar`.

Make sure your `driver-jar` config (in `storages/sql.conf` has the correct file name and points to the driver .jar file. It should look something like this:
driver-jar: "/app/data/mariadb-java-client-3.5.4.jar"
```
driver-jar: "/app/data/mariadb-java-client-3.5.4.jar"
```

### BlueMap Map Configuration
My configuration is configured to have multiple world maps in the `worlds` folder. So basically, I just dump all my worlds in that folder and configure the maps accordingly.

This is an example of one of the maps.

Map Name: Darwin_Server\
World Folder Location: `worlds/Darwin_Server`\
BlueMap Configuration Example:
```
##                          ##
##         BlueMap          ##
##        Map-Config        ##
##                          ##

# The path to the save-folder of the world to render.
#  (If this is not defined (commented out or removed), the map will be only registered to the web-server and the web-app
#   but not rendered or loaded by BlueMap. This can be used to display a map that has been rendered somewhere else.)
world: "worlds/Darwin_Server"
dimension: "minecraft:overworld"
name: "Darwin_Server"
sorting: 0
```

The important field is `world:` as that specifies the location of the world folder. You can tweak the folders accordingly in the `docker-compose.yml` file.
