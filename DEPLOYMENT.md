# Deployment Guide - Rohan Reddy Portfolio

This guide covers deploying the portfolio website to **rohan-reddy.com** using CloudFlare Tunnel.

## 🚀 CloudFlare Tunnel Deployment

### Prerequisites
- CloudFlare account with domain management access
- Domain: `rohan-reddy.com` added to CloudFlare
- Server with Node.js 18+ installed

### Step 1: Install CloudFlare Tunnel

```bash
# For Ubuntu/Debian
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared.deb

# For CentOS/RHEL
sudo rpm -i https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-x86_64.rpm

# For macOS
brew install cloudflared

# For Windows
# Download from: https://github.com/cloudflare/cloudflared/releases/latest
```

### Step 2: Authenticate with CloudFlare

```bash
cloudflared tunnel login
```

This opens a browser window where you'll authorize CloudFlare to access your account.

### Step 3: Create the Tunnel

```bash
# Create a new tunnel
cloudflared tunnel create rohan-portfolio

# Note: Save the tunnel ID that's displayed
```

### Step 4: Configure the Tunnel

Create the configuration file:

```bash
# Create config directory if it doesn't exist
mkdir -p ~/.cloudflared

# Create config file
nano ~/.cloudflared/config.yml
```

Add the following configuration:

```yaml
tunnel: YOUR_TUNNEL_ID_HERE
credentials-file: /home/USERNAME/.cloudflared/YOUR_TUNNEL_ID.json

ingress:
  - hostname: rohan-reddy.com
    service: http://localhost:5000
  - hostname: www.rohan-reddy.com
    service: http://localhost:5000
  - service: http_status:404

# Optional: Logging and other settings
logLevel: info
```

### Step 5: Configure DNS

Route your domain through the tunnel:

```bash
# Route main domain
cloudflared tunnel route dns rohan-portfolio rohan-reddy.com

# Route www subdomain
cloudflared tunnel route dns rohan-portfolio www.rohan-reddy.com
```

### Step 6: Prepare the Application

1. **Set up the production environment**:

```bash
# Clone or update your repository
git clone https://github.com/Rohan1011/Portfolio-Website-.git
cd Portfolio-Website-

# Install dependencies
npm install

# Build the application (if needed)
npm run build
```

2. **Configure environment variables**:

```bash
# Create production environment file
nano .env
```

Add your production environment variables:

```env
# Required API Keys
NEWS_API_KEY=your_actual_newsapi_key
GITHUB_TOKEN=your_actual_github_token
SESSION_SECRET=your_secure_session_secret

# Email Configuration (optional)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password
SMTP_FROM=noreply@rohan-reddy.com
ADMIN_EMAIL=admin@rohan-reddy.com

# Production Settings
NODE_ENV=production
SITE_URL=https://rohan-reddy.com
```

### Step 7: Set up Process Manager (PM2)

Install and configure PM2 for production:

```bash
# Install PM2 globally
npm install -g pm2

# Create PM2 ecosystem file
nano ecosystem.config.js
```

Add PM2 configuration:

```javascript
module.exports = {
  apps: [{
    name: 'rohan-portfolio',
    script: 'npm',
    args: 'run dev',
    cwd: '/path/to/Portfolio-Website-',
    env: {
      NODE_ENV: 'production',
      PORT: 5000
    },
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    log_file: './logs/app.log',
    error_file: './logs/error.log',
    out_file: './logs/out.log'
  }]
};
```

Start the application with PM2:

```bash
# Start the application
pm2 start ecosystem.config.js

# Save PM2 configuration
pm2 save

# Setup PM2 startup script
pm2 startup
```

### Step 8: Start the CloudFlare Tunnel

#### Option A: Run Manually (for testing)

```bash
cloudflared tunnel --config ~/.cloudflared/config.yml run rohan-portfolio
```

#### Option B: Install as System Service (recommended)

```bash
# Install tunnel as a system service
sudo cloudflared --config ~/.cloudflared/config.yml service install

# Start the service
sudo systemctl start cloudflared

# Enable auto-start on boot
sudo systemctl enable cloudflared

# Check service status
sudo systemctl status cloudflared
```

### Step 9: SSL/TLS Configuration

CloudFlare automatically provides SSL certificates. Configure the SSL mode:

1. Go to CloudFlare Dashboard → SSL/TLS
2. Set encryption mode to "Full" or "Full (strict)"
3. Enable "Always Use HTTPS"
4. Configure HSTS (recommended)

### Step 10: Performance Optimization

