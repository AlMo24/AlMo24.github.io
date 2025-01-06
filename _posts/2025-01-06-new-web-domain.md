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

In this article, we'll explore how to integrate a GitHub-hosted project with Cloudflare Zero Trust for security and deploy subdomains effectively.

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
   - Sign up and/or log in to your Cloudflare.
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

