# MyApp – Node.js Deployment with systemd and SSH

This project demonstrates how to deploy a minimal Node.js application on a remote Ubuntu server using SSH and systemd — without Docker, PM2, or cloud providers.

---

## 📦 Project Structure

```
myapp/
├── index.js              # Main Node.js HTTP server
├── package.json          # Node app metadata and dependencies
└── README.md             # Project documentation
```

---

## 🚀 Tech Stack

- **Node.js v18**
- **Ubuntu Server 22.04**
- **systemd**
- **SSH / SCP**
- **VirtualBox (local VM)**
- **WSL (Windows Subsystem for Linux)**

---

## 🛠️ Installation (Local VM Setup)

1. Create a virtual Ubuntu machine using **VirtualBox**.
2. Use `ip addr` and `scp` to copy files via SSH from WSL to the VM.
3. Install Node.js v18:
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install -y nodejs
   ```

---

## 📁 Deploying the App

From your host machine (WSL), copy the app to the server:

```bash
scp -r ./myapp deploy@192.168.56.10:~
```

SSH into the server and install dependencies:

```bash
ssh deploy@192.168.56.10
cd ~/myapp
npm install
```

---

## 🔄 systemd Service Setup

Create a service file:

```bash
sudo nano /etc/systemd/system/myapp.service
```

Paste this:

```ini
[Unit]
Description=My Simple Node.js App
After=network.target

[Service]
ExecStart=/usr/bin/node /home/deploy/myapp/index.js
Restart=always
User=deploy
Environment=NODE_ENV=production
WorkingDirectory=/home/deploy/myapp

[Install]
WantedBy=multi-user.target
```

Reload and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

---

## 📈 Monitoring the App

To check service status:

```bash
sudo systemctl status myapp
```

To view live logs:

```bash
journalctl -u myapp.service -f
```

---

## ✅ Output

Open in browser: `http://<your-vm-ip>:3000`  
Expected response:

```
✅ Server is running!
```

---

## 💡 Learnings

- Manually deploying and managing a Node.js app without cloud platforms
- Working with SSH and SCP
- Creating and debugging systemd services
- Monitoring server-side logs with `journalctl`
