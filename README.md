# Travel Memory App

Travel Memory App is a MERN stack application that helps users capture, store, and relive their travel history and personal travel experiences.

**Live Application:** [www.aws-ninjas.store](https://www.aws-ninjas.store)

![App Screenshot](https://github.com/user-attachments/assets/969db9b9-4257-4565-8e6d-b3e952ce77ca)

---

## 📋 Table of Contents
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [MongoDB Atlas Setup](#mongodb-atlas-setup)
- [Backend Setup](#backend-setup)
- [Frontend Setup](#frontend-setup)
- [Configuration](#configuration)
- [Deployment](#deployment)

---

## ✨ Features

- ✅ Add and save travel destinations with photos and experiences
- ✅ Store detailed travel information in MongoDB
- ✅ Browse and view complete travel history
- ✅ Access application via custom domain (AWS)
- ✅ Responsive web interface

---

## 🛠 Tech Stack

| Component | Technology |
|-----------|------------|
| **Frontend** | React, Node.js |
| **Backend** | Node.js, Express.js |
| **Database** | MongoDB Atlas |
| **Hosting** | AWS EC2 |
| **Web Server** | Nginx |
| **Tools** | Git, MongoDB Compass |

---

## 📦 Prerequisites

Before you start, make sure you have:

- ✅ AWS account with EC2 access
- ✅ Two EC2 instances (one for frontend, one for backend)
- ✅ MongoDB Atlas account (free tier available)
- ✅ Git installed locally
- ✅ Basic terminal/command line knowledge

---

## 🗄 MongoDB Atlas Setup

### Step 1: Create a Free Cluster

1. Go to [cloud.mongodb.com](https://cloud.mongodb.com)
2. Sign up or log in
3. Create a new project
4. Create a free cluster (M0 tier)

### Step 2: Create Database User

1. Click **Database Access** in the left sidebar
2. Click **Add New Database User**
3. Set a username and password
4. **Save these credentials** (you'll need them later)
5. Click **Add User**

### Step 3: Configure Network Access

1. Click **Network Access** in the left sidebar
2. Click **Add IP Address**
3. Enter `0.0.0.0/0` (allows access from anywhere - needed for EC2 instances)
4. Click **Confirm**

![MongoDB Network Setup](https://github.com/user-attachments/assets/fe70bf0b-4614-4f49-bc71-a1383a48a2a8)

### Step 4: Get Connection String

1. Click **Databases** in the left sidebar
2. Click **Connect** on your cluster
3. Select **Drivers**
4. Copy the connection string

![MongoDB Connection](https://github.com/user-attachments/assets/78d8654c-c4b6-45df-8ed5-92034701df8c)

**Your connection string will look like:**
```
mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/travelmemory?retryWrites=true&w=majority
```

---

## 🚀 Backend Setup

### Step 1: Launch Backend EC2 Instance

1. Go to AWS EC2 Dashboard
2. Click **Launch Instance**
3. Name it: `backend-master`
4. Choose **Ubuntu** (latest LTS)
5. Choose instance type: `t2.micro` (free tier eligible)
6. Launch instance

![EC2 Instances](https://github.com/user-attachments/assets/5ea7dfd3-5733-4827-bc7d-76444530d4cf)

### Step 2: Connect to Backend Instance

SSH into your backend EC2 instance and run the following commands:

#### Update Package Manager
```bash
sudo apt update
sudo apt upgrade -y
```

#### Install Required Tools
```bash
sudo apt install -y nodejs npm git nginx
```

#### Clone the Application
```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory/backend
```

#### Install Dependencies
```bash
npm install
```

### Step 3: Create Environment File

```bash
nano .env
```

Add the following lines (replace with your MongoDB credentials):

```env
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/travelmemory?retryWrites=true&w=majority
PORT=3000
```

**To save and exit:**
- Press `Ctrl + X`
- Press `Y`
- Press `Enter`

### Step 4: Run the backend as a systemd service (no PM2)

```bash
Create /etc/systemd/system/travelmemory-backend.service
```
- Add Env Details
  ```bash
  [Unit]
Description=TravelMemory Backend
After=network.target
[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/TravelMemory/backend
EnvironmentFile=/home/ubuntu/TravelMemory/backend/.env
ExecStart=/usr/bin/node index.js
Restart=always
RestartSec=5
[Install]
WantedBy=multi-user.target
```
- start the services
``` bash
sudo systemctl daemon-reload
sudo systemctl enable --now travelmemory-backend
sudo systemctl status travelmemory-backend
```

- Setup Nginx reverse proxy
- Edit /etc/nginx/sites-available/default, replace the default location / with:
  ```bash
  location / {
    proxy_pass http://localhost:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```
- Refer attached image

<img width="1470" height="956" alt="Screenshot 2026-10-12 at 1 58 35 PM" src="https://github.com/user-attachments/assets/9174e0d9-b295-4f84-b44e-025750e1747b" />



- start the services
```bash
sudo nginx -t
sudo systemctl restart nginx
```

- verify with below command
  ``` bash
  curl http://<backend-master-public-ip>/hello
  --> Hello World!
  ```
## 🎨 Frontend Setup

### Step 1: Launch Frontend EC2 Instance

1. Go to AWS EC2 Dashboard
2. Click **Launch Instance**
3. Name it: `frontend-master`
4. Choose **Ubuntu** (latest LTS)
5. Choose instance type: `t2.micro`
6. Launch instance

### Step 2: Connect to Frontend Instance

SSH into your frontend EC2 instance and run:

#### Update Package Manager
```bash
sudo apt update
sudo apt upgrade -y
```

#### Install Required Tools
```bash
sudo apt install -y nodejs npm git nginx
```

#### Clone the Application
```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory/frontend
```

#### Install Dependencies
```bash
npm install
```

### Step 3: Configure Backend URL (If Needed)

Edit your frontend configuration to point to your backend:

```bash
Open src/url.js and point baseUrl at the backend master (this is an interim value — you'll repoint it once the backend load balancer exists,
```

<img width="2940" height="1640" alt="image" src="https://github.com/user-attachments/assets/38d43f8d-0c06-4520-99a7-c8f24175ef8a" />

<img width="2940" height="1912" alt="image" src="https://github.com/user-attachments/assets/9cc4f7de-1f0f-4372-80c3-4b439ca09fe9" />

### Step 4: Build Frontend
```bash
npm install
npm run build
sudo rm -rf /var/www/html/*
sudo cp -r build/* /var/www/html/
sudo systemctl restart nginx
```

### Step 5: Verify the Baseline Before Imaging
```bash
http://<frontend-master-public-ip>
```

### Step 6: create AMIs

<img width="2918" height="674" alt="image" src="https://github.com/user-attachments/assets/37fcf80c-2c5f-4e8e-b3b8-a1b31632e189" />

### Step 7: Launch Instance #2 From Each AMI
 - Notice frontend-2 and backend-2 instances in the image was created from AMIs respectively
<img width="1463" height="447" alt="Screenshot 2026-10-12 at 2 20 54 PM" src="https://github.com/user-attachments/assets/0691ab82-3eee-43dd-b104-0387428ee7bf" />

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name _;

    root /home/ubuntu/TravelMemory/frontend/build;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    location /api {
        proxy_pass http://<your-backend-ec2-ip>:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Step 6: Restart Nginx
```bash
sudo systemctl restart nginx
```

Frontend should now be accessible at `http://<your-frontend-ec2-ip>`

---

## ⚙️ Configuration

### Environment Variables

**Backend `.env` file:**
```env
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/travelmemory?retryWrites=true&w=majority
PORT=3000
NODE_ENV=production
```

### MongoDB Compass (Optional)

To view and manage your database locally:

1. Download [MongoDB Compass](https://www.mongodb.com/products/tools/compass)
2. Paste your MongoDB connection string
3. Click **Connect**
4. You can now browse your database

---

## 🌐 Deployment & Domain Setup

1. Get a domain from a domain registrar (or use AWS Route 53)
2. Point the domain A record to your frontend EC2 instance's public IP
3. Update your Nginx configuration with your domain name
4. Configure SSL with Let's Encrypt (optional but recommended):

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com
```

---

## 🔧 Troubleshooting

### MongoDB Connection Error
- ✅ Check your MongoDB URI in `.env`
- ✅ Verify username and password
- ✅ Ensure IP address `0.0.0.0/0` is added in MongoDB Atlas Network Access

### Backend Not Starting
- ✅ Run `npm install` again
- ✅ Check `.env` file exists and has correct values
- ✅ Check port 3000 is not already in use: `sudo lsof -i :3000`

### Frontend Not Loading
- ✅ Check Nginx is running: `sudo systemctl status nginx`
- ✅ Verify backend URL in frontend config
- ✅ Check browser console for errors (F12)

### EC2 Connection Issues
- ✅ Verify security group allows inbound traffic on ports 80, 443, 3000
- ✅ Check EC2 instance is running
- ✅ Verify SSH key permissions: `chmod 400 your-key.pem`

---

## 📚 Useful Resources

- [MongoDB Atlas Documentation](https://docs.mongodb.com/atlas/)
- [AWS EC2 Guide](https://docs.aws.amazon.com/ec2/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [React Documentation](https://react.dev/)
- [Express.js Guide](https://expressjs.com/)

---

## 📝 Notes

- The backend listens on port `3000` by default
- Ensure MongoDB Atlas network allows your EC2 instance IPs
- Keep your MongoDB credentials secure
- Regularly backup your MongoDB database
- Monitor EC2 instance costs

---

## 📄 License

Add your license here

## 👨‍💻 Author

[Your Name / Organization]

---

**Last Updated:** October 12, 2026
