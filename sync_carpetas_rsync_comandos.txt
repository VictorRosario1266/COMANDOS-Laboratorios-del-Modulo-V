
# Titulo: Sincronización de Carpetas con Rsync en Parrot Security y Kali Linux

## 1. Instalar rsync en ambas máquinas
# En Parrot Security y Kali Linux:
sudo apt update && sudo apt install rsync -y

## 2. Crear la carpeta y los archivos en el servidor primario (Parrot Security)
# Ejecuta en Parrot Security:
mkdir -p /home/yuh/sync_folder
cd /home/yuh/sync_folder
touch file{1..100}.txt

# Verificar que los archivos fueron creados:
ls -l /home/yuh/sync_folder

## 3. Configurar permisos en el servidor primario
sudo chown -R yuh:yuh /home/yuh/sync_folder
chmod -R 755 /home/yuh/sync_folder

## 4. Crear la carpeta en el servidor secundario (Kali Linux)
# Ejecuta en Parrot Security:
ssh yuh@172.22.178.11 'mkdir -p /home/yuh/sync_folder'

# Verificar que la carpeta se creó correctamente en Kali Linux:
ssh yuh@172.22.178.11 'ls -ld /home/yuh/sync_folder'

## 5. Copiar los archivos con rsync
# Ejecuta en Parrot Security:
rsync -avz /home/yuh/sync_folder/ yuh@172.22.178.11:/home/yuh/sync_folder/

# Verifica en Kali Linux:
ssh yuh@172.22.178.11 'ls -l /home/yuh/sync_folder'

## 6. Crear un script de sincronización automática
# Ejecuta en Parrot Security:
nano /home/yuh/sync_script.sh

# Agrega este contenido al archivo:
#!/bin/bash
rsync -avz /home/yuh/sync_folder/ yuh@172.22.178.11:/home/yuh/sync_folder/

# Guardar y salir (CTRL + X, luego Y y ENTER).

# Dar permisos de ejecución al script:
sudo chmod +x /home/yuh/sync_script.sh

## 7. Configurar crontab para sincronización cada minuto
# Ejecuta en Parrot Security:
export VISUAL=nano; crontab -e

# Agrega esta línea al final:
* * * * * /home/yuh/sync_script.sh

# Guardar y salir (CTRL + X, Y, ENTER).

## 8. Probar que crontab funciona
# Ejecuta en Parrot Security:
touch /home/yuh/sync_folder/test_sync.txt

# Esperar 1 minuto, luego verificar en Kali Linux:
ls -l /home/yuh/sync_folder/
