# Definiciones básicas
## 1. Protocolo
  ### Definición
  Conjunto de reglas y formatos acordados creados por organizaciones de normalización
  #### Suite de protocolos
  Grupo de protocolos interelacionados que son necesarios para realizar una función de comunicaciones
## 2. Internet Protocol Suite o TCP/IP
## 3. ProtocoloS de Interconexión de Sistemas Abiertos (OSI)
## 4. Protocolos de Enrutamiento
## 5. Dirección IP
## 6. Máscara de subred en IPv4
## 7. Puerta de Enlace

# Routers
<img width="607" height="345" alt="image" src="https://github.com/user-attachments/assets/386be6cf-eda5-4cb2-931d-7d9572e51764" />

#### Definición:
Un router es una computadora de propósito específico que opera en la Capa 3 (Red) del Modelo OSI y en la capa de Internet del modelo TCP/IP.
#### Misión
Su misión principal es una sola: Conectar redes diferentes y decidir cuál es el mejor camino (ruta) para enviar los paquetes de datos de origen a destino --> No hay caminos, se hacen los caminos, el routeer decide la ruta de los paquetes. Es por eso que los paquetes van por todow 

A diferencia del Switch (que conecta computadoras dentro de un mismo barrio usando direcciones MAC), el Router conecta distintos barrios usando Direcciones IP lógicas.

Es la Puerta de Enlace (Default Gateway) de tu red local. Si un paquete va dirigido a una IP que no está en tu red, se lo entregas al router para que él se encargue.
Importante: Se requiere un router por cada operador

--> Solo conocen las redes a las que están conectadas.
--> Cuandos las redes están directamente conectadas al router NO necesitan enrutamiento, solo es encesario cuando el router NO conoce a la red.
--> Número de redes que no conocen= número de rutas que se deben realizar por enrutamiento estático, o los que se deben señalarle al router.

#### Anatomía del router
<img width="547" height="353" alt="image" src="https://github.com/user-attachments/assets/c8563daa-ae0c-4cff-b0a8-53736c86f4e8" />

Al igual que tu laptop, un router Cisco tiene componentes físicos, pero organizados de forma distinta para priorizar la velocidad de procesamiento de red.

###### ROM (Read-Only Memory): 
Contiene las instrucciones de arranque (Bootstrap) y el POST (Power-On Self Test) que revisa que el hardware funcione al encender.

###### Memoria Flash: 
Es como el "disco duro" del router. Aquí se guarda el Cisco IOS (El Sistema Operativo de Internetwork). No se borra si se va la luz.

###### NVRAM (Non-Volatile RAM):
Guarda un archivo fundamental: el startup-config. Es la configuración permanente del router. No se borra al apagar el equipo.

###### RAM (Random Access Memory):
La memoria de trabajo. Es súper rápida pero volátil (si se apaga el router, se borra todo). Aquí vive:
    - El sistema operativo en ejecución.
    - La tabla de enrutamiento (Routing Table).
    - La caché ARP.

El archivo running-config (los comandos que estás tecleando en tiempo real).

💡 Pro-Tip de Laboratorio: Si te pasas 2 horas configurando un router en GNS3/Packet Tracer y lo apagas sin guardar, lo pierdes todo porque estaba en la RAM. Siempre debes pasar tu trabajo de la RAM a la NVRAM usando el comando: copy running-config startup-config (o el atajo write).
  
#### Configuración de una interfaz en el router
<img width="619" height="358" alt="image" src="https://github.com/user-attachments/assets/28b03a32-0999-4eba-9a38-ffb4ddb8594c" />

##### Configuración de una interfaz serial
<img width="712" height="358" alt="image" src="https://github.com/user-attachments/assets/24c3fd63-0fb8-430e-9c25-9d2d7de2de2c" />

#### Comandos para configuración del router
````
Router> enable                           # 1. Pasa del modo Usuario al modo Privilegiado.
Router# configure terminal               # 2. Entra al modo de Configuración Global.

# --- IDENTIDAD Y SEGURIDAD ---
Router(config)# hostname R1              # Cambia el nombre del router a "R1".
R1(config)# enable secret class          # Crea una contraseña encriptada ("class") para entrar al modo privilegiado.

