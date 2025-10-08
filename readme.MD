# 🐳 Docker Ports & Root Access – Complete Simple Notes

## 🔹 Left Side Port (Host Port)

- Command example:
  ```bash
  docker run -p 80:80 nginx
  ```
- Left side port = Host or EC2 system port.
- Ports **1–1024** are system (root) ports → normal user can't open them.
- But **Docker Daemon runs as root**, so:
  - Even if you are a normal user,
  - Docker Daemon opens that port **on your behalf**.
- ✅ So `-p 80:80` works because Docker Daemon (root) does it for you.

If you use port **above 1024** (like 8080, 5000, etc):
- It still works the same way.
- Root permission not needed, but Docker Daemon still manages it.

---

## 🔹 Right Side Port (Container Port)

- Right side port = port inside the container.
- Docker Daemon creates container → by **default** gives **root access inside container**.
- That’s why Nginx (or any app) can run on port 80 inside the container.

Example:
- `-p 8080:80` → host 8080 → container 80 (Nginx runs inside container on port 80 as root).

---

## 🔹 Root User Concept (Very Important)

| Level | Who | Root Access | Notes |
|--------|-----|--------------|-------|
| Host (EC2) | You (normal user) | ❌ No | You start the command only |
| Docker Daemon | Runs as root | ✅ Yes | Opens host ports, creates container |
| Container (inside) | Has its own root user | ✅ Yes (by default) | Root only inside container, cannot affect host |
| Other Containers | ❌ | ❌ | One container’s root cannot control another |

---

## 🔹 If You Don’t Want Root Inside Container

You can disable root inside container by specifying a user:
```bash
docker run -u 1000 -p 8080:80 nginx
```
or inside Dockerfile:
```dockerfile
USER 1000
```
→ Then container runs with normal user, not root.

---

## 🧠 Final Summary

- Left side port → handled by Docker Daemon (root).  
- Right side port → inside container (by default root user).  
- Each container = isolated small system (sandbox).  
- Root inside container ≠ root on host.  
- You can use any port above 1024 to avoid system ports.  
- You can disable root user inside container if needed.

---

✅ **In Simple Words:**
> Docker Daemon creates containers as root by default.  
> Even though you are a normal user, it opens ports and gives root inside container safely (isolated).  
> If you don’t want root → you can specify another user.

🧩 Your question:

“Why can’t we add a volume to a stopped container?
Why do we need to delete and create a new one to attach a volume?”

💡 The short answer:

Because volume mounting is part of the container’s filesystem configuration,
and that configuration is locked once the container is created.

You can’t change it afterward — even if the container is stopped.

🧠 Let’s understand how Docker containers are built

When you run:

docker run -d --name mongodb -v mongodb:/data/db mongodb:v1


Docker creates 3 things:

A read-only image layer (mongodb:v1)

A read-write container layer (unique to that container)

Any mounted volumes you specify (-v mongodb:/data/db)

Then Docker merges these layers together to form the container’s filesystem view.

📦 This "filesystem view" is frozen when the container is created.

🧱 Why you can’t modify it later

Once a container exists (even if stopped), Docker treats its filesystem layout (including mounts) as immutable metadata.

That means:

You can start/stop the container.

But you cannot change its mount points (volumes, ports, network modes, etc.)

Because all those things are defined in the container’s creation metadata stored under /var/lib/docker/containers/<container_id>/config.v2.json.

So Docker will say:

“If you want a different volume, create a new container.”

🧰 What you can do:

If you want to use a new volume with the same data/config:

Stop and remove the old container:

docker stop mongodb
docker rm mongodb


Recreate it with the desired volume:

docker run -d --name mongodb -v mongodb:/data/db mongodb:v1


Docker will reattach that volume cleanly.

💬 Analogy (easy way to remember)

Think of a container like a running computer made from a snapshot (the image):

When you create it, you decide which drives (volumes) to plug in.

Once it’s built, you can’t suddenly open the computer case and attach another drive — you’d have to rebuild the machine (new container) with the drive included.

That’s exactly how Docker containers work.

| Action              | Allowed on stopped container? | Why                          |
| ------------------- | ----------------------------- | ---------------------------- |
| Start/Stop          | ✅ Yes                         | Doesn’t change configuration |
| Rename              | ✅ Yes                         | Metadata only                |
| Attach new volume   | ❌ No                          | Filesystem layout fixed      |
| Change network mode | ❌ No                          | Network namespace fixed      |
| Commit as new image | ✅ Yes                         | Creates new image snapshot   |



🐳 1️⃣ docker system df

Purpose:

Shows disk usage of Docker objects — how much space containers, images, volumes, and build cache are using.

What it shows:

    Total size of all images, containers, and volumes
    Space that is reclaimable (unused/dangling resources)

Command: 

docker system df

Sample Output:

TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          12        3         4.5GB     3.2GB
Containers      8         2         400MB     250MB
Local Volumes   5         3         2.1GB     1.0GB
Build Cache                          300MB     300MB


Use case:

  Check disk usage before cleaning up
  Plan docker system prune or docker volume prune safely

You might see a column called RECLAIMABLE.

    Reclaimable = the amount of disk space that Docker could free if you removed unused resources
    In other words, unused or dangling objects that are safe to delete

TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          12        3         4.5GB     3.2GB
Containers      8         2         400MB     250MB
Local Volumes   5         3         2.1GB     1.0GB
Build Cache                          300MB     300MB

SIZE → total space used by that type
RECLAIMABLE → the portion of that space you can free if you remove unused or dangling items

✅ Example:

Images: total 4.5GB
    3.2GB is reclaimable → you can recover 3.2GB if you run:

docker image prune       # removes dangling images
# or more aggressively
docker system prune -a   # removes unused images, stopped containers, and networks

  Containers: 400MB total, 250MB reclaimable → stopped containers that are safe to delete
  Volumes: 2.1GB total, 1.0GB reclaimable → volumes not attached to any container
  Build Cache: 300MB reclaimable → old build layers that are no longer needed

So yes ✅, RECLAIMABLE is the space you could free by running prune commands.



Yes ✅ — exactly right!

Let me clarify step by step:

1. Deleting reclaimable (3.2GB) images
      You are deleting dangling(Intermediate-layers) / unused images only.
      Original / in-use images (1.3GB) remain untouched.
      Running containers will not be affected.
2. Next time you build or pull an image
      Docker will recreate the image layers as needed.
      So total size may increase again depending on what you build/pull.

3. Effect on disk space

      Deleting reclaimable images frees actual disk space immediately.
      That 3.2GB becomes available on your system.

✅ Summary:

Deleting reclaimable = safe, frees disk space
Original images = safe, not deleted
New builds = may increase disk usage again

So your understanding is correct — you will get more free space on your disk by deleting the reclaimable 3.2GB.

🐳 2️⃣ docker stats

Purpose:

Shows real-time resource usage of running containers — CPU, memory, network, and disk I/O.

Command:

docker stats

Sample Output (live streaming):

CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT     NET I/O      BLOCK I/O
a1b2c3d4e5f6   mongodb    2.34%     256MiB / 2GiB        1.2MB / 3.4MB   12MB / 8MB
b2c3d4e5f6g7   nginx      0.12%     12MiB / 512MiB       100kB / 200kB   0B / 0B


Use case:

  Monitor container performance live
  Detect high CPU/memory usage
  Debug performance issues