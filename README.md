## Laboratorio: Ataque Evil Twin (WPA) 

Laboratorio educativo sobre la técnica Evil Twin aplicada a redes WPA, realizado en entorno controlado con fines académicos.

## Descripción 

El ataque Evil Twin consiste en crear un punto de acceso falso que suplanta
una red legítima para interceptar credenciales de autenticación mediante
el protocolo MSCHAP (hash NetNTLM).

## Herramientas utilizadas

| Herramienta | Función |
|-------------|-------------------------------|
| airmon-ng   | Activar modo monitor          |
| airodump-ng | Escanear redes                |
| hostapd-wpe | Crear punto de acceso falso   |
| john        | Crackear hash NetNTLM         |
| rockyou.txt | Diccionario de contraseñas    |

## Pasos del ataque

1. Verificar antena con iwconfig

Lo primero es comprobar que el sistema reconoce nuestra antena inalámbrica. Vemos que aparece como wlan0, lo que confirma que está correctamente detectada y lista para usarse.
Comando: iwconfig

<img width="893" height="291" alt="image" src="https://github.com/user-attachments/assets/4ade2bd3-ef91-406f-90f9-ab266af0479f" />


2. Matar procesos con airmon-ng check kill
Antes de poner la antena en modo monitor, necesitamos detener cualquier proceso que pueda interferir.
Comando: sudo airmon-ng check kill
<img width="791" height="363" alt="image" src="https://github.com/user-attachments/assets/74658f5d-1a1b-4aea-952a-87ef91e62f8c" />

3. Activar modo monitor con airmon-ng start wlan0
Ahora ponemos la antena en modo monitor. Este modo nos permite capturar y transmitir paquetes de red sin estar asociados a ninguna red, algo imprescindible para lanzar el punto de acceso falso.
Comando: sudo airmon-ng start wlan0
<img width="886" height="130" alt="image" src="https://github.com/user-attachments/assets/f0e6613a-5e12-412c-a6e9-014fd3374ac7" />

Comprobamos de nuevo con iwconfig y vemos que la interfaz ahora figura en Mode: Monitor.
<img width="871" height="256" alt="image" src="https://github.com/user-attachments/assets/c8df2f8a-4fd5-4236-8a5f-abc58f67a996" />


4. Detectar red víctima con airodump-ng
Ahora es momento de escanear la red en busca del SSID que usaremos para suplantar a nuestra victima
Comando: sudo airodump-ng wlan0
<img width="886" height="246" alt="image" src="https://github.com/user-attachments/assets/ab356cc2-8f90-4e16-a49b-632ebc961e5b" />

<img width="886" height="104" alt="image" src="https://github.com/user-attachments/assets/f3dd0435-d795-4e57-a34f-b71db71c6094" />

5. Configurar hostapd-wpe.conf con el SSID víctima
Editamos el archivo de configuración de hostapd-wpe, que es la herramienta que nos permitirá levantar el punto de acceso falso con soporte para WPA. Los dos parámetros clave que modificamos son:
•	ssid: lo ponemos igual que la red víctima para que sea convincente
•	interface: verificamos que apunta a wlan0
Comando: Sudo nano /etc/hostapd-wpe/hostapd-wpe.conf
<img width="566" height="75" alt="image" src="https://github.com/user-attachments/assets/39a966fb-0437-4cc1-a9c6-0a19da5bf0de" />

<img width="886" height="276" alt="image" src="https://github.com/user-attachments/assets/3243d225-3eb2-4e4f-bbf7-71dcc2df6fc5" />

6. Levantar el punto de acceso falso
Lanzamos el Evil Twin. Ahora mismo tenemos activo un punto de acceso que se hace pasar por la red legítima. Cuando una víctima intente conectarse e introduzca sus credenciales, hostapd-wpe las interceptará.

<img width="689" height="90" alt="image" src="https://github.com/user-attachments/assets/cba954de-c741-491f-bdd0-0bd5beaf7146" />

7. Simular conexión de la víctima
Nos conectamos al punto de acceso falso desde un dispositivo, introduciendo cualquier contraseña. En un entorno real, la víctima creería estar autenticándose en la red corporativa.

<img width="254" height="213" alt="image" src="https://github.com/user-attachments/assets/6623ec85-42ae-4615-87e5-89277041fc38" />

8. Capturar hash NetNTLM
En la terminal donde corre hostapd-wpe vemos que ha capturado un hash NetNTLM. Este hash contiene las credenciales del usuario de forma cifrada.

<img width="886" height="396" alt="image" src="https://github.com/user-attachments/assets/c732a521-b37b-4862-b8de-f66dbf95871e" />

Lo copiamos para el siguiente paso y guardamos en un fichero en texto plano

<img width="886" height="55" alt="image" src="https://github.com/user-attachments/assets/a4669b55-7ef3-454a-abe2-2140ec83acf8" />

9. Preparar diccionario rockyou.txt
Descomprimimos el famoso diccionario rockyou.txt y verificamos que está disponible. Este diccionario contiene millones de contraseñas reales filtradas en brechas anteriores y es el estándar en ejercicios de cracking

<img width="886" height="103" alt="image" src="https://github.com/user-attachments/assets/bbb7af4a-0a14-44da-b74d-82f23e14aa77" />

10. Crackear con John the Ripper
Usamos John the Ripper para realizar un ataque de diccionario contra el hash capturado. Si la contraseña de la víctima está en el diccionario, John la encontrará y nos la mostrará en texto plano.
Comando: john --wordlist=/usr/share/wordlists/rockyou.txt hashtwin.txt
 
<img width="886" height="77" alt="image" src="https://github.com/user-attachments/assets/435b95ac-c151-4427-8529-22e4004739d8" />

<img width="886" height="229" alt="image" src="https://github.com/user-attachments/assets/981c786d-d91d-47bf-9d51-20755b6044d9" />

Obteniendo las credenciales de la red.

Como conclusión, este ataque demuestra que WPA, puede ser comprometido si los usuarios no verifican el certificado del servidor antes de autenticarse.
La contramedida principal es configurar correctamente la validación del certificado en los dispositivos cliente, de forma que rechacen puntos de acceso que no lo presenten correctamente.


## Documentación

El informe completo está disponible en Auditorias_redes_twin.pdf

El video se puede ver desde el siguiente enlace: 
https://gvaedu-my.sharepoint.com/:v:/g/personal/leopaegut_alu_edu_gva_es/IQB_qOmw4OW_TZbvD3-h1FA7AUhVHw-HXDCzSGIeu8R23PU?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&email=g.velagarcia%40edu.gva.es&e=Cfy8qE

## Aviso legal

Este laboratorio se realizó en un entorno controlado con fines exclusivamente
educativos. El uso de estas técnicas fuera de entornos autorizados es ilegal.

## Autor

Leonardo David Páez Gutiérrez
