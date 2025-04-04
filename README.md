# Proyecto de Infraestructura Local con Docker y Traefik

## Instrucciones de Despliegue

1. **Requisitos Previos**: Asegúrate de tener Docker y Docker Compose instalados en tu máquina.

2. **Clonar el Repositorio**: Clona este repositorio en tu máquina local.

3. **Navegar al Directorio del Proyecto**:
   ```bash
   cd Taller3Distribuidos
   ```

4. **Construir y Levantar los Contenedores**:
   ```bash
   docker compose up --build -d
   ```

5. **Acceder a los Servicios**: Una vez que los contenedores estén en funcionamiento, puedes acceder a:
   - API Registro: `http://api.localhost` o `http://localhost/registro` (requiere autenticación)
   - Clientes: 
     - `http://cliente.localhost/cliente/app1`
     - `http://cliente.localhost/cliente/app2`
     - `http://cliente.localhost/cliente/app3`
   - Monitor: `http://monitor.localhost`
   - Dashboard de Traefik: `http://traefik.localhost:8080`

## Arquitectura y Servicios

### Traefik como Reverse Proxy
- **Dashboard habilitado**: Accesible solo desde localhost en `http://traefik.localhost:8080`
- **EntryPoint web**: Configurado en el puerto 80 para todos los servicios

### Servicios Principales

- **api-registro**:
  - Servicio backend que lleva el conteo de cuántos servicios le han hecho peticiones
  - Incrementa un contador asociado al nombre del cliente (servicio)
  - Accesible mediante autenticación básica en la ruta `/registro`

- **cliente-app (x3)**:
  - Tres instancias replicadas que hacen solicitudes periódicas al api-registro
  - Cada instancia se identifica con un nombre único mediante headers HTTP
  - Las réplicas están expuestas bajo rutas como `/cliente/app1`, `/cliente/app2`, `/cliente/app3`

- **monitor**:
  - Servicio web que muestra el estado de la infraestructura
  - Visualiza cuántos registros lleva api-registro, qué rutas están activas, y qué clientes se han comunicado

## Configuración de Ruteo y Middlewares

- **Rutas Cliente**: Las rutas `/cliente/app*` se dirigen a sus respectivas instancias y aplican `stripPrefix`
- **Ruta Registro**: Las llamadas a `/registro` (hacia api-registro) pasan por autenticación básica
- **Headers de Identificación**: Los clientes incluyen un header `X-Service-ID` que es leído por api-registro para identificar la fuente de la llamada

## Configuración Dinámica

Se utilizan etiquetas (labels) en `docker compose.yml` para declarar:
- Routers para enrutar solicitudes
- Servicios para definir destinos
- Middlewares para modificar solicitudes o respuestas

## Dashboard de Traefik

Puedes acceder al dashboard de Traefik en `http://traefik.localhost:8080`, donde podrás ver:
- Estado de todos los servicios
- Rutas configuradas
- Middlewares aplicados
- Estadísticas de tráfico

## Contribuciones

Las contribuciones son bienvenidas. Si deseas mejorar este proyecto, por favor abre un issue o un pull request.

## Credenciales

Para acceder al servicio `api-registro` a través de la ruta `/registro`:
- Usuario: `user`
- Contraseña: `password`