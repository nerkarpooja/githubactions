# GitHub Actions → AWS EC2 Deploy

Auto-deploy a static HTML/CSS site to an AWS EC2 Linux instance on every push to `main`.

## Folder Structure

```
project/
├── index.html
├── style.css
├── README.md
└── .github/
    └── workflows/
        └── deploy.yml
```

## Setup Steps

### 1. Add GitHub Secrets
Go to: Repo → Settings → Secrets and variables → Actions

| Secret Name   | Value                              |
|---------------|------------------------------------|
| EC2_HOST      | Your EC2 Public IP (e.g. 13.x.x.x)|
| EC2_USER      | ubuntu (or ec2-user)               |
| EC2_SSH_KEY   | Contents of your .pem key file     |

### 2. Prepare EC2 (run once via SSH)
```bash
sudo apt update && sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx

# Allow passwordless nginx restart for GitHub Actions
echo "ubuntu ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx, /usr/bin/systemctl status nginx, /bin/chown, /bin/chmod" \
  | sudo tee /etc/sudoers.d/github-actions
```

### 3. Open Port 80
AWS Console → EC2 → Security Groups → Inbound Rules
→ Add rule: HTTP | Port 80 | Source: 0.0.0.0/0

### 4. Push to main
```bash
git add .
git commit -m "deploy"
git push origin main
```

Your site will be live at: http://<your-ec2-public-ip>
