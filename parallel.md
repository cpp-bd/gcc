### Parallel Compilation of C++ Source Codes
Awesome! Building **Redis** source code in parallel across **4 Linux servers** can dramatically speed up compilation, especially on lower-power machines. Redis is a C-based project that uses a Makefile, so it works great with **distributed compilation tools** like `distcc`.

---

## ✅ Step-by-Step Guide to Building Redis in Parallel on 4 Servers

### 🧱 Assumptions

- You have 4 Linux servers: `192.168.1.101` to `192.168.1.104`
- You want to build Redis on the **main node** (`192.168.1.101`), using the **CPU power of all 4 nodes**
- All nodes are in a trusted local network
- You want to compile Redis source code (from https://github.com/redis/redis)

---

## 🧰 Tools Needed

- `distcc`: For distributed compilation
- `gcc`/`make`: Already needed for Redis build
- `rsync` (optional): For code syncing if needed

---

## 🛠️ Step 1: Install distcc on All Servers

Run this on **all 4 servers**:

```bash
sudo apt update
sudo apt install distcc gcc g++ make -y
```

---

## ⚙️ Step 2: Set Up Worker Servers (`192.168.1.102` - `192.168.1.104`)

On each worker node:

1. Allow the master IP (`192.168.1.101`) in the config (optional):
```bash
sudo nano /etc/distcc/hosts
```
Add:
```
192.168.1.101
```

2. Start the `distcc` daemon:
```bash
sudo distccd --daemon --allow 192.168.1.101 --log-level info
```

You can also make it start on boot if you want:
```bash
sudo systemctl enable distcc
```

---

## 🧠 Step 3: On Master Server (`192.168.1.101`)

### 3.1 Clone Redis:
```bash
git clone https://github.com/redis/redis.git
cd redis
```

### 3.2 Export distcc environment:
```bash
export DISTCC_HOSTS="192.168.1.102 192.168.1.103 192.168.1.104"
export CC="distcc gcc"
export CXX="distcc g++"
```

You can also add this to `.bashrc` or a build script.

---

## ⚡ Step 4: Compile Redis Using Distributed Make

```bash
make -j16
```

- `-j16`: Run 16 compilation jobs in parallel (adjust based on total cores across your 4 servers).

---

## 🧪 Step 5 (Optional): Test the Build

```bash
src/redis-server --version
```

This will show the Redis version if it compiled successfully.

---

## 🔍 Troubleshooting

- If `distcc` is not working, try compiling with logging:
  ```bash
  export DISTCC_LOG=/tmp/distcc.log
  ```
  Check `/tmp/distcc.log` for errors.
  
- You can monitor distcc activity with:
  ```bash
  distccmon-text 1
  ```

---

## 📦 Optional: Script It All

Would you like a Bash script to:
- Set up distcc across the nodes
- Trigger compilation
- Monitor progress

Let me know — I can generate that for your exact IPs.
