# 🐬 MySQL Automatic Replication

Automatic **Master–Replica** MySQL replication setup using **Docker Compose**.

## 📘 Description

This project sets up replication between two MySQL nodes (**Master** and **Replica**) using **Docker Compose** and custom Docker images.

Before starting, the user must select the server role (**Master** or **Replica**). Default passwords can also be customized in the configuration.

### Node Roles

| Node | Description |
| :--- | :--- |
| **Master** | Main database node |
| **Replica** | Secondary node copying data from **Master** |

---

## ⚙️ Setup Steps

### 1️⃣ Install Docker

Ensure Docker is installed:

```bash
sudo snap install docker
```

### 2️⃣ Clone the Project

Clone the repository and navigate to the project folder:

```bash
git clone https://github.com/<your_username>/mysql-auto-replication.git
cd mysql-auto-replication
```

### 3️⃣ Decide the Role

Determine the server's role:

* **Master** → Main node
* **Replica** → Secondary node

### 4️⃣ Edit Configuration (Optional)

For the Replica node, you *must* replace `MASTER_IP` in `replica/init.sql` with the Master’s IP address.

**File path:** `mysql-auto-replication/replica/init.sql`

**Example:**

```sql
CHANGE REPLICATION SOURCE TO
  SOURCE_HOST='10.1.1.1',
  SOURCE_USER='repl',
  SOURCE_PASSWORD='replpass',
  SOURCE_AUTO_POSITION=1;
START REPLICA;
```

### 5️⃣ Navigate to the Correct Directory

* **Master:**

    ```bash
    cd mysql-auto-replication
    ```

* **Replica:**

    ```bash
    cd mysql-auto-replication/replica
    ```

### 6️⃣ Start Container with Build

Start the service using the appropriate configuration file:

* **Master:**

    ```bash
    docker-compose -f docker-compose.master.yml up -d --build
    ```

* **Replica:**

    ```bash
    docker-compose -f docker-compose.replica.yml up -d --build
    ```

### 7️⃣ Verify Replication

Check the Replica node logs to ensure it connects to the master:

```bash
docker-compose -f docker-compose.replica.yml logs -f
```

---

## 🔧 Notes & Tips

* Each server is either **Master** or **Replica**, never both.
* `MASTER_IP` must be correct for replication to work.
* Passwords and credentials can be customized.
* Ensure network connectivity if servers are on different machines.

### Container Management

Stop and remove containers:

```bash
docker-compose -f docker-compose.master.yml down
docker-compose -f docker-compose.replica.yml down
```

Reset volumes if needed:

```bash
docker volume prune
