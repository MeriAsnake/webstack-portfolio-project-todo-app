🚀 Deployment Guide – Todo App

This document explains how to deploy the Todo App to a Linux server using Nginx and GitHub Actions (CI/CD).
1. 🔧 Server Setup

Install required packages:


# Update system
sudo apt update
# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
# Install PM2 for process management
sudo npm install -g pm2
# Install Git (if not already installed)
sudo apt install git -y
2. ⚙️ CI/CD Pipeline

GitHub Actions deploys the app automatically when you push to the main branch.

Workflow Actions:
Connect to the server via SSH
Pull latest code
Install dependencies
Build frontend
Restart the app using PM2

3. 🌐 Nginx + Domain Setup
 
  Step-by-Step:
  1.Install Nginx
  sudo systemctl start nginx
  sudo systemctl enable nginx

  2. Create Nginx Site Config
  File: /etc/nginx/sites-available/todo-app
  server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
   }
   3. Enable the Site
   sudo ln -s /etc/nginx/sites-available/todo-app /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl reload nginx

4.Common Issues & Troubleshooting
| Issue                                | Solution                                                                     |
| ------------------------------------ | ---------------------------------------------------------------------------- |
| **Push doesn't update app**          | Make sure `npm run build` is included in the GitHub Actions script           |
| **Build not updating**               | PM2 may still be serving old `build/`. Run `npm run build` and restart `pm2` |
| **Permission denied (SSH)**          | Make sure your private key is correctly added to GitHub Secrets              |
| **Nginx shows default page**         | Make sure the correct config is symlinked and Nginx is reloaded              |
| **Changes not reflected in browser** | Try hard refresh (Ctrl + Shift + R) or clear cache                           |


