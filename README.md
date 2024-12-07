# Monitoreo con Nagios usando Docker

Sistema de monitoreo utilizando Nagios Core para supervisar servicios MySQL y NGINX en contenedores Docker.

## Requisitos

- Docker
- Docker Compose
- Ubuntu (probado en Ubuntu 24.04)

## Instalación de Requisitos Previos

1. **Instalar Docker**:
```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

2. **Instalar Docker Compose**:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Instalación de Nagios

1. **Clonar el repositorio**:
```bash
git clone https://github.com/juanramirez-tech/nagios-docker-monitoring.git
cd nagios-monitoring
```

2. **Crear la estructura de directorios**:
```bash
mkdir -p nagios/config/objects
mkdir -p nagios/plugins
mkdir -p data/mysql
mkdir -p nginx/conf.d
```

3. **Configurar los archivos**:
- Los archivos de configuración ya están incluidos en el repositorio
- Ubicados en:
  - nagios/config/nagios.cfg
  - nagios/config/objects/commands.cfg
  - nagios/config/objects/contacts.cfg
  - nagios/config/hosts.cfg
  - nagios/config/services.cfg

4. **Iniciar los contenedores**:
```bash
sudo docker-compose up -d
```

## Acceso a la Interfaz

- **URL**: http://localhost:8080/nagios/
- **Usuario**: nagiosadmin
- **Contraseña**: nagios123

## Servicios Monitoreados

### MySQL
- Puerto: 3307 (externo), 3306 (interno)
- Credenciales:
  - Usuario: testuser
  - Contraseña: testpass
  - Base de datos: testdb

### NGINX
- Puerto: 8081 (externo), 80 (interno)
- Acceso web: http://localhost:8081

## Verificación de Servicios

1. **Probar MySQL**:
```bash
# Desde el host
mysql -h localhost -P 3307 -u testuser -p'testpass'

# Desde el contenedor de Nagios
sudo docker exec -it nagios-monitoring-nagios-1 bash
/opt/nagios/libexec/check_tcp -H mysql -p 3306
```

2. **Probar NGINX**:
```bash
# Desde el host
curl http://localhost:8081

# Desde el contenedor de Nagios
sudo docker exec -it nagios-monitoring-nagios-1 bash
/opt/nagios/libexec/check_tcp -H nginx -p 80
```

## Mantenimiento

### Reiniciar Servicios
```bash
# Reiniciar todo
sudo docker-compose restart

# Reiniciar servicio específico
sudo docker-compose restart nagios
sudo docker-compose restart mysql
sudo docker-compose restart nginx
```

### Verificar Logs
```bash
# Ver logs de todos los servicios
sudo docker-compose logs

# Ver logs de un servicio específico
sudo docker-compose logs nagios
sudo docker-compose logs mysql
sudo docker-compose logs nginx
```

### Detener Servicios
```bash
# Detener todos los servicios
sudo docker-compose down

# Detener servicio específico
sudo docker-compose stop nagios
```

## Solución de Problemas

1. **Error de Plugins**:
```bash
sudo docker exec -it nagios-monitoring-nagios-1 bash
apt-get update
apt-get install -y monitoring-plugins
cp /usr/lib/nagios/plugins/* /opt/nagios/libexec/
chown -R nagios:nagios /opt/nagios/libexec/
chmod +x /opt/nagios/libexec/*
```

2. **Verificar Configuración de Nagios**:
```bash
sudo docker exec -it nagios-monitoring-nagios-1 /opt/nagios/bin/nagios -v /opt/nagios/etc/nagios.cfg
```

3. **Problemas de Permisos**:
```bash
sudo chown -R 999:999 nagios/
```

## Estructura del Proyecto
```
nagios-monitoring/
├── docker-compose.yml
├── nagios/
│   └── config/
│       ├── objects/
│       │   ├── commands.cfg
│       │   ├── contacts.cfg
│       │   ├── templates.cfg
│       │   └── timeperiods.cfg
│       ├── hosts.cfg
│       ├── services.cfg
│       ├── nagios.cfg
│       └── resource.cfg
├── nginx/
│   └── conf.d/
│       └── default.conf
└── README.md
```

## Contribuir

1. Fork del repositorio
2. Crear una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit de tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir un Pull Request

## Notas de Seguridad

- Cambiar las contraseñas por defecto antes de usar en producción
- Configurar correctamente los firewalls y accesos
- Mantener los contenedores actualizados

## Licencia

Este proyecto está bajo la Licencia MIT - ver el archivo [LICENSE.md](LICENSE.md) para más detalles.

## Autor

Juan Ramirez - [GitHub](https://github.com/juanramirez-tech)
