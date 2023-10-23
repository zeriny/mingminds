# Certbot申请Let's Encrypt证书

#### 通过DNS验证challenge

sudo ./certbot-auto certonly  -d *.sccattack.top -d sccattack.top --manual --preferred-challenges dns-01  --server https://acme-v02.api.letsencrypt.org/directory