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