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
2. Matar procesos con airmon-ng check kill
3. Activar modo monitor con airmon-ng start wlan0
4. Detectar red víctima con airodump-ng
5. Configurar hostapd-wpe.conf con el SSID víctima
6. Levantar el punto de acceso falso
7. Simular conexión de la víctima
8. Capturar hash NetNTLM
9. Preparar diccionario rockyou.txt
10. Crackear con John the Ripper

## Documentación

El informe completo está disponible en /docs/Auditorias_redes_twin.pdf

## Aviso legal

Este laboratorio se realizó en un entorno controlado con fines exclusivamente
educativos. El uso de estas técnicas fuera de entornos autorizados es ilegal.

## Autor

Leonardo David Páez Gutiérrez
