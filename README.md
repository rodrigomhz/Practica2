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
- **`ping`**: Para comprobar si el dominio está activo.
- **`jq`**: Herramienta para procesar respuestas JSON de la API de IntelX. JQ nos permite procesar datos json, pudiendo en esta caso buscar la existencia de data.

- **Hay que recordad la necesidad de darle permisos de ejecuación, podemos hacerl con el comando:**
    ```
       chmod +x script
    ````

## 2. Obtener las credenciales cifradas de un sistema Windows y otro Linux, analizar el algoritmo utilizado para cifrar dichas claves, y verificar las diferentes opciones para romperlo mediante el uso de Hashcat. Así mismo, el alumno deberá realizar la búsqueda de posibles diccionarios ya creados, así como Rainbow tables, y comprobar la diferencia y efectividad de ambos casos.
