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
