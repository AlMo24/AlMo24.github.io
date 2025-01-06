---
title: "Homelab - New Web Domain, Sounds Interesting?"
date: 2025-01-06 00:00:00 +0700
categories: [Homelab, server]
tags: [homelab, server, proxmox, domain, cloudflare, github]
---


If you find yourself eager to try and venture more at the risk of exposing your system to the world, this is for you.  


## What You Can Do with a New Web Domain

A new web domain opens up a world of possibilities for personal and professional projects. Here are some key things you can do with it:

1. **Create a Personal or Professional Website:**
   - Showcase your portfolio, resume, or personal projects.
   - Build a blog to share your thoughts and experiences.

2. **Set Up an Online Store:**
   - Use e-commerce platforms like Shopify, WooCommerce, or Magento to sell products or services.

3. **Host Web Applications:**
   - Deploy custom web applications using platforms like Heroku, Vercel, or your own servers.

4. **Email Hosting:**
   - Set up a professional email address (e.g., `yourname@yourdomain.com`).

5. **Create Subdomains:**
   - Organize your content by creating subdomains such as `blog.yourdomain.com` or `shop.yourdomain.com`.

6. **Integrate Security and Performance Enhancements:**
   - Use services like Cloudflare to secure and optimize your site.


Here we'll explore how to integrate a GitHub-hosted project with Cloudflare Zero Trust for security and deploy subdomains effectively.  
But which one is better from the security perspective, directly link your github to your domain, or clone it first to a Proxmox lxc?

From a security perspective, **cloning your GitHub repository to a Proxmox LXC (Linux Container)** and serving the content from there is generally better than directly linking GitHub to your domain. Here’s why:

---

### 1. **Key Security Considerations**

#### **Directly Linking GitHub to Your Domain**
   - **Pros:**
     - Easy to set up with GitHub Pages or a GitHub Action.
     - Automated deployment ensures changes are reflected immediately.
   - **Cons:**
     - If your GitHub account is compromised, attackers could modify your site directly.
     - Public repositories expose your source code to everyone.
     - Limited control over the hosting environment and its security configuration.
   
#### **Using Proxmox LXC**
   - **Pros:**
     - Enhanced security through isolation. Even if one container is compromised, it won't affect the host or other services.
     - Complete control over the server environment, allowing you to harden security (e.g., disabling unused services, enforcing strict file permissions).
     - Private repository cloning adds another layer of protection by keeping sensitive code away from public access.
   - **Cons:**
     - Slightly more complex setup and maintenance.
     - Requires monitoring and patching the server regularly.

---

### 2. **Security Recommendations for Each Approach**

#### **If You Choose Directly Linking GitHub to Your Domain:**
   - Use **private repositories** for sensitive projects.
   - Enable **2FA (Two-Factor Authentication)** on your GitHub account.
   - Use **branch protection rules** to prevent unauthorized changes.
   - Deploy using **GitHub Actions with secrets management** to avoid exposing sensitive data.
   - Use **Cloudflare or another WAF (Web Application Firewall)** to protect against DDoS and other web attacks.

#### **If You Choose Proxmox LXC:**
   - Secure the Proxmox server:
   - Use **strong SSH keys** for remote access.
   - Enable a **firewall** and restrict access to only necessary services.
   - Keep the host and container OS updated.
   - Clone the GitHub repository into a private container.
   - Use **container snapshots** to quickly recover from any compromise.
   - Serve the content using a reverse proxy like **NGINX** or **Traefik** with **TLS encryption** (HTTPS).
   - Restrict container access using **Cloudflare Zero Trust** to enforce strict access controls.

---

### 3. **Recommendation: Proxmox LXC for Sensitive Projects**
If your project requires robust security, Proxmox LXC is the better choice because it provides isolation, private hosting, and complete control over the environment. For simple, public-facing projects without sensitive data, GitHub Pages or direct deployment is sufficient when properly secured.

---

## Integrating GitHub with Cloudflare Zero Trust

Cloudflare Zero Trust is a robust solution for securing your web applications by enforcing access controls. Here's how you can integrate it with a GitHub project.

### Step 1: Set Up a GitHub Repository

1. **Create a New Repository:**
   - Log in to GitHub and create a new repository for your project.
   - Add your code and ensure it is properly structured for deployment.

2. **Set Up GitHub Pages (Optional):**
   - If your project is static, you can deploy it directly to GitHub Pages.
     - Go to the repository settings.
     - Under "Pages," select the branch to publish.

### Step 2: Configure Cloudflare for Your Domain

1. **Add Your Domain to Cloudflare:**
   - Sign up or log in to Cloudflare.
   - Add your domain by following the on-screen instructions.
   - Update your domain registrar’s DNS settings to point to Cloudflare’s nameservers.

2. **Set Up SSL/TLS Encryption:**
   - Ensure that your site is served over HTTPS by enabling SSL in Cloudflare.
   - Choose the appropriate SSL setting (e.g., Full or Full (Strict)).

