#server 

#### 🔹 Cтартовый сетап Linux-сервера

```bash
# Ubuntu/Debian - fast server bootstrap
sudo apt update && sudo apt -y upgrade

# Base tools
sudo apt -y install curl wget git unzip zip jq ca-certificates gnupg lsb-release \
  htop iotop ncdu lsof net-tools dnsutils ufw fail2ban tmux vim

# Firewall
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw --force enable

# SSH hardening (recommended after adding SSH keys)
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart ssh

# Fail2ban
sudo systemctl enable --now fail2ban

# Time sync
sudo timedatectl set-ntp true
```