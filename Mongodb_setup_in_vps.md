# 🚀 MongoDB VPS Setup + Secure Access Guide

## 🧱 1. Install MongoDB on VPS

```bash
sudo apt-get install gnupg curl
curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor

echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list

sudo apt-get update
sudo apt-get install -y mongodb-org
```

---

## ▶️ 2. Start MongoDB

```bash
sudo systemctl daemon-reload
sudo systemctl start mongod
sudo systemctl enable mongod
sudo systemctl status mongod
```

---

## 🔐 3. Setup Authentication

```bash
mongosh
```

```js
use admin

db.createUser({
  user: "admin",
  pwd: "yourPassword",
  roles: ["root"]
})
```

```bash
exit
```

Enable auth:

```bash
sudo nano /etc/mongod.conf
```

```yaml
security:
  authorization: enabled
```

```bash
sudo systemctl restart mongod
```

---

## 🔑 4. Login

```bash
mongosh -u admin -p yourPassword --authenticationDatabase admin
```

---

## 🗄️ 5. Create Project DB

```js
use myDatabase

db.createUser({
  user: "appUser",
  pwd: "appPassword",
  roles: [{ role: "readWrite", db: "myDatabase" }]
})
```

---

## 🔐 6. Secure Connection (SSH Tunnel)

Keep config:

```yaml
net:
  bindIp: 127.0.0.1
```

Create tunnel: (in local pc and keep running)

```bash
ssh -L 27017:localhost:27017 root@YOUR_VPS_IP
```

---

## 🖥️ 7. MongoDB Compass

Connection URI:

```
mongodb://admin:yourPassword@localhost:27017/admin?authSource=admin
```

---

## ⚠️ 8. Common Errors

### ❌ Authentication failed
- Wrong password
- Wrong auth DB
- Tunnel not running
- Local MongoDB conflict

Fix:
- Use correct URI
- Restart tunnel
- Stop local MongoDB:
```bash
net stop MongoDB
```

---

### ❌ ECONNREFUSED
MongoDB not running:
```bash
sudo systemctl restart mongod
```

---

### ❌ YAML config error
Fix indentation:
```yaml
security:
  authorization: enabled
```

---

### ❌ Local MongoDB conflict
Use different port:
```bash
ssh -L 27018:localhost:27017 root@VPS_IP
```

---

## 🔒 9. Security Checklist

- Enable auth
- Strong password
- Keep bindIp local
- Use SSH tunnel
- Do not expose port

---

## 🔌 10. Disconnect

Stop tunnel:
```
CTRL + C
```

---

## 🎉 DONE

Secure MongoDB VPS + Compass setup complete.