1. **Enable CloudFlare Caching**:
   - Go to CloudFlare Dashboard → Caching
   - Set caching level to "Standard"
   - Create page rules for static assets

2. **Configure Compression**:
   - Enable Brotli compression
   - Enable Gzip compression

3. **Enable Speed Optimizations**:
   - Auto Minify (CSS, JS, HTML)
   - Rocket Loader
   - Mirage (for images)

### Step 11: Monitoring and Maintenance

1. **Set up monitoring**:

```bash
# Monitor PM2 processes
pm2 monit

# View logs
pm2 logs rohan-portfolio

# Monitor tunnel status
sudo systemctl status cloudflared
```

2. **Create update script**:

```bash
nano update-site.sh
```

```bash
#!/bin/bash
cd /path/to/Portfolio-Website-
git pull origin main
npm install
pm2 restart rohan-portfolio
echo "Site updated successfully!"
```

```bash
chmod +x update-site.sh
```

## 🔧 Troubleshooting

### Common Issues

1. **Tunnel not connecting**:
   ```bash
   # Check tunnel status
   cloudflared tunnel list
   
   # Check service logs
   sudo journalctl -u cloudflared -f
   ```

2. **Application not starting**:
   ```bash
   # Check PM2 logs
   pm2 logs rohan-portfolio --lines 50
   
   # Restart application
   pm2 restart rohan-portfolio
   ```

3. **DNS propagation issues**:
   ```bash
   # Check DNS resolution
   dig rohan-reddy.com
   nslookup rohan-reddy.com
   ```

### Health Check Script

Create a health check script:

```bash
nano health-check.sh
```

```bash
#!/bin/bash

echo "🔍 Checking application health..."

# Check if application is responding
if curl -f -s http://localhost:5000 > /dev/null; then
    echo "✅ Application is running"
else
    echo "❌ Application is down"
    pm2 restart rohan-portfolio
fi

# Check tunnel status
if sudo systemctl is-active --quiet cloudflared; then
    echo "✅ CloudFlare tunnel is active"
else
    echo "❌ CloudFlare tunnel is down"
    sudo systemctl restart cloudflared
fi

echo "🏁 Health check complete"
```

Set up a cron job to run this every 5 minutes:

```bash
crontab -e
```

Add:
```
*/5 * * * * /path/to/health-check.sh >> /var/log/health-check.log 2>&1
```

## 🔒 Security Best Practices

1. **Firewall Configuration**:
   ```bash
   # Only allow necessary ports
   sudo ufw allow 22   # SSH
   sudo ufw allow 80   # HTTP (optional, CloudFlare handles)
   sudo ufw allow 443  # HTTPS (optional, CloudFlare handles)
   sudo ufw enable
   ```

2. **Environment Security**:
   - Use strong session secrets
   - Regularly rotate API keys
   - Keep environment files secure (600 permissions)

3. **CloudFlare Security**:
   - Enable DDoS protection
   - Configure WAF rules
   - Set up rate limiting

## 📊 Performance Monitoring

1. **CloudFlare Analytics**:
   - Monitor traffic and performance
   - Set up alerts for downtime
   - Track Core Web Vitals

2. **Server Monitoring**:
   ```bash
   # Install monitoring tools
   sudo apt install htop iotop nethogs
   
   # Monitor resource usage
   pm2 monit
   ```

## 🔄 Backup Strategy

1. **Database Backups** (if using PostgreSQL):
   ```bash
   # Create backup script
   pg_dump portfolio_db > backup_$(date +%Y%m%d_%H%M%S).sql
   ```

2. **Code Backups**:
   - Use Git for version control
   - Regular pushes to GitHub
   - Consider automated backups

---

## ✅ Final Verification

After deployment, verify:

- [ ] Site loads at https://rohan-reddy.com
- [ ] All pages are accessible
- [ ] GitHub integration works
- [ ] News feed loads
- [ ] Contact form submits successfully
- [ ] Admin panel is accessible
- [ ] Email notifications work (if configured)
- [ ] SSL certificate is valid
- [ ] Performance is satisfactory

## 🆘 Support

If you encounter issues:

1. Check CloudFlare Tunnel logs: `sudo journalctl -u cloudflared -f`
2. Check application logs: `pm2 logs rohan-portfolio`
3. Verify DNS settings in CloudFlare dashboard
4. Test local application first: `npm run dev`

## 🎉 Going Live

Once everything is working:

1. Update any hardcoded URLs in the application
2. Test all functionality
3. Set up monitoring alerts
4. Update DNS records if needed
5. Announce your new site! 🚀

Your portfolio is now live at https://rohan-reddy.com!