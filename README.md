# Hosting and Cleaning Up a Node.js Application on Ubuntu

## 📌 Hosting Node.js Application on Ubuntu

### 1. Install Node.js 16.x (LTS)

```bash
# Update package index
sudo apt update

# Add NodeSource repository for Node.js 16.x
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -

# Install Node.js and npm
sudo apt install -y nodejs

# Verify installation
node -v
npm -v
```

---

### 2. Set Up Sample Node.js Application

```bash
# Create a directory for your app
mkdir sample-app
cd sample-app

# Initialize a new Node.js project
npm init -y

# Install Express.js
npm install express
```

---

### 3. Create `app.js` for the Server

```bash
nano app.js
```

Paste:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

Save and exit (`CTRL + X`, then `Y`, then `Enter`).

---

### 4. Run the Application

```bash
node app.js
```

Visit in your browser:

```
http://your-server-ip:3000
```

---

### 5. Optional: Add a Public Page

```bash
mkdir public
touch public/register.html
```

Place your HTML content inside `public/register.html`.

---

## 🌍 Make Your Application Public

### Option 1: Use Port 80 Directly (Not Recommended for Production)

```bash
sudo node app.js
```

---

### Option 2: Reverse Proxy with Nginx (Recommended)

```bash
# Install Nginx
sudo apt install -y nginx

# Start and enable Nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

Edit the Nginx config:

```bash
sudo nano /etc/nginx/sites-available/default
```

Replace with:

```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Test and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🐳 Optional: Dockerize the Application

```bash
# Install Docker
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

Log out and log back in.

Create `Dockerfile`:

```dockerfile
FROM node:16
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

Build and run:

```bash
docker build -t sample-app .
docker run -p 3000:3000 sample-app
```

---

## 🧹 Cleanup Instructions

### 1. Remove Node.js and npm

```bash
sudo apt remove --purge nodejs
sudo apt autoremove --purge
sudo apt clean
rm -rf /path/to/sample-app/node_modules
npm cache clean --force
```

---

### 2. Remove Docker Resources

```bash
# Stop and remove containers
docker ps
docker stop <container_id>
docker rm <container_id>

# Remove images
docker images
docker rmi <image_id>

# Remove unused volumes and networks
docker volume prune
docker network prune
docker image prune
docker system prune -a
```

---

### 3. Remove Nginx

```bash
sudo systemctl stop nginx
sudo apt remove --purge nginx
sudo apt autoremove --purge
sudo apt clean
sudo rm -rf /etc/nginx
```

---

### 4. Remove the App Directory

```bash
cd ~
rm -rf sample-app
```

---

### 5. General Cleanup

```bash
sudo apt autoremove
sudo apt remove --purge
sudo apt clean
```

---

### 6. Remove Docker Group Access

```bash
sudo gpasswd -d $USER docker
sudo reboot
```

---

### 7. Kill Remaining Node.js Processes

```bash
ps aux | grep node
kill <PID>
```

---

### 8. Search and Remove Leftover `node_modules`

```bash
find / -name "node_modules"
rm -rf /path/to/node_modules
```

---

✅ Following these steps will let you **host** a Node.js app on Ubuntu and **fully clean up** the environment when done.
