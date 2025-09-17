# Traefik con Docker

Este proyecto sigue la guía oficial de [Traefik con Docker](https://doc.traefik.io/traefik/getting-started/docker/) para configurar un proxy inverso y enrutar servicios de manera sencilla.

---

Para empezar se nos pide verificar los requisitos necesarios para utilizar Traefik. Para eso vemos las versiones que tenemos en Docker y Docker Compose instaladas:

<img width="476" height="181" alt="image" src="https://github.com/user-attachments/assets/68a021f0-15a2-444f-8cf7-0da845465d6f" />

## 1. Archivo docker-compose.yml

Aquí se define el servicio de *Traefik*, que:
- Usa la imagen traefik:v3.5.
- Expone los puertos 80 (para aplicaciones) y 8080 (para el dashboard).
- Monta el socket de Docker para descubrir servicios automáticamente.
- Habilita el proveedor Docker y la API insegura (solo desarrollo).

<img width="522" height="354" alt="docker-yml" src="https://github.com/user-attachments/assets/b8d67448-cc7f-4705-a532-824bb99b7602" />

---

## 2. Levantar Traefik con docker compose up

Se inicia el servicio en segundo plano con:

```bash
docker compose up -d
```
<img width="1909" height="219" alt="docker-compose-up" src="https://github.com/user-attachments/assets/281cc9f5-b66a-48f5-9c9e-068cecfc9edb" />

Verificamos:

<img width="1919" height="100" alt="image" src="https://github.com/user-attachments/assets/905168de-58ca-4c48-b568-8c26dc019f2f" />

## 3. Dashboard de Traefik

El dashboard queda expuesto en el puerto 8080, accesible en:

http://localhost:8080/dashboard/

<img width="1919" height="995" alt="dashboard" src="https://github.com/user-attachments/assets/cc21552f-6cc1-423b-98bb-93fdf50d0ee6" />


## 4. Archivo whoami.yml

Se define un servicio de prueba llamado whoami que responde con información del contenedor.
Se usa una label para enrutar el tráfico cuando el host sea whoami.localhost.

<img width="700" height="227" alt="whoami-yml" src="https://github.com/user-attachments/assets/0b21af19-7bc8-4803-baea-1ac5ed482e9b" />

## 5. Prueba con whoami.localhost

Se verifica en whoami.localhost para verificar si funcionó correctamente:

<img width="961" height="515" alt="whoami" src="https://github.com/user-attachments/assets/4f0f0d5b-daf7-459f-8ffb-c674c3879a05" />

## 6. whoami en el Dashboard

En el panel de Traefik, dentro de la sección HTTP Routers, aparece el enrutamiento de whoami.localhost.

<img width="1918" height="927" alt="whoami-dashboard" src="https://github.com/user-attachments/assets/57b0665e-9887-4e0a-9ce1-6dfd5eca6ba7" />

---
---

# Preguntas

- **¿Qué ventaja aporta enrutar por host (dominio) vs por puerto?**

Con hostnames (ej: app1.localhost, app2.localhost) se puede tener múltiples aplicaciones en el mismo puerto sin conflictos, lo que simplifica despliegues y evita manejar muchos puertos distintos.

Con puertos, cada servicio requiere un puerto diferente, lo que escala peor y es menos amigable para el usuario.

- **¿Qué diferencia hay entre labels en los servicios y usar archivos de configuración?**

**Labels:** se definen directamente en el servicio del contenedor (docker-compose.yml). Son rápidas y prácticas para configuraciones simples.

**Archivos de configuración:** permiten mayor flexibilidad, centralizar reglas y manejar setups más complejos sin sobrecargar el compose.

- **¿Cómo se entera Traefik de que había servicios nuevos?**

Traefik escucha el Docker socket (/var/run/docker.sock). Cada vez que un contenedor se levanta o cae, Traefik se entera automáticamente y actualiza la configuración interna en tiempo real.
