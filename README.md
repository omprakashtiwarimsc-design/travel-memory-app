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

**MongoDB Network Access Configuration:**
![MongoDB Network Setup](https://github.com/user-attachments/assets/fe70bf0b-4614-4f49-bc71-a1383a48a2a8)

### Step 4: Get Connection String

1. Click **Databases** in the left sidebar
2. Click **Connect** on your cluster
3. Select **Drivers**
4. Copy the connection string

**MongoDB Connection String Page:**
![MongoDB Connection](https://github.com/user-attachments/assets/78d8654c-c4b6-45df-8ed5-92034701df8c)

**Your connection string will look like:**
```
mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/travelmemory?retryWrites=true&w=majority
```
<img width="1174" height="120" alt="image" src="https://github.com/user-attachments/assets/abf24039-274a-4e04-93e6-10bf493e34a1" />

---

## 🚀 Backend Setup

### Step 1: Launch Backend EC2 Instance

1. Go to AWS EC2 Dashboard
2. Click **Launch Instance**
3. Name it: `backend-master`
4. Choose **Ubuntu** (latest LTS)
5. Choose instance type: `t2.micro` (free tier eligible)
6. Launch instance

**AWS EC2 Instances Dashboard:**
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

### Step 4: Run the Backend as a systemd Service

Create the systemd service file:

```bash
sudo nano /etc/systemd/system/travelmemory-backend.service
```

Add the following configuration:

```ini
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

Save and exit (Ctrl + X, Y, Enter)

Start the backend service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now travelmemory-backend
sudo systemctl status travelmemory-backend
```

### Step 5: Setup Nginx Reverse Proxy

Edit the Nginx default configuration:

```bash
sudo nano /etc/nginx/sites-available/default
```

Replace the location block with:

```nginx
location / {
    proxy_pass http://localhost:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

**Nginx Configuration File Example:**
![Nginx Configuration](https://github.com/user-attachments/assets/9174e0d9-b295-4f84-b44e-025750e1747b)

Restart Nginx:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

### Step 6: Verify Backend is Running

```bash
curl http://<backend-master-public-ip>/hello
```

Expected output:
```
Hello World!
```

---

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

### Step 3: Configure Backend URL

Edit the frontend configuration file to point to your backend server:

```bash
nano src/url.js
```

Update the `baseUrl` to your backend EC2 instance IP:

```javascript
const baseUrl = "http://<backend-master-public-ip>:3000";
```

**Frontend Configuration File (src/url.js):**

**Example Configuration:**
![Configuration Example](https://github.com/user-attachments/assets/9cc4f7de-1f0f-4372-80c3-4b439ca09fe9)

### Step 4: Build Frontend

```bash
npm install
npm run build
```

Copy the build to Nginx web root:

```bash
sudo rm -rf /var/www/html/*
sudo cp -r build/* /var/www/html/
sudo systemctl restart nginx
```

### Step 5: Verify the Frontend

Open your browser and go to:

```
http://<frontend-master-public-ip>
```

You should see your Travel Memory App loaded.

### Step 6: Configure Nginx for Frontend

Update your Nginx configuration to serve the React app:

```bash
sudo nano /etc/nginx/sites-available/default
```

Use this configuration:

```sudo tee /etc/nginx/sites-available/default > /dev/null <<'EOF'
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
EOF

sudo nginx -t
sudo systemctl restart nginx
```


### Step 7: Create AMI (Amazon Machine Images)

For load balancing and redundancy, create AMI snapshots of both instances:

**AWS AMI Creation Process:**
![Create AMI](https://github.com/user-attachments/assets/37fcf80c-2c5f-4e8e-b3b8-a1b31632e189)

Steps to create AMI:
1. Right-click on the EC2 instance
2. Select **Image and templates** → **Create image**
3. Give it a meaningful name (e.g., `travelmemory-backend-v1`, `travelmemory-frontend-v1`)
4. Click **Create image**

### Step 8: Launch Additional Instances from AMI

Once AMIs are created, you can launch additional instances:

**Multiple Instances from AMI:**
![Multiple Instances](https://github.com/user-attachments/assets/0691ab82-3eee-43dd-b104-0387428ee7bf)

This creates redundancy and enables load balancing across multiple backend and frontend servers.


### Step 9: Backend Target Group + Load Balancer


<img width="1470" height="956" alt="Screenshot 2026-10-12 at 3 02 35 PM" src="https://github.com/user-attachments/assets/ee21e030-78ad-4cf6-93d1-6ce8e4d6015a" />

<img width="1470" height="956" alt="Screenshot 2026-10-12 at 8 31 54 AM" src="https://github.com/user-attachments/assets/f021a4d1-0d6e-4496-8e1b-639c7ebe7e11" />



### Step 10: Frontend Target Group + Load Balancer

<img width="1470" height="956" alt="Screenshot 2026-10-12 at 8 34 20 AM" src="https://github.com/user-attachments/assets/1f5c32bd-5f66-4a9e-8f46-68d65c7b6486" />

<img width="1470" height="820" alt="Screenshot 2026-10-12 at 1 49 34 PM" src="https://github.com/user-attachments/assets/3f0ad785-2aa6-4036-be86-4f4766d1798b" />


### Step 11:Point Frontend at the Backend Load Balancer

<img width="1470" height="956" alt="Screenshot 2026-10-12 at 1 59 55 PM" src="https://github.com/user-attachments/assets/9a424daf-2c9a-42ac-a09f-5e2dc3b40597" />

### Step 11: DNS Setup

<img width="1470" height="820" alt="Screenshot 2026-10-12 at 1 40 10 PM" src="https://github.com/user-attachments/assets/52170ed7-ec34-4fca-a34a-0d5d4ad40ab1" />

### Final Verification 
-- adding record and observing the same at database end.
<img width="1470" height="956" alt="Screenshot 2026-10-12 at 1 57 28 PM" src="https://github.com/user-attachments/assets/cdcaed41-c619-4a02-8795-b6f265b491d5" />

-- Application is now accessible from frontend load balancer and my dns website (i.e : aws-ninjas.store) 
<img width="1470" height="956" alt="Screenshot 2026-10-12 at 1 57 50 PM" src="https://github.com/user-attachments/assets/222a908f-edc7-47f7-8c91-7c6a942b2a1b" />

<img width="1466" height="520" alt="Screenshot 2026-10-12 at 2 01 04 PM" src="https://github.com/user-attachments/assets/4e85e792-1f8e-4595-a713-83115514486f" />


---

## ⚙️ Configuration

### Environment Variables

**Backend `.env` file:**
```env
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/travelmemory?retryWrites=true&w=majority
PORT=3000
```


### MongoDB Compass (Optional)

To view and manage your database locally:

1. Download [MongoDB Compass](https://www.mongodb.com/products/tools/compass)
2. Paste your MongoDB connection string
3. Click **Connect**
4. You can now browse your database




## 🔧 Troubleshooting

### MongoDB Connection Error
- ✅ Check your MongoDB URI in `.env`
- ✅ Verify username and password
- ✅ Ensure IP address `0.0.0.0/0` is added in MongoDB Atlas Network Access

### Backend Not Starting
- ✅ Run `npm install` again
- ✅ Check `.env` file exists and has correct values
- ✅ Check port 3000 is not already in use: `sudo lsof -i :3000`
- ✅ Check systemd service status: `sudo systemctl status travelmemory-backend`

### Frontend Not Loading
- ✅ Check Nginx is running: `sudo systemctl status nginx`
- ✅ Verify backend URL in `src/url.js`
- ✅ Check browser console for errors (F12)
- ✅ Check that frontend build exists in `/var/www/html`

### EC2 Connection Issues
- ✅ Verify security group allows inbound traffic on ports 80, 443, 3000
- ✅ Check EC2 instance is running
- ✅ Verify SSH key permissions: `chmod 400 your-key.pem`
- ✅ Check MongoDB Atlas network allows EC2 IP

### Images Not Loading on Frontend
- ✅ Verify backend API is running and accessible
- ✅ Check CORS settings on backend
- ✅ Verify database has travel data stored

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
- The frontend should be served from `/var/www/html` by Nginx

---

## 📄 License

Add your license here

## 👨‍💻 Author

[Your Name / Organization]

---

**Last Updated:** October 12, 2026
