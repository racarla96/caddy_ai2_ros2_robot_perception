# caddy_ai2_ros2_robot_perception

Pila de percepción del **Caddy AI2** (carrito de golf, plataforma de pruebas de vehículos) en hardware real.
Corre en la **Jetson Orin NX** (ROS 2 Jazzy; el plan es pasarla a Ubuntu 24.04 y, si no es posible, usar un contenedor Docker sobre JetPack 6.2).
El control de bajo nivel corre en el LattePanda Alpha: [`caddy_ai2_ros2_robot_lowlevel`](https://github.com/racarla96/caddy_ai2_ros2_robot_lowlevel).

**ROS 2:** Jazzy | **Proyecto:** CERVAREC

## Qué lleva

| Paquete | Rol |
|---|---|
| `caddy_ai2_ros2_description` | Descripción del robot (fuente única compartida con la simulación) |
| `caddy_ai2_ros2_robot_description_publisher` | Publicador de `robot_description` |
| `caddy_ai2_ros2_sensors_rs_helios_5515` | LiDAR 3D RoboSense Helios RS-5515 |
| `caddy_ai2_ros2_imu_vectornav_vn100` | IMU VectorNav VN-100 |
| `caddy_ai2_ros2_camera_zed_2i` | Dos ZED 2i (disposición A: dos delanteras, o B: delantera y trasera) |
| *(pendiente)* `caddy_ai2_ros2_sensors_gps_ublox` | GPS u-blox propio de la Jetson |

## Clonar y compilar

```bash
git clone --recurse-submodules git@github.com:racarla96/caddy_ai2_ros2_robot_perception.git
```

Las ZED necesitan el ZED SDK y `zed-ros2-wrapper` (submódulo en `caddy_ai2_ros2_camera_zed_2i/third_party`),
que solo se compilan dentro del contenedor de la Jetson, no en un PC de desarrollo.

## Pendiente

- Paquete del GPS u-blox (hoy no existe repo).
- `Dockerfile` / `docker-compose` del contenedor de la Jetson.
- `perception.launch.py` que arranque solo los sensores de este equipo (campo `host: jetson` en `robot_params.yaml`).
- Reconciliar los frames ópticos del wrapper real con los `<frame_id>_optical` del URDF.
- Red: mismo `ROS_DOMAIN_ID` que el LattePanda, RMW y interfaz de red fijados, y hora sincronizada con chrony.
- Tres fuentes de posición conviven (SBG en el LattePanda, u-blox y la ZED), cada una con su topic; la localización decide cuál(es) usa.
- Instalar en la Jetson los drivers de Helios, VN-100 y ZED 2i; actualizar su SO (ver plan, Fase E).
- Medir las poses reales de las ZED en la barra del vehículo.

Plan completo: [`PLAN_REORGANIZACION.md`](https://github.com/racarla96/caddy_ai2/blob/jazzy/docs/PLAN_REORGANIZACION.md).
Licencia CC BY 4.0.
