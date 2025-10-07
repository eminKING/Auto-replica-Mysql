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

Use the official repository method to install Docker Engine for better stability and integration (recommended for production on Ubuntu/Debian OR... just use 'sudo snap install docker' ._.):

```bash
# 1. Update packages and install dependencies
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release -y

# 2. Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3. Add the Docker repository to APT sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine and related tools (including Compose plugin)
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# 5. Add current user to the 'docker' group to run commands without sudo (requires re-login)
sudo usermod -aG docker $USER

# 6. Verify installation
docker run hello-world
```

### 2️⃣ Clone the Project

Clone the repository and navigate to the project folder:

```bash
git clone https://github.com/eminKING/Auto-replica-Mysql.git
cd Auto-replica-Mysql
```

### 3️⃣ Decide the Role

Determine the server's role:

* **Master** → Main node
* **Replica** → Secondary node

### 4️⃣ Edit Configuration (Optional)

For the Replica node, you *must* replace `MASTER_IP` in `replica/init.sql` with the Master’s IP address.

**File path:** `Auto-replica-Mysql/replica/init.sql`

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
    cd Auto-replica-Mysql/master
    ```

* **Replica:**

    ```bash
    cd Auto-replica-Mysql/replica
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