### Step 3: Enable Cloudflare Zero Trust

1. **Set Up a Cloudflare Zero Trust Account:**
   - Navigate to the Zero Trust dashboard within Cloudflare.
   - Follow the setup wizard to configure Zero Trust.

2. **Create Access Policies:**
   - Define rules to control who can access your site.
   - For example, restrict access to specific GitHub users or IP ranges.

3. **Generate Tunnel Credentials:**
   - Use Cloudflare Tunnel to securely connect your local or GitHub-hosted application to Cloudflare.
   - Install the `cloudflared` tool and authenticate your domain.

### Step 4: Connect GitHub with Cloudflare

1. **Deploy Your Site Using GitHub Actions:**
   - Create a `.github/workflows/deploy.yml` file to automate deployments.
   - Example configuration:

```yaml
name: Deploy to Cloudflare

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Deploy with Cloudflare Tunnel
      run: |
        cloudflared tunnel run <your-tunnel-name>
```

2. **Monitor and Verify Deployment:**
   - Check the Cloudflare dashboard for successful deployments.

---

## Setting Up a Proxmox LXC for Hosting

Proxmox is a powerful platform for managing virtual machines and containers. Here’s a step-by-step guide to setting up a Proxmox LXC for hosting your project.

### Step 1: Install Proxmox (If you haven't already)

1. **Download the Proxmox ISO:**
   - Visit the [Proxmox website](https://www.proxmox.com/) and download the latest ISO image.

2. **Install Proxmox on Your Server:**
   - Create a bootable USB drive with the ISO.
   - Boot your server from the USB and follow the installation wizard.

3. **Access the Proxmox Web Interface:**
   - Open a browser and navigate to `https://<your-server-ip>:8006`.
   - Log in with the root credentials you set during installation.

### Step 2: Create an LXC Container

1. **Download a Template:**
   - In the Proxmox web interface, go to `Local > Content > Templates`.
   - Download a Linux distribution template (e.g., Ubuntu or Debian).

2. **Create the Container:**
   - Go to `Create CT` and fill in the details:
     - Container ID and hostname.
     - Root password.
     - Select the downloaded template.
     - Allocate resources (CPU, RAM, disk space).

3. **Start the Container:**
   - Once created, start the container and access it via the console.

### Step 3: Clone Your GitHub Repository

1. **Install Git in the Container:**
   - Run: `apt update && apt install git`

2. **Clone the Repository:**
   - Run: `git clone https://github.com/yourusername/yourrepository.git`

### Step 4: Set Up a Web Server

1. **Install NGINX or Apache:**
   - Run: `apt install nginx` (or `apt install apache2` for Apache).

2. **Configure the Web Server:**
   - Place your project files in `/var/www/html` or configure a virtual host.
   - Ensure the web server is running: `systemctl start nginx`.

### Step 5: Secure Your LXC

1. **Enable HTTPS:**
   - Use `certbot` to obtain a free SSL certificate:
     - Run: `apt install certbot python3-certbot-nginx`
     - Follow the prompts to secure your site.

2. **Restrict Access:**
   - Use UFW (Uncomplicated Firewall) to allow only necessary ports:
     - Run: `ufw allow 80`
     - Run: `ufw allow 443`
     - Run: `ufw enable`

3. **Backup Regularly:**
   - Use Proxmox’s built-in backup tools to create snapshots of your container.

---

## Creating and Managing Subdomains

Subdomains allow you to organize your web domain effectively. Here’s how to create and manage them.

### Step 1: Create a Subdomain in Cloudflare

1. **Log In to Cloudflare:**
   - Select your domain from the dashboard.

2. **Add a DNS Record:**
   - Go to the DNS settings.
   - Add a new A or CNAME record for the subdomain.
     - Example:
       - Name: `blog`
       - Type: CNAME
       - Target: `yourdomain.com`

3. **Verify Subdomain Setup:**
   - Use a tool like `ping` or a browser to test the subdomain.

### Step 2: Deploy Different Content to Subdomains

1. **Assign Subdomains to Specific Projects:**
   - Use subdomains like `api.yourdomain.com` for APIs or `admin.yourdomain.com` for admin portals.

2. **Set Up Hosting:**
   - Host subdomain content on platforms like Vercel, Netlify, or custom servers.

3. **Configure Cloudflare Rules:**
   - Use Page Rules to redirect or manage traffic for subdomains.

### Step 3: Secure Subdomains with Cloudflare

1. **Enable SSL for Subdomains:**
   - Ensure each subdomain is secured with HTTPS.

2. **Apply Access Control Policies:**
   - Use Cloudflare Zero Trust to manage access for sensitive subdomains.

---

## Conclusion

A new web domain gives you the opportunity to create websites, deploy web applications, and organize your content effectively. By integrating GitHub with Cloudflare Zero Trust and managing subdomains, you can ensure security, performance, and scalability. Creating subdomains helps you organize your content and expand your online presence.  
Follow the steps outlined here to make the most out of your domain and unlock its full potential.

