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

