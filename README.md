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

De un forma sencilla, un hash es una forma de identificar un archivo. Es muy común que muchas contraseñas vengan "hasheadas", es por eso que mediante el uso de diccionarios intentamos descifrar una contraseña hasheada.

   ### 1) Identificar metodos de cifrado de Windows y Linux
   Para poder identificar los metodos de cifrado de una clave existe una herramienta llamada *hash-identifier* (que ya viene instalada por defecto en kali). Pero antes debemos obtener los hashes de Windows y Linux.

   ## Windows
   Haremos uso de la herramienta **Mimikatz**, teniendola que descargar (https://github.com/gentilkiwi/mimikatz/releases)
   
   1) Ejecutar Mimikatz como administrador:
   
   - Abre una ventana de CMD como administrador. 
   - Navega hasta la carpeta donde tienes Mimikatz.
     
   ![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/consola.png)
  
   ````
   cd x64
   mimikatz.exe
   ````
   ![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/mimikatz.png)
  
- Extraer los hashes:
Dentro de Mimikatz, primero debes activar el modo de depuración para obtener privilegios. En la línea de comandos de Mimikatz, escribe:
````
privilege::debug
````
Y luego, ejecuta el siguiente comando para extraer los hashes de las contraseñas:
````
sekurlsa::logonpasswords
````
De aquí nos interesa es el hash NTLM, que en nuestro caso es: ```` 6A1058A7CC3F0B083F219462A076A768 ````

## Linux
En Linux, las contraseñas se guardan en el archivo /etc/shadow. Este archivo es donde se encuentran los hashes de las contraseñas cifradas.

1 ) Accedemos con:
  
````      
sudo cat /etc/shadow
````

Obtenemos el siguiente texto: 
````
calvo:$y$j9T$Eq1ZJiOBuLw2dr464g5sh/$wnQR8oZYZlSwTf1/WXsAHo8mcOzPban/r21woFLZmm3:20352:0:99999:7:::
````

### Uso de hash-identifier
Esto es imprescindible, porque luego con hashcat debemos decir que hash se está utilizando:

**Windows**

   ![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/hashidWin.png)

**Linux**

   ![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/hashidLin.png)

Pese a que diga que no lo encuentra, en principio este hash está utilizando el algoritmo SHA-512 con un salt aleatorio para mejorar la seguridad. Es un hash comúnmente utilizado en sistemas Linux para proteger las contraseñas de los usuarios.

### 1) Uso de hashcat
Con man podemos ver que poner según el tipo de ataque que quiero utilizar.
### Comando para Hashcat (Windows NTLM):
````
hashcat -m 1000 -a 0 -o resultado.txt windows_hash.txt rockyou.txt
````
### Comando para Hashcat (para yescrypt)
````
hashcat -m 18000 -a 0 -o resultado.txt linux_hash.txt rockyou.txt
````

"Hashcat necesita mucha potencia para funcionar, por lo que puede dar error".

En caso de funcionar, nos devolverá: **hash:constraseñaHasheada**

Nosotros en este caso hemos usado un diccionario (rockyou), pero antes de terminar vamos a diferenciar los diccionarios de las rainbow tables para ver que nos vendría mejor según nuestro caso:

Diccionarios:
- Qué son: Listas de contraseñas comunes.
- Ventajas: Rápidos, fáciles de usar.
- Desventajas: Solo rompen contraseñas presentes en la lista.

Rainbow Tables:
- Qué son: Tablas precomputadas de hashes de contraseñas.
- Ventajas: Muy rápidos para hashes precomputados.
- Desventajas: Ocupan mucho espacio y solo funcionan para un tipo de hash específico.

Diferencia:

- Diccionarios son más flexibles, pero menos eficaces con contraseñas complejas.

- Rainbow Tables son más rápidas, pero limitadas por el tipo de hash y su tamaño.



## 3. Desarrollar el proceso completo de explotación sobre la máquina Windows 2008. El alumno deberá identificar todas las posibles vulnerabilidades para el acceso a la máquina, así como la elevación de privilegios en la misma. 

https://liveutad-my.sharepoint.com/:u:/g/personal/eduardo_arriols_u-tad_com/EYaDZBfKhgZMoGo1YmAVZ-YBJYFJEKGVxor3xFjR64Vwkg?e=2xeXRI

Para el desarrollo de las pruebas sobre la máquina vulnerable, el alumno deberá crear una red NAT virtualizada con la configuración 10.0.2.0/24.

### 1) Configurar la red Nat en la 10.0.2.0

. Configuramos la red Nat en nuestro VirtualBox

![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/NatConfig1.png)

. Metemos la máquina dentro de nuestra red Nat

![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/NatConfig1.png)

Lo primero que hacemos es en nuestra máquina atacante es usar Nmap para encontrar la IP de nuestra máquina windows2008, es por ello que vamos a usar el siguiente comando:
````
nmap -sS -sV -O 10.0.2.5/24 -T5
````
En este caso lo que nos interesa interesa es el -O, que nos dice nuestro sistema operativo, y buscando el windows.

![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/Nmap.png)

Aquí vemos toda la info (incluida la IP) de la máquina windows.

Ya con esto desde nuestra máquina atacante usaremos **nessus**, para poder encontar vulnerabilidades de la máquina (10.0.2.26).

![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/Scan.png)

![Salida del script](https://github.com/rodrigomhz/Practica2/raw/main/Images/Resultado.png)

   
   
