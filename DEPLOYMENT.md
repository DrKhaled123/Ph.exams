# SPLE Pro - Caddy Deployment Guide

## Domain Configuration
- **Production Domain**: `prodemy.promedic1.com`
- **WWW Redirect**: `www.prodemy.promedic1.com` → `prodemy.promedic1.com`
- **SSL Email**: `doctor@promedic1.com`

## File Locations

| File | Path |
|------|------|
| Caddyfile | `/etc/caddy/Caddyfile` |
| Application | `/root/sple-exam-app/` |
| Access Logs | `/var/log/caddy/access.log` |
| Error Logs | `/var/log/caddy/error.log` |

## Quick Start

### Local Development (Port 8080)
```bash
caddy run --config /etc/caddy/Caddyfile
```

### Production (with HTTPS)
```bash
# Ensure DNS points to server IP first
caddy run --config /etc/caddy/Caddyfile
```

Or use systemd:
```bash
systemctl enable caddy
systemctl start caddy
```

## Configuration Features

### ✅ Security
- **X-Frame-Options**: SAMEORIGIN (clickjacking protection)
- **X-Content-Type-Options**: nosniff (MIME sniffing protection)
- **X-XSS-Protection**: 1; mode=block
- **Content-Security-Policy**: Comprehensive CSP header
- **Strict-Transport-Security**: HSTS with preload
- **Permissions-Policy**: Restricted device access
- **Request Size Limit**: 10MB
- **Hidden Files**: .git, .env, .htpasswd protected

### ✅ Performance
- **Gzip Compression**: Enabled for all text content
- **Static Asset Caching**: 1 year cache for CSS/JS/Images
- **SPA Routing**: try_files for client-side routing
- **HTTP/2**: Automatic HTTP/2 support

### ✅ SSL/TLS (Let's Encrypt)
- **Auto HTTPS**: Automatic certificate generation
- **Email**: doctor@promedic1.com for renewal notifications
- **WWW Redirect**: www → non-www redirect
- **HSTS**: Strict transport security enabled

### ✅ Logging
- **JSON Format**: Structured logging for easier parsing
- **Rotation**: 100MB size limit, 10 files kept
- **Retention**: 30 days
- **Separate Files**: access.log and error.log

### ✅ Health Checks
- **Endpoint**: `/health` returns "OK" 200
- **SPA 404 Handling**: Returns index.html for all 404s

## Testing

### Validate Configuration
```bash
caddy validate --config /etc/caddy/Caddyfile
```

### Format Configuration
```bash
caddy fmt --overwrite /etc/caddy/Caddyfile
```

### Test Local Server
```bash
# Start server
caddy run --config /etc/caddy/Caddyfile

# Test endpoints
curl http://localhost:8080/health
curl -I http://localhost:8080/
```

## DNS Configuration

Point your DNS A records to this server's IP:

```
prodemy.promedic1.com    A     <YOUR_SERVER_IP>
www.prodemy.promedic1.com A    <YOUR_SERVER_IP>
```

## Firewall Requirements

Ensure these ports are open:
- **80/tcp**: HTTP (for Let's Encrypt validation)
- **443/tcp**: HTTPS
- **8080/tcp**: Local development (optional)

## Troubleshooting

### Check Logs
```bash
tail -f /var/log/caddy/access.log
tail -f /var/log/caddy/error.log
```

### Test Configuration
```bash
caddy adapt --config /etc/caddy/Caddyfile
```

### Restart Caddy
```bash
pkill caddy
caddy run --config /etc/caddy/Caddyfile
```

## Production Checklist

- [ ] DNS A records configured
- [ ] Firewall ports 80/443 open
- [ ] SSL email valid (doctor@promedic1.com)
- [ ] Domain accessible from internet
- [ ] Logs directory writable
- [ ] Application files in /root/sple-exam-app/

## Support

For issues with:
- **Caddy**: https://caddyserver.com/docs/
- **Let's Encrypt**: https://letsencrypt.org/docs/
- **SPLE Pro App**: Check browser console for errors
