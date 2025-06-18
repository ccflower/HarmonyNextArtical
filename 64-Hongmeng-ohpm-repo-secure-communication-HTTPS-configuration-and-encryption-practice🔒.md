
# Hongmeng ohpm-repo secure communication: HTTPS configuration and encryption practice🔒

In Hongmeng development, the security of ohpm-repo private repository is crucial.This article teaches you to build a safe and reliable component warehouse communication link through HTTPS encrypted transmission, reverse proxy and certificate management~


## 1. HTTPS basic configuration: from HTTP to encrypted transmission🚀
### 1. Enable HTTPS listening
**Modify config.yaml**
```yaml  
listen: https://<Server Public IP>:8088 # Replace with the actual IP
https_key: ./ssl/server.key # Private key path
https_cert: ./ssl/server.crt # Certificate path
```  

**Self-signed certificate generation (OpenSSL)**
```bash  
# Generate 4096-bit private key
openssl genrsa -out server.key 4096  

# Generate a self-signed certificate (valid for 10 years)
openssl req -new -x509 -days 3650 -key server.key -out server.crt  
# Tip: Country/region and other information need to be filled in correctly, and the domain name can be written in the server IP
```  

**Permission settings**
```bash  
chmod 600 server.key server.crt # Only the current user can read and write
mkdir -p ssl && mv *.key *.crt ssl/  
```  


## 2. Reverse proxy configuration: Nginx's security guard🛡️
### 1. Nginx proxy HTTPS request
**nginx.conf configuration example**
```nginx  
server {  
  listen 443 ssl;  
server_name repo.example.com; # Replace with the actual domain name

# SSL certificate path (consistent with ohpm-repo)
  ssl_certificate /path/to/ssl/server.crt;  
  ssl_certificate_key /path/to/ssl/server.key;  

#Safe Headers
  add_header Content-Security-Policy "default-src 'self'";  
  add_header X-Content-Type-Options nosniff;  

  location / {  
# Forward to the ohpm-repo instance (assuming local listening 8088)
    proxy_pass http://127.0.0.1:8088;  

# Pass the real IP of the client
    proxy_set_header X-Real-IP $remote_addr;  
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
    proxy_set_header Host $http_host;  
  }  
}  
```  

### 2. ohpm-repo adapter proxy
**Open reverse proxy mode**
```yaml  
use_reverse_proxy: true  
store:  
  config:  
server: https://repo.example.com # Fill in the Nginx proxy address
```  


## 3. Security Strengthening: Deep Protection from Certificate to Agreement🔐
### 1. Certificate Lifecycle Management
| Stage | Key points of operation |
|--------------|-------------------------------------------|  
| **Generate** | Use CA signature certificate instead of self-signing (Recommended Let's Encrypt) |
| **Storage** | Private key storage into Hardware Security Module (HSM) or Key Management Service (KMS) |
| **Update** | Set the time limit (such as 30 days in advance) to automatically rotate the certificate |
| **Abandoned** | Revoke expired certificates and clean up server residual files |

**Let's Encrypt Example (Certbot)**
```bash  
# Install Certbot
sudo apt-get install certbot python3-certbot-nginx  

# Automatically configure Nginx certificates
sudo certbot --nginx -d repo.example.com  
```  

### 2. TLS protocol and encryption suite optimization
**Disable weak protocol in Nginx**
```nginx  
ssl_protocols TLSv1.3; # Enable only TLSv1.3 (the safest version)
ssl_ciphers ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;  
ssl_prefer_server_ciphers on;  
```  

**Verification Tool**
- [SSL Labs Server Test](https://www.ssllabs.com/ssltest/): Detect TLS configuration security
- `openssl s_client -connect repo.example.com:443`: Test connection encryption suite


## 4. Multi-instance secure communication: encryption policy in cluster environment👥
### 1. Inter-instance communication encryption
**Scenario**: Prevent man-in-the-middle attacks when the master and slave warehouse synchronize data
**plan**:
1. Enable bidirectional TLS (mTLS) in ohpm-repo configuration
   ```yaml  
https_client_ca: ./ssl/ca.crt # Client CA certificate path
require_client_cert: true # Force verification of client certificates
   ```  
2. Generate a unique client certificate for each instance, signed by CA

### 2. Load balancing layer security configuration
**Example: HAProxy Load Balancer**
```haproxy  
frontend https-in  
  bind *:443 ssl crt /path/to/ssl/combined.pem  
  mode http  
  default_backend ohpm-repo-instances  

backend ohpm-repo-instances  
  mode http  
  server instance1 192.168.1.1:8088 ssl verify required ca-file /path/to/ca.crt  
  server instance2 192.168.1.2:8088 ssl verify required ca-file /path/to/ca.crt  
```  


## 5. Practical testing: security vulnerability investigation and optimization📊
### 1. Common vulnerability detection
| Vulnerability Type | Detection Commands/Tools | Fix Solutions |
|----------------|---------------------------------|-----------------------------------|  
| Certificate expired | `openssl x509 -in server.crt -text` | Update certificates in time |
| Weak encryption kit | `nmap --script ssl-enum-ciphers` | Disable low-strength encryption kit |
| Cross-site scripting attacks | OWASP ZAP scan | Enable Content-Security-Policy Header |
| Error in proxy configuration | Check if the `X-Forwarded-For` header is correct | Ensure the reverse proxy passes the complete client information |

### 2. Performance and safety balance
**Problem**: HTTPS encryption increases CPU consumption
**Optimization Solution**:
- Enable Nginx cache: `proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=ohpm_cache:10m max_size=10g;`
- Use session multiplexing: `ssl_session_cache shared:SSL:10m; ssl_session_timeout 10m;`
- Hardware acceleration: Accelerate encryption using the AES-NI instruction set of server CPU


## Summary: "Five Elements" for HTTPS Configuration
1. **Certificate Management**: Priority is given to the use of CA certificates and strict control of private key permissions
2. **Protocol upgrade**: Only enable TLSv1.3, disable all old versions of the protocols
3. **Proxy Protection**: Hide the real server IP through Nginx to enhance the security of the request header
4. **Cluster encryption**: Inter-instance communication adopts mTLS bidirectional authentication
5. **Continuous monitoring**: Regularly scan for vulnerabilities and automated certificate rotation
