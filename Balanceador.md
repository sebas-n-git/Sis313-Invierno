# Balanceador de Carga Nginx: Comandos y Configuración

## **Instalación de Nginx**

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

---

## **Configuración de Balanceo Round Robin**

Edita el archivo de configuración principal o crea un nuevo sitio:

```bash
sudo nano /etc/nginx/sites-available/compras-balanceo
```

Ejemplo de configuración (ajusta las IPs según tus apps):

```nginx
upstream compras_app {
    server 192.168.43.101:3001;
    server 192.168.43.102:3002;
    server 192.168.43.103:3003;
}

server {
    listen 80;
    server_name 192.168.43.100; # IP del balanceador

    location / {
        proxy_pass http://compras_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Habilita el sitio:

```bash
sudo ln -s /etc/nginx/sites-available/compras-balanceo /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## **Pruebas de Funcionamiento**

- Desde otro equipo/VM, accede a:
  ```
  http://192.168.43.100/
  ```
- Refresca varias veces y verifica que rota entre apps.
- Prueba el CRUD completo desde el navegador, asegurando que opera correctamente.

---

## **Comandos de Gestión**

```bash
# Ver estado del servicio
sudo systemctl status nginx

# Reiniciar nginx tras cambios
sudo systemctl reload nginx

# Ver logs de acceso y error
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

---

## **Hardening y Firewall**

```bash
# Permitir solo el puerto HTTP (80)
sudo ufw allow 80

# (Opcional) Limitar acceso solo a las IPs de la red interna
# sudo ufw allow from 192.168.43.0/24 to any port 80

# Ver estado de UFW
sudo ufw status
```

---
