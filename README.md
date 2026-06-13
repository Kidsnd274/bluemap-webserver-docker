# BlueMap External Web Server with Docker

This setup uses MariaDB, Nginx, and PHP containers to host an external BlueMap web server. It also includes a BlueMap server instance that can be used to render your maps into the SQL database.

## 🚀 Setup (Docker Setup)

1. **Configure Environment Variables**
   Create a `.env` file in the root directory with these variables:
   ```env
   MARIADB_ROOT_PASSWORD=<your_password>
   MARIADB_DATABASE=BlueMap
   ```

2. **Configure SQL Connection for the Web Server**
   Configure your `web/sql.php` file to allow the web server to communicate with the database:
   ```php
   <?php
   
   // !!! SET YOUR SQL-CONNECTION SETTINGS HERE: !!!
   
   $driver   = 'mysql'; // 'mysql' (MySQL) or 'pgsql' (PostgreSQL)
   $hostname = 'db';
   $port     = 3306;
   $username = 'root';
   $password = '<your_password>';
   $database = 'BlueMap';
   
   // !!! END - DONT CHANGE ANYTHING AFTER THIS LINE !!!
   ```
   *Note: If you don't have this file, run `docker compose up -d` once to generate the initial structure, or use your existing BlueMap instance to generate it.*

3. **Launch the Application**
   ```bash
   docker compose up -d
   ```

---

## 🗺️ Rendering Worlds (BlueMap Server Setup)

Follow these steps if you want to use the included BlueMap service to render your maps.

### 1. BlueMap SQL Configuration
In `storages/sql.conf`, ensure the storage type is set to `sql` and the connection details are correct:
```conf
storage-type: sql
connection-url: "jdbc:mariadb://db:3306/bluemap"
connection-properties: {
    user: "root",
    password: "<your_password>"
}
driver-jar: "/app/data/mariadb-java-client-3.5.4.jar"
driver-class: "org.mariadb.jdbc.Driver"
```

### 2. Add the MariaDB JDBC Connector
BlueMap requires a driver to connect to MariaDB.
1. Download the [MariaDB Java Client](https://mariadb.com/downloads/connectors/).
2. Place the `.jar` file in the `data/` directory.
3. Ensure the `driver-jar` path in `storages/sql.conf` matches the filename you downloaded.

### 3. Configure Maps and Worlds
1. Place your Minecraft world folders into the `worlds/` directory.
2. Create a configuration file for each map in `config/maps/`.

**Example Map Configuration (`config/maps/my_world.conf`):**
```conf
# The path to the save-folder of the world to render.
world: "worlds/my_world"
dimension: "minecraft:overworld"
name: "My World"
sorting: 0
```
*Note: You can adjust the volume mappings for `worlds`, `config`, `data`, and `web` in the `docker-compose.yml` file if you want to use different local directories.*

---

## 🧹 Switching to Map-Only Mode (Post-Rendering)

If you have finished rendering your worlds and no longer wish to host the heavy Minecraft world files, you can switch to a "map-only" deployment. This saves significant disk space by removing the world data while keeping the rendered tiles in the SQL database.

1. **Disable the BlueMap Service**
   In `docker-compose.yml`, comment out the `bluemap` service:
   ```yaml
   # bluemap:
   #   image: ghcr.io/bluemap-minecraft/bluemap:v5.10
   #   ...
   ```

2. **Remove World Files**
   You can now safely delete the `worlds/` directory.

3. **Restart the Stack**
   ```bash
   docker compose up -d
   ```

The `db`, `php`, and `webserver` services will continue to run, serving the map data from the SQL database through the web interface.

---

## 📚 More Information

For advanced configurations regarding external webservers and SQL storage, refer to the [official BlueMap Wiki](https://bluemap.bluecolored.de/wiki/webserver/ExternalWebserversSQL.html).
