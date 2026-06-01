# 📑 [PORTAFOLIO] Auditoría de Seguridad Perimetral: Análisis de Obsolescencia en Dispositivos de Borde (ISP)
Analista: Juan Cadena

Fecha del Análisis: 31 de Mayo de 2026

Tipo de Proyecto: Gestión de Vulnerabilidades / Auditoría de Infraestructura

Entorno: Red Local (Laboratorio Casero)

## 1. Objetivo del Proyecto (Objective)
Levantamiento y análisis de vulnerabilidades en red LAN con enfoque en el router, con el fin de identificar posibles brechas de seguridad, firmwares obsoletos y evaluar el riesgo de exposición ante amenazas externas.

## 2. Metodología y Recopilación de Telemetría (Methodology)

Para obtener los datos técnicos del dispositivo sin interrumpir el servicio, se aplicó el siguiente procedimiento de reconocimiento (Reconnaissance):

1. **Identificación de la Puerta de Enlace:** Se utilizó la interfaz de línea de comandos de Windows (CMD) ejecutando el comando `ipconfig` para localizar la IP privada del router.

<img width="1043" height="741" alt="image" src="https://github.com/user-attachments/assets/6331b203-1b7a-483c-841e-7d550ba95504" />

2. **Extracción de Información en la Consola Web:** Se ingresó la IP privada en el navegador web, accediendo directamente al panel de estado del dispositivo provisto por el ISP para extraer los datos de manufactura.

<img width="1425" height="903" alt="image" src="https://github.com/user-attachments/assets/33fdd0d2-5735-47ea-8a4e-c6d52807b3e3" />

   * **Dispositivo:** Módem/Router (ISP).
   * **Versión de Hardware:** `3.35.1`
   * **Versión de Firmware Activa:** `9.36.2020`

## 3. Análisis de Riesgo y Vulnerabilidades (Risk Assessment)

Tras analizar los identificadores de versiones, se determinaron los siguientes hallazgos críticos de seguridad:

* **Obsolescencia Crítica del Sistema (Firmware):** La versión de firmware detectada (`9.36.2020`) indica que el sistema operativo interno del router fue compilado o actualizado por última vez en el año **2020**. Al encontrarnos en el año **2026**, el dispositivo acumula **6 años sin recibir parches de seguridad**.
* **Ciclo de Vida del Hardware:** La revisión física `3.35.1` estima una antigüedad del componente de hardware de entre 6 y 8 años (Pre-pandemia). Existe un alto riesgo de que el fabricante haya declarado este modelo como **End of Life (EoL)**, cesando el soporte técnico definitivo.

### Impacto Potencial en la Seguridad (Vectores de Ataque):

1. **Explotación de CVEs Públicos e Inyección de Código (RCE):** Un atacante externo podría escanear el puerto WAN (IP pública) del router y, al detectar la versión de 2020, ejecutar exploits públicos para lograr la **Ejecución Remota de Código (RCE)** o provocar una Denegación de Servicio (DoS). Debido a la falta de validación en la memoria de un firmware obsoleto, solicitudes maliciosas dirigidas podrían sobrecargar el procesador, congelando los protocolos de seguridad internos (*Fail-Open*). Esto permitiría forzar la apertura de puertos no autorizados o el despliegue de una *Reverse Shell* para tomar el control total del dispositivo desde el exterior.

2. **Falta de Cifrado Moderno:** Hardware de esta antigüedad suele carecer de soporte para protocolos de seguridad inalámbrica modernos (como WPA3), limitando la red local a WPA2, el cual es vulnerable a ataques de desautenticación y captura de *handshakes* para el descifrado de contraseñas.

3. **Agobio de Procesamiento por Fuerza Bruta:** El procesador de un hardware antiguo es altamente susceptible a la saturación por tráfico masivo. Un ataque automatizado de denegación de servicio o intentos repetidos de acceso agotarían la CPU y la RAM del módem, provocando la caída total de la red local.

## 4. Plan de Remediación Propuesto (Mitigation Plan)

Como analista de seguridad, se proponen las siguientes acciones prioritarias para mitigar los riesgos identificados en la infraestructura perimetral:

1. **Aislamiento de la Interfaz de Gestión (Hardening):** Verificar y desactivar por completo la opción de "Administración Remota WAN" dentro de la configuración del router. Esto garantiza que la consola web del dispositivo solo sea accesible desde la red local (LAN) y permanezca totalmente invisible ante escaneos de puertos automatizados desde el internet público.

2. **Actualización Forzada o Reemplazo del Activo:** * Coordinar con el soporte técnico del proveedor de servicios (ISP) para validar si existe un parche de firmware firmado posterior a la versión `9.36.2020` que sea compatible con la revisión de hardware `3.35.1`.
   * En caso de confirmarse que el fabricante ha clasificado el dispositivo como *End of Life* (EoL), se debe gestionar el reemplazo físico obligatorio del módem por un modelo vigente con soporte de seguridad activo.

3. **Segmentación y Perímetro Dedicado (Modo Puente):** Como mejor práctica de arquitectura segura, se recomienda configurar el módem del ISP en **Modo Puente (Bridge)**. Esto delega la función de enrutamiento y la inspección de tráfico a un Firewall perimetral dedicado de código abierto (como *pfSense* o *OPNsense*), permitiendo un control estricto sobre las conexiones entrantes y bloqueando de manera proactiva cualquier intento de tráfico saliente no autorizado (*Reverse Shells*).
