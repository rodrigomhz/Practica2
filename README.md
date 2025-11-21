# Practica2

## 1. Desarrollar un script que permita automatizar las siguientes acciones de cara a realizar el proceso de identificación y enumeración. El alumno podrá seleccionar el lenguaje de programación que quiera, y en la memoria se deberá describir el desarrollo realizado y un caso de uso con los resultados. La herramienta deberá desarrollar las siguientes acciones de forma automática a partir de un dominio dado:

Obtener whois del dominio proporcionado

- En caso de existir direcciones de correo en Whois, comprobar filtraciones

- Comprobar que el dominio está vivo, y realizar un escaneo del TOP 10 de puertos

- Mostrar la información del dominio, servidores NS y MX, asi como filtraciones y puertos abiertos

Realizaremos un script que automatiza el proceso de identificación y enumeración de un dominio. Realiza las siguientes acciones de forma automática:

*1. Obtiene información WHOIS del dominio proporcionado.*

*2. Si el WHOIS contiene direcciones de correo electrónico, consulta la API de IntelX para verificar si esos correos han sido comprometidos en filtraciones.*

*3. Comprueba si el dominio está activo (vivo) con un `ping`.*

*4. Realiza un escaneo de los primeros 10 puertos del dominio.*

*5. Muestra la información de los servidores de nombres (NS) y servidores de correo (MX) asociados al dominio.*

## Detalles Importantes del Script

- **`whois`**: Comando para obtener la información WHOIS del dominio.

* Nos devolverá mucha información, como estados, nombres de servidor, advertencia, etc... . En esencia, nos devuelve un tocho enorme

- **`ping`**: Para comprobar si el dominio está activo.
- **`jq`**: Herramienta para procesar respuestas JSON de la API de IntelX. JQ nos permite procesar datos json, pudiendo en esta caso buscar la existencia de data.

- **Hay que recordad la necesidad de darle permisos de ejecuación, podemos hacerl con el comando:**
```
   chmod +x script
````

Al ejecutar nuestro script, obtendremos mucha información:

![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/SalidaScript.jpg)
De aquí podemos sacar muchas cosas, como:

## 1- Información clave extraída del WHOIS ##
    
1. Identificación del dominio

Nosotros hemos escogido usar el dominio --> ````adobe.com````, y al obtener

***Domain ID: 4364022_DOMAIN_COM-VRSN***, probamos que el dominio existe y está registrado.

2. Correos electrónicos extraídos
 
 ````
 abuse@comlaude.com
 dns-admin@adobe.com
 ````
 Interpretación:

    . abuse@comlaude.com → correo del registrar (para abuso, fraude, DMCA…).

    . dns-admin@adobe.com → correo real interno de Adobe (equipo DNS/SecOps).
## 2- Filtraciones en IntelX ##
````
No hay filtraciones para abuse@comlaude.com
No hay filtraciones para dns-admin@adobe.com
````
Interpretación:
    . No se encontraron filtraciones públicas indexadas para esos emails.

### IMPORTANTE:
Esto es normal para correos corporativos bien protegidos.

## 3- Comprobación del dominio (ping)
````
PING adobe.com (185.43.182.42)
time=42.6 ms
````
Interpretación:
    . El dominio está vivo.
    
## 4- Escaneo de puertos (Nmap)
````
PORT   STATE    SERVICE
1/tcp  filtered
2/tcp  filtered
...
10/tcp filtered
````
Cuando sale el estado como *"filtered"* significa que no se recibe respuesta directa del puerto, no sabiendo si está abierto (open) o cerrado (closed).

Interpretación:
    . Los puertos 1–10 están filtrados.
    . Adobe bloquea la mayoría de puertos con firewall/CDN.
    
## 5- Servidores NS y MX con dig
NS (Name Server — DNS):
````
a28-67.akam.net.
a1-217.akam.net.
a26-66.akam.net.
a13-65.akam.net.
a7-64.akam.net.
a10-64.akam.net.
````
MX (Mail Server — correo):
````
2 adobe.mail.protection.outlook.com.
1 adobe-com.mail.protection.outlook.com.
````
### En conclusión: 

- El WHOIS revela que adobe.com es un dominio extremadamente protegido mediante múltiples flags EPP, gestionado por Nom-iq Ltd. (ComLaude).
- Se han identificado correos internos (dns-admin@adobe.com) y de abuso del registrador (abuse@comlaude.com).
- No se han encontrado filtraciones asociadas a esos correos mediante IntelX.
- El dominio responde a ping correctamente, confirmando que está activo.
- Un escaneo Nmap básico muestra todos los puertos 1–10 filtrados, indicando presencia de firewall/CDN.
- La infraestructura DNS corre sobre Akamai (akam.net), mientras que los servidores MX están gestionados por Microsoft (Exchange Online Protection).

  
## 2. Obtener las credenciales cifradas de un sistema Windows y otro Linux, analizar el algoritmo utilizado para cifrar dichas claves, y verificar las diferentes opciones para romperlo mediante el uso de Hashcat. Así mismo, el alumno deberá realizar la búsqueda de posibles diccionarios ya creados, así como Rainbow tables, y comprobar la diferencia y efectividad de ambos casos.
