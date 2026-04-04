# Tarea: Enrutamiento Estático en Topología de Estrella (HUB Central)

Este proyecto resuelve el problema de interconectividad entre 4 sedes remotas utilizando un HUB como punto central de comunicación, bajo restricciones de hardware y direccionamiento IPv4.

## 📋 Enunciado y Restricciones
La topología requiere conectar 8 subredes distintas (2 por router) con las siguientes limitaciones:
- **Dispositivo Central:** HUB (Capa 1), lo que implica un único dominio de difusión para todos los routers.
- **Interfaces:** Máximo 2 interfaces físicas por router.
- **Direccionamiento:** Máximo 2 direcciones IPv4 por interfaz física.
- **Método:** Enrutamiento Estático exclusivamente.



## 🛠️ Diseño de la Solución

### 1. Red de Tránsito (Backbone)
Para que los routers puedan establecer saltos entre sí, se configuró una red común en el segmento del HUB. Se seleccionó la red **10.10.10.0/24** para este propósito:
- **R1 (F0/1):** 10.10.10.1
- **R2 (F0/1):** 10.10.10.2
- **R3 (F0/1):** 10.10.10.3
- **R4 (F0/1):** 10.10.10.4

### 2. Tablas de Enrutamiento
Cada router fue configurado con rutas estáticas apuntando a la IP del router vecino correspondiente en la red de tránsito.

#### Ejemplo: Tabla de Enrutamiento de R1
| Red Destino | Máscara | Siguiente Salto (Next Hop) |
| :--- | :--- | :--- |
| 192.168.20.0 (PC3) | 255.255.255.0 | 10.10.10.2 |
| 192.168.30.0 (PC5) | 255.255.255.0 | 10.10.10.3 |
| 192.168.55.0 (PC7) | 255.255.255.0 | 10.10.10.4 |



## 💻 Configuración (Cisco IOS)
A continuación, se muestra un fragmento de la configuración aplicada en el **Router 1** para habilitar la comunicación hacia las demás sedes:

```cisco
interface FastEthernet0/1
 ip address 10.10.10.1 255.255.255.0
 no shutdown

! Rutas hacia Sede R2
ip route 192.168.20.0 255.255.255.0 10.10.10.2
ip route 220.110.33.0 255.255.255.0 10.10.10.2

! Rutas hacia Sede R3
ip route 192.168.30.0 255.255.255.0 10.10.10.3
ip route 192.168.40.0 255.255.255.0 10.10.10.3

! Rutas hacia Sede R4
ip route 192.168.55.0 255.255.255.0 10.10.10.4
ip route 192.168.60.0 255.255.255.0 10.10.10.4
