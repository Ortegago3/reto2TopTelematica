# Topicos Especiales en Telematica
# Estudiantes:
- Holmer Ortega Gomez
- 
# Reto: Despliegue de WordPress Distribuido

# 1. Descripción del proyecto
Este proyecto despliega una aplicación WordPress distribuida utilizando contenedores Docker. Se implementa un balanceador de cargas (Nginx) que recibe el tráfico web, con múltiples instancias de WordPress. Adicionalmente, la arquitectura incluye una base de datos MySQL y un almacenamiento compartido mediante NFS.

# 2. Componentes del despliegue
Instancias de WordPress (2 servidores de aplicación)
Servidor MySQL (base de datos para WordPress)
Servidor NFS (almacenamiento compartido para archivos de WordPress)
Balanceador de carga (Nginx para balanceo entre instancias)

# 3. Arquitectura
La arquitectura incluye un balanceador de cargas que distribuye el tráfico entre las instancias de WordPress. Estas instancias están conectadas a un servidor de base de datos MySQL y a un servidor de archivos compartidos mediante NFS. A continuación, se detallan los pasos para la configuración de cada componente.

# 4. Requisitos
Docker y Docker Compose instalados en cada instancia.
Permisos y reglas de seguridad configurados correctamente para permitir la comunicación entre las instancias.

# 5. Guía paso a paso de instalación
# 5.1. Instalación de MySQL
Instalar Docker y Docker Compose:
sudo apt update && sudo apt install docker.io docker-compose -y

Clonar el repositorio:
git clone <este repositorio>
cd /Database
sudo docker-compose up -d

# 5.2. Configuración del servidor NFS
Instalar NFS:
sudo apt update
sudo apt install nfs-kernel-server -y

Configurar el directorio compartido:
sudo mkdir -p /mnt/wordpress
sudo chown nobody:nogroup /mnt/wordpress
sudo chmod 777 /mnt/wordpress

Editar el archivo /etc/exports:
sudo nano /etc/exports

Añade la línea con las IPs de las instancias de WordPress:
/mnt/wordpress 172.31.95.168(rw,sync,no_subtree_check) 172.31.87.149(rw,sync,no_subtree_check)

Aplicar cambios y reiniciar NFS:
sudo exportfs -a
sudo systemctl restart nfs-kernel-server

# 5.3. Configuración de las instancias de WordPress
Instalar Docker y NFS:
sudo apt update && sudo apt install docker.io docker-compose nfs-common -y

Montar el directorio NFS:
sudo mkdir -p /mnt/wordpress
sudo mount 172.31.85.124:/mnt/wordpress /mnt/wordpress

Clonar el repositorio y desplegar WordPress:
git clone <este repo>
cd /Wordpress
sudo docker-compose up -d

# 5.4. Configuración del balanceador de cargas (Nginx)
Instalar Docker:
sudo apt update && sudo apt install docker.io docker-compose -y

Modificar el archivo nginx.conf con las IPs de las instancias de WordPress:
upstream backend {
    server 172.31.95.168;
    server 172.31.87.149;
}

Clonar repositorio y Levantar el contenedor de Nginx:
git clone <este repo>
cd /Load-balancer/loadbalancer
sudo docker-compose up -d

# 6. Pruebas
Acceder a la IP pública del balanceador de cargas:
http://3.83.220.209/wp-admin/install.php
Puede que las instancias donde estan alojados los recursos no esten ejecucion, por lo tanto, no estara disponible la pagina web