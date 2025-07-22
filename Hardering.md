# Hardening: Seguridad aplicada a la infraestructura

## **1. SSH Seguro**

- **Cambiar puerto SSH**  
  Edita `/etc/ssh/sshd_config` y cambia el puerto:
  ```
  Port 2222
  ```
- **Deshabilitar autenticación por contraseña**  
  Solo acceso por clave pública:
  ```
  PasswordAuthentication no
  ```
- **Reinicia el servicio SSH**
  ```bash
  sudo systemctl restart ssh
  ```
- **Agregar clave pública al usuario**  
  Copia tu clave a la VM:
  ```bash
  ssh-copy-id -p 2222 usuario@IP
  ```

---

## **2. Firewall UFW**

- **Activar UFW y permitir solo los puertos necesarios**
  ```bash
  sudo ufw default deny incoming
  sudo ufw default allow outgoing
  sudo ufw allow 2222      # SSH seguro
  sudo ufw allow 80        # Nginx HTTP
  sudo ufw allow 3306      # MariaDB
  sudo ufw enable
  sudo ufw status
  ```
- **(Opcional) Limitar acceso por IP**
  ```bash
  sudo ufw allow from 192.168.43.0/24 to any port 3306
  sudo ufw allow from 192.168.43.0/24 to any port 80
  ```

---

## **3. Servicios Web y Base de Datos**

- **MariaDB: Permitir solo conexiones necesarias**
  - Edita `/etc/mysql/mariadb.conf.d/50-server.cnf`
    ```
    bind-address = 0.0.0.0
    ```
  - Limita usuarios por IP y privilegios:
    ```sql
    GRANT ALL PRIVILEGES ON compras.* TO 'usuario'@'192.168.43.%' IDENTIFIED BY 'contraseña';
    FLUSH PRIVILEGES;
    ```
- **Nginx:** Solo escucha en el puerto 80 y rechaza otros.

---

## **4. Usuarios y Contraseñas**

- **Evita usuarios por defecto (root) en MariaDB.**
- **Usa contraseñas robustas** (`pwgen`, `openssl rand -base64 16`, etc).
- **Evita acceso remoto con usuario root.**
- **Crea usuarios específicos para cada app.**

---

## **5. Actualizaciones y Paquetes**

- **Mantén el sistema actualizado**
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```
- **Elimina paquetes innecesarios**
  ```bash
  sudo apt autoremove -y
  ```

---

## **6. Pruebas de Hardening**

- **Verifica puertos abiertos**
  ```bash
  sudo ss -tuln
  sudo ufw status
  ```
- **Verifica autenticación SSH**
  - Intenta conectarte usando contraseña (debe fallar).
  - Solo acceso por clave.
- **Verifica acceso a MariaDB**
  - Solo desde las IPs permitidas por firewall y usuario.
- **Verifica acceso web**
  - Solo HTTP, sin otros servicios expuestos.

---
