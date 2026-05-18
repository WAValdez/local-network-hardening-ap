# Reutilización y Hardening de ONT Huawei HG8546M como Punto de Acceso Inalámbrico

## 📌 Resumen Ejecutivo
Este proyecto detalla el procedimiento técnico aplicado para mitigar una zona de baja cobertura inalámbrica ("zona muerta") en un entorno doméstico, reutilizando hardware de grado de proveedor de servicios (ISP). Se intervino una terminal de red óptica (ONT) **Huawei EchoLife HG8546M**, configurándola como un Punto de Acceso (AP) secundario interconectado mediante un enlace troncal de capa 2 (Ethernet).

A lo largo del despliegue se resolvieron desafíos críticos relacionados con el aislamiento del firmware propietario, la resolución de conflictos de direccionamiento IPv4 entre subredes disjuntas y la remediación de vulnerabilidades criptográficas en la interfaz aérea (WLAN).

---

## 🛠️ Especificaciones del Escenario
* **Dispositivo Intervenido:** Huawei EchoLife HG8546M (GPON ONU)
* **Router Principal (Gateway):** Operando en el segmento `192.168.1.0/24` (IP: `192.168.1.1`)
* **Nueva IP de Gestión del AP:** `192.168.1.254` (Mapeada In-Band)
* **Cifrado Wi-Fi Final:** WPA2-PSK (AES)

---

## 🚀 Fases de Implementación

### 1. Aislamiento Físico y Evasión del Bloqueo de ISP
Para evitar que el dispositivo sincronizara directivas de aprovisionamiento remoto del ISP, se aisló físicamente desconectando la interfaz GPON (fibra óptica) y los enlaces LAN. Se ejecutó un factory reset de 15 segundos para forzar el retorno al firmware base, permitiendo el acceso mediante credenciales de ingeniería global (`telecomadmin` / `admintelecom`).

### 2. Realineación de Subredes y Reingeniería LAN (Capa 2)
La ONT operaba por defecto en el segmento `192.168.100.0/24`. Para integrarla a la red local y mantener la administración sin perder conectividad, se configuró una IP estática temporal en el host de administración (`192.168.100.50`) para acceder a la interfaz web.

Posteriormente, bajo el menú `LAN > LAN Host Configuration`, se aplicaron los siguientes cambios:
* **Primary IP Address:** `192.168.1.254` (extremo superior seguro de la subred principal).
* **DHCP Server:** **Deshabilitado**. La ONT pasa a trabajar estrictamente como un Bridge de Capa 2, delegando la asignación de direccionamiento IP de forma centralizada al Gateway Principal (`192.168.1.1`).


> ![Configuración de Interfaz LAN y DHCP](./assets/lan_dhcp_config.png)]
> ![Configuración de Interfaz DHCP](./assets/lan_dhcp_config1.png)]

### 3. Hardening Inalámbrico (Remediación de Cifrado Vulnerable)
El firmware nativo negociaba por defecto algoritmos basados en **TKIP**, provocando alertas de "Seguridad Baja" en los dispositivos clientes debido a sus vulnerabilidades estructurales ante ataques de inyección de paquetes.

Se accedió a `WLAN > WLAN Basic Configuration` y se aplicó el endurecimiento perimetral:
* **Authentication Mode:** Fijado estrictamente en `WPA2 Pre-Shared Key`.
* **Encryption Mode:** Configurado exclusivamente en `AES` (deshabilitando por completo TKIP).


> ![Hardening Criptográfico WPA2-AES](./assets/wlan_hardening.png)

---

## 📊 Resultados y Verificación
* **Latencia y Rendimiento:** Al utilizar un enlace troncal físico Ethernet se evitó la degradación del 50% del ancho de banda clásico de los repetidores inalámbricos comerciales (WISP).
* **Mitigación de Riesgos:** El perímetro inalámbrico quedó alineado con los estándares modernos de confidencialidad, desapareciendo las alertas de seguridad en los hosts.

> ![Evidencia Física de Conectividad](./assets/hw_backplate.png)
> ![Verificación de Seguridad en Host Cliente](./assets/client_verification.png)