# --- CONFIGURACIÓN DE INTERFACES (Cables) ---
R1(config)# interface gigabitEthernet 0/0          # Entra al puerto Gigabit 0/0.
R1(config-if)# ip address 192.168.1.1 255.255.255.0 # Le asigna una IP y Máscara a la interfaz (Gateway de la LAN).
R1(config-if)# no shutdown                         # ¡Enciende el puerto! (Levanta la interfaz físicamente).
R1(config-if)# exit                                # Vuelve al modo global.

R1(config)# interface serial 0/0/0                 # Entra a un puerto Serial (para conectar con otro router).
R1(config-if)# ip address 10.0.0.1 255.255.255.252 # Asigna la IP (usualmente con máscara /30 para enlaces punto a punto).
R1(config-if)# clock rate 64000                    # Sincroniza la velocidad (SOLO si este lado del cable es el DCE o "jefe").
R1(config-if)# no shutdown                         # Enciende el puerto serial.
R1(config-if)# exit
````

--> El router debe "molestar" a su vecino para llegar a la red destino  


#### Enrutamiento Estático (El modo Manual)
###### ¿Qué es?
Tú (el administrador) le dictas al router el camino exacto hacia una red desconocida.
###### Ventaja: 
No consume recursos del procesador y es muy seguro.
###### Desventaja:
Si la red es muy grande o un cable se rompe, tienes que reconfigurar todo a mano.
###### Comandos necesarios
```
ip route [Red_Destino] [Máscara_Destino] [IP_del_Siguiente_Salto]
```
```
# (Asumiendo que estás en el modo global: R1(config)# )

# Ejemplo 1: Ruta estática normal
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2  
# Traducción: "Para llegar a la red 192.168.2.0, envía el paquete a la IP 10.0.0.2 (que es la puerta del router vecino)".

# Ejemplo 2: Ruta estática por defecto (La "Ruta de escape")
R1(config)# ip route 0.0.0.0 0.0.0.0 200.20.20.1

```
#### Enrutamiento Dinámico (EL Modo Automático)
###### ¿Qué es?
Los routers usan un protocolo (como RIP, OSPF o EIGRP) para hablar entre ellos y descubrir el mapa de la red automáticamente.

###### Ventaja:
Si un cable se rompe, los routers calculan solos un nuevo camino.

###### Desventaja:
Consume memoria RAM y procesador del equipo.
###### Comandos Necesarios
Aquí usamos OSPF (Open Shortest Path First), que es el estándar de la industria y el protocolo estrella que verás en tu curso:

```
# (Asumiendo que estás en el modo global: R1(config)# )

# 1. Encendemos el "cerebro" dinámico
R1(config)# router ospf 1                
# Inicia el proceso de enrutamiento OSPF con el identificador "1".

# 2. Presentamos nuestras redes a los vecinos (Comando Network)
# OJO: OSPF no usa la máscara de subred normal, usa la "Wildcard" (la máscara invertida).
R1(config-router)# network 192.168.1.0 0.0.0.255 area 0  
# Traducción: "Publica mi red local (192.168.1.0) hacia los demás routers, y dile que pertenecemos a la zona principal (Area 0)".

R1(config-router)# network 10.0.0.0 0.0.0.3 area 0       
# Traducción: "Publica también mi red WAN (el enlace punto a punto) para que los vecinos sepan cómo llegar a mí".
R1(config-router)# end

# Traducción: "Si alguien te pide ir a una IP que NO conoces (como Internet), envíalo todo a la IP 200.20.20.1 (el router del proveedor)".
```

#### Guardar y verificar 
###### Comandos de verificación
Una vez que terminaste de configurar todo, debes revisar si funcionó y guardar tu trabajo para que no se borre si apagas el simulador. (Se hace en el modo Privilegiado #).

```
# --- COMANDOS DE DIAGNÓSTICO ---
R1# show ip route               # El comando MÁS importante. Te muestra la tabla de enrutamiento. 
                                # (Busca las líneas con 'C' de Conectada, 'S' de Estática, o 'O' de OSPF).

R1# show ip interface brief     # Te muestra una lista rápida de tus cables. Verifica que digan "Up / Up".

R1# ping 192.168.2.10           # Lanza paquetes de prueba para ver si llegas al destino.

# --- GUARDAR EL TRABAJO ---
R1# copy running-config startup-config  
# Copia todo lo que acabas de teclear (RAM) al disco duro seguro del router (NVRAM).
```

