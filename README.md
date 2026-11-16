# caddy_ai2_ros2_robot_perception

Pila de percepción del **Caddy AI2** (carrito de golf, plataforma de pruebas de vehículos) en hardware real.
Corre en la **Jetson Orin NX** (ZED Box; ROS 2 Jazzy sobre Ubuntu 24.04 tras la actualización del SO, con contenedor Docker como alternativa).
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

## Estado en la ZED Box (Ubuntu 24.04, Jazzy nativo, aarch64)

| Sensor | Estado | Cómo |
|---|---|---|
| Helios RS-5515 | ✅ 10,00 Hz con QoS `reliable`, 57 600 pts/nube | `ros2 launch caddy_ai2_ros2_sensors_rs_helios_5515 test.launch.py` (driver + RViz + monitor); parar con `ros2 run caddy_ai2_ros2_sensors_rs_helios_5515 stop_helios.sh` |
| VN-100 | ✅ 200 Hz, 921600 baudios, ejes `rep103` | `ros2 launch caddy_ai2_ros2_imu_vectornav_vn100 vn100.launch.py` (`/dev/vn100`) |
| ZED 2i | en la otra rama | ver `caddy_ai2_ros2_camera_zed_2i` |

Aviso: un suscriptor `best_effort` recibe solo 6-7 Hz de la nube del Helios por pérdida en DDS;
los consumidores deben usar `reliable`. Detalles en cada paquete y en el plan, Fase E.

## Pendiente

- Paquete del GPS u-blox (hoy no existe repo).
- Contenedor de la Jetson: solo si la ZED lo exige; el Helios y el VN-100 corren nativos.
- `perception.launch.py` que arranque solo los sensores de este equipo (campo `host: jetson` en `robot_params.yaml`).
- Reconciliar los frames ópticos del wrapper real con los `<frame_id>_optical` del URDF.
- Red: mismo `ROS_DOMAIN_ID` que el LattePanda, RMW y interfaz de red fijados, y hora sincronizada con chrony.
- Tres fuentes de posición conviven (SBG en el LattePanda, u-blox y la ZED), cada una con su topic; la localización decide cuál(es) usa.
- Instalar el ZED SDK y las dos ZED 2i en la Jetson. Helios y VN-100 ya funcionan y el SO ya es 24.04 (ver plan, Fase E).
- Arranque automático de los drivers (systemd), Tailscale y reglas udev del resto de dispositivos serie.
- Medir las poses reales de las ZED en la barra del vehículo.

Plan completo: [`PLAN_REORGANIZACION.md`](https://github.com/racarla96/caddy_ai2/blob/jazzy/docs/PLAN_REORGANIZACION.md).
Licencia CC BY 4.0.
