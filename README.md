# docker
Explore containerization with our Docker Tutorials repository. Perfect for beginners and seasoned devs alike, it guides you from basic setups to advanced deployments, offering practical examples and best practices.

## Get Started
> For video tutorial, watch here on YouTube: https://www.youtube.com/watch?v=XQJNqh0Mnh8
1. Download Docker from https://www.docker.com/products/docker-desktop/
   > Mac users should verify their chip type before downloading by going to the Apple icon on the top left corner and selecting ```About This Mac.```
2. Install it on [Windows](https://docs.docker.com/desktop/install/windows-install/) or [Mac](https://docs.docker.com/desktop/install/mac-install/)
3. Start Docker Desktop
   > Windows users may find a Desktop icon to Start Docker Desktop and the Mac users can find it at ```Applications``` folder
5. Open a terminal/command prompt:
   - Mac Users: Open a new Terminal window ```Applications -> Utilities -> Terminal```
   - Windows Users: Go to Search in ```Start``` menu and find ```Command Prompt```
6. Pull ```MySQL``` image: Once the Terminal or Command Prompt is opened, please type ```docker pull mysql``` and Press Enter
7. Run ```MySQL``` instances or containers: Based on our requirements, we need to initiate three MySQL servers, each using a distinct port number.
   - In you Terminal or Command Prompt window type the following command/s and presee Enter
     - Running Server 1: 
       - ```docker run --name server1 -e MYSQL_ROOT_PASSWORD=_your_password_ -p 3307:3306 -d mysql```
     - Running Server 2: 
       - ```docker run --name server2 -e MYSQL_ROOT_PASSWORD=_your_password_ -p 3308:3306 -d mysql```
     - Running Server 3: 
       - ```docker run --name server3 -e MYSQL_ROOT_PASSWORD=_your_password_ -p 3309:3306 -d mysql```
     > You can run as many servers as you like using the above command but you should change the name and port number
8. Verify the running status of your MySQL servers by typing ```docker ps``` and it will return a list of servers which are currently running.
9. Login to the MySQL Servers:
   > The password is not visible when you type.
11. Open three Terminal or Command Prompt Windows and execute the following commands:
    - Terminal/Command Prompt 1: ```docker exec -it server1 mysql -P 3307 -u root -p``` and press enter and then your password. 
    - Terminal/Command Prompt 2: ```docker exec -it server2 mysql -P 3308 -u root -p``` and press enter and then your password.
    - Terminal/Command Prompt 3: ```docker exec -it server3 mysql -P 3309 -u root -p``` and press enter and then your password.
## phpMyAdmin - A web interface for MySQL and MariaDB.
Connecting to a MySQL instance running in a Docker container via PhpMyAdmin. We are assuming you will create a new MySQL container to access via phpMyAdmin. 
1. **Create a Docker Network:** ```docker network create mysql-network```
2. **Run MySQL Container:** ```docker run --name PHPMyAdmin -e MYSQL_ROOT_PASSWORD=123456 --network mysql-network -p 3307:3306 -d mysql```
3. **Run PhpMyAdmin Container:** ```docker run --name MySQLAdmin -d --network mysql-network -e PMA_HOST=PHPMyAdmin -p 8080:80 phpmyadmin/phpmyadmin```
4. **Access PhpMyAdmin:** Open a browser window and navigate to ```http://localhost:8080``` and login with user ```root``` and password ```123456```

## Using Apache Cassandra with Docker
1. Pull the Docker image:
   ```bash
   docker pull cassandra
   ```
2. Create a Docker Network:
   ```bash
   docker network create cassandra-net
   ```
3. Start the Seed Node:
   ```bash
   docker run --name cassandra-seed   --network cassandra-net   --hostname cassandra-seed   -p 9043:9042   -m 2g --memory-swap 3g   -e MAX_HEAP_SIZE=1024M   -e HEAP_NEWSIZE=256M   -d cassandra
   ```
4. Start two additional nodes:
   ```bash
   docker run --name cassandra-node1   --network cassandra-xunw   --hostname cassandra-node1   -p 9044:9042   -m 2g --memory-swap 3g   -e CASSANDRA_SEEDS=cassandra-seed   -e MAX_HEAP_SIZE=1024M   -e HEAP_NEWSIZE=256M   -d cassandra
   ```
   ```bash
   docker run --name cassandra-node2   --network cassandra-net   --hostname cassandra-node2   -p 9045:9042   -m 2g --memory-swap 3g   -e CASSANDRA_SEEDS=cassandra-seed   -e MAX_HEAP_SIZE=1024M   -e HEAP_NEWSIZE=256M   -d cassandra
   ```
5. Inspect the network configuration:
    ```bash
   docker network inspect cassandra-net
    ```
6. Check the status of your nodes:
    ```bash
    docker exec -it cassandra-seed nodetool status
    ```
   > ***Note:*** Identify which node is the seed node:
   >
   > ```bash
   > docker exec -it cassandra-node1 cat /etc/cassandra/cassandra.yaml | grep -i seeds
   > ```
   > ```bash
   > docker exec -it cassandra-node2 cat /etc/cassandra/cassandra.yaml | grep -i seeds
   > ```
   > ```bash
   > docker exec -it cassandra-seed cat /etc/cassandra/cassandra.yaml | grep -i seeds
   > ```
7. Start Bash Shell in Cassandra Container (Seed Node):
   ```bash
   docker exec -it cassandra-seed bash
   ```    
8. Update the package index from sources:
   ```bash
   apt update
   ```
9. Install the ```nano``` editor:
   ```bash
   apt install nano
   ```
9. Increment request timeout:
   8.1. Navigate to cassandra folder
   ```bash
   cd ~/.cassandra/
   ```
   > If it fails to switch to the ```.cassandra``` folder, then you should create it ```mkdir -p ~/.cassandra```
   
   8.2. Create or edit the ```cqlshrc``` file:
   ```bash
   nano ~/.cassandra/cqlshrc
   ```
   8.4. Add the following two lines and save and exit ```nano``` (```Ctrl + O``` to save, Enter, then ```Ctrl + X``` to exit).
   ```bash
   [authentication]
   username = your_username
   password = your_password
   
   [connection]
   request_timeout = 6000
   ```
6. Switch to Cassandra SQL Prompt:
   ```bash
   cqlsh
   ```
8. Verify the Data Center Names:
   ```bash
   SELECT data_center FROM system.local;
   ```
   
   ```bash
   SELECT peer, data_center FROM system.peers;
   ``` 
9. Create a ```KEYSPACE```:
   ```bash
   CREATE KEYSPACE iot_data WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter1': 3};
   ```
10. Verify if the ```KEYSPACE``` named ```iot_data``` is created
   ```bash
   DESCRIBE KEYSPACES;
   ```
11. Use the ```iot_data``` Keyspace:
    ```bash
    USE iot_data
    ```
12. Create a table
   ```sql
   CREATE TABLE example_table (
    id UUID PRIMARY KEY,
    name text,
    age int,
    email text
   );

   ```
12. Verify if the table is created:
    ```bash
    DESCRIBE TABLES;
    ```
13. Verify if the data is replicated.
    13.1. Enter the node1 by opening a new Command Prompt/Terminal window and type ```docker exec -it cassandra-node1 cqlsh``` and press Enter. This will take you to the Cassandra SQL Shell and now you can list the KEYSPACES created in the Seed Node (cassandra-seed) ```DESCRIBE KEYSPACES;```. The ```iot_data``` Keyspace should list.

14. Insert some data:
    ```sql
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Alice Johnson', 29, 'alice.johnson@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Bob Smith', 34, 'bob.smith@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Charlie Brown', 22, 'charlie.brown@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'David Wilson', 45, 'david.wilson@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Eva Adams', 31, 'eva.adams@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Frank Miller', 28, 'frank.miller@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Grace Lee', 37, 'grace.lee@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Henry Walker', 26, 'henry.walker@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Isabella Martinez', 33, 'isabella.martinez@example.com');
    INSERT INTO example_table (id, name, age, email) VALUES (uuid(), 'Jack Davis', 40, 'jack.davis@example.com');

    ```
### Primary Key Structure (Partition Key)
   ```sql
   CREATE TABLE users (
    user_id UUID,
    name text,
    age int,
    email text,
    country text,
    PRIMARY KEY (country, user_id)
   );
   ```
   > - ```country``` is the **partition** key.
   >    - The partition key (```country```) determines how the data is distributed across nodes. Rows with the same country value are stored on the same node, making queries that filter by country efficient.
   > - ```user_id``` is the clustering column.

   ```sql
   INSERT INTO users (user_id, name, age, email, country) VALUES (uuid(), 'Alice Johnson', 29, 'alice.johnson@example.com', 'USA');
   INSERT INTO users (user_id, name, age, email, country) VALUES (uuid(), 'Bob Smith', 34, 'bob.smith@example.com', 'Canada');
   INSERT INTO users (user_id, name, age, email, country) VALUES (uuid(), 'Charlie Brown', 22, 'charlie.brown@example.com', 'USA');
   INSERT INTO users (user_id, name, age, email, country) VALUES (uuid(), 'David Wilson', 45, 'david.wilson@example.com', 'UK');
   ```
#### Tracing Queries
   ```sql
   TRACING ON;
   SELECT * FROM users WHERE country = 'USA';
   TRACING OFF;
   ```
