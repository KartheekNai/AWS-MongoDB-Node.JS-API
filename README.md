# Node.js API Setup on AWS with MongoDB

This guide provides **Bash commands** to set up a **Node.js API** on an **AWS server**, configure **MongoDB**, manage logs, and set up a **reverse proxy** using Nginx.

## 1. Install Required Packages
```bash
# Update system packages
sudo apt update && sudo apt upgrade -y  

# Install Node.js and npm (LTS version)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -  
sudo apt install -y nodejs  

# Verify installation
node -v  
npm -v  

# Install PM2 for process management  
sudo npm install -g pm2  

# Install MongoDB client (if needed)
sudo apt install -y mongodb-clients  
```

## 2. Clone & Set Up Node.js API
```bash
# Navigate to your working directory
cd /var/www  

# Clone your Node.js API project from GitHub (replace with your repo URL)
git clone https://github.com/your-repo/node-api.git  

# Move into the project directory
cd node-api  

# Install dependencies
npm install  
```

## 3. Set Permissions
```bash
# Give execute permissions to scripts
chmod +x start.sh  

# Change ownership to allow Node.js to run
sudo chown -R ubuntu:ubuntu /var/www/node-api  
```

## 4. Set Up Environment Variables
```bash
# Open the .env file to configure database and API keys
nano .env  

# Example .env content
MONGO_URI="mongodb+srv://user:password@cluster.mongodb.net/dbname"
PORT=3000
NODE_ENV=production  

# Save and exit (CTRL+X, Y, ENTER)
```

## 5. Start and Manage Node.js API with PM2
```bash
# Start the Node.js application
pm2 start server.js --name node-api  

# Ensure it runs on system reboot
pm2 startup  
sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u ubuntu --hp /home/ubuntu  
pm2 save  

# Restart the application if needed
pm2 restart node-api  

# Stop the application
pm2 stop node-api  

# Delete the application
pm2 delete node-api  
```

## 6. Check Logs for Errors
```bash
# View real-time logs
pm2 logs node-api  

# View last 100 lines of logs
pm2 logs node-api --lines 100  

# Tail logs continuously
tail -f ~/.pm2/logs/node-api-out.log  
tail -f ~/.pm2/logs/node-api-error.log  
```

## 7. Check Server & API Status
```bash
# Check if Node.js is running
ps aux | grep node  

# Check if API is listening on port 3000
netstat -tulnp | grep :3000  

# Test if API is reachable
curl -i http://localhost:3000/api/status  
```

## 8. Manage MongoDB Connection & Status
```bash
# Check if MongoDB service is running
systemctl status mongod  

# Restart MongoDB service
sudo systemctl restart mongod  

# Test MongoDB connection
mongo --host your-mongodb-cluster.mongodb.net --username yourUser --password yourPassword  

# Show available databases
show dbs  
```

## 9. Firewall & Security Setup
```bash
# Allow traffic on port 3000 (Node.js API)
sudo ufw allow 3000/tcp  

# Enable firewall (if not enabled)
sudo ufw enable  

# Check firewall rules
sudo ufw status  
```

## 10. Connect Power BI to Node.js API
```bash
# Ensure API is running
pm2 start node-api  

# Check the API URL
curl -i http://your-server-ip:3000/api/data  

# Install CORS for Power BI connectivity
npm install cors  

# Edit server.js to enable CORS
nano server.js  

# Add the following lines at the top
const cors = require('cors');
app.use(cors());  

# Restart the API
pm2 restart node-api  

# Test Power BI connection
# Open Power BI Desktop > Get Data > Web > Enter API URL
http://your-server-ip:3000/api/data  

# Publish Power BI report and refresh data
``` 


## Final Checklist
✅ **Node.js Installed** (`node -v`)

✅ **Project Installed & Running** (`pm2 list`)

✅ **MongoDB Connected** (`mongo --eval "show dbs"`)

✅ **API Accessible** (`curl http://localhost:3000/api/status`)

✅ **Firewall Rules Set** (`sudo ufw status`)

---
### 🎯 Now you have a fully configured **Node.js API on AWS** with **MongoDB** and **Nginx reverse proxy**! 🚀
