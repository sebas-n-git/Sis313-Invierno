# Servidor de Base de Datos MariaDB: Instalación y Configuración

## **1. Instalación de MariaDB**

```bash
sudo apt update
sudo apt install mariadb-server mdadm -y
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

---

## **2. Configuración de RAID 10**

```bash
# Verifica discos disponibles
lsblk

# Limpia superblocks previos
sudo mdadm --zero-superblock /dev/sdb /dev/sdc /dev/sdd /dev/sde

# Crea el RAID 10
sudo mdadm --create --verbose /dev/md0 --level=10 --raid-devices=4 /dev/sdb /dev/sdc /dev/sdd /dev/sde

# Formatea y monta
sudo mkfs.ext4 /dev/md0
sudo mkdir /mnt/raid10
sudo mount /dev/md0 /mnt/raid10

# Agrega a /etc/fstab
echo '/dev/md0 /mnt/raid10 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab

# Guarda la configuración de mdadm
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u
```

---

## **3. Configuración de MariaDB para Acceso Remoto**

```bash
# Edita el archivo de configuración
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

# Cambia el bind-address a 0.0.0.0
bind-address = 0.0.0.0

# Reinicia MariaDB
sudo systemctl restart mariadb
```

---

## **4. Creación de Base de Datos y Tabla**

En el cliente de MariaDB:

```sql
CREATE DATABASE compras;
USE compras;
CREATE TABLE items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255) NOT NULL
);
```

---

## **5. Gestión de Usuarios y Permisos**

```sql
GRANT ALL PRIVILEGES ON compras.* TO 'usuario'@'%' IDENTIFIED BY 'contraseña';
FLUSH PRIVILEGES;
```

---

## **6. Firewall y Hardening**

```bash
# Permite solo el puerto 3306 para MariaDB
sudo ufw allow 3306

# (Opcional) Limita el acceso solo a las IPs de las apps
# sudo ufw allow from 192.168.43.0/24 to any port 3306

# Verifica el estado del firewall
sudo ufw status
```

---

## **7. Simulación y Recuperación de Falla RAID**

```bash
# Marca un disco como fallido y remuévelo
sudo mdadm /dev/md0 --fail /dev/sdb
sudo mdadm /dev/md0 --remove /dev/sdb

# (Si se reemplaza por un disco nuevo, por ejemplo /dev/sdf)
sudo mdadm --zero-superblock /dev/sdf
sudo mdadm /dev/md0 --add /dev/sdf

# Verifica el estado de RAID
cat /proc/mdstat
sudo mdadm --detail /dev/md0
```

---
