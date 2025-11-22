# GUÍA DE DESPLIEGUE: Servidor Oracle Database 21c XE (Modo Headless)

**Objetivo:** Montar un servidor de base de datos Oracle profesional sobre Linux sin interfaz gráfica (ahorrando RAM y disco), descargando el software directamente desde la terminal.
**Requisitos del Alumno:** Portátil Windows o Mac (Intel). **NO compatible con Mac Apple Silicon (M1/M2/M3)**.

-----

## FASE 1: Configuración de la Máquina Virtual (VirtualBox)

Vamos a crear una máquina que simula un servidor real en un rack (sin pantalla gráfica).

1.  **Nueva Máquina:**

      * **Nombre:** `OracleServer_21c`
      * **Tipo:** Linux / **Versión:** Oracle Linux (64-bit).
      * **Memoria RAM:** Asignar **3072 MB** (3 GB).
          * *Nota:* Al no tener interfaz gráfica, con 3GB va sobrada. Menos de 2.5GB puede hacer fallar la instalación de la base de datos.
      * **Disco Duro:** 25 GB - 30 GB (Reservado dinámicamente).

2.  **Configuración de RED (Vital para conectar desde fuera):**

      * Configuración \> Red \> Adaptador 1 \> **NAT**.
      * Botón **Avanzadas** \> **Reenvío de puertos**.
      * Añadir regla:
          * Nombre: `Oracle` | Protocolo: `TCP` | IP Anfitrión: `127.0.0.1` | Puerto Anfitrión: `1521` | Puerto Invitado: `1521`

3.  **Cargar ISO:**

      * En Almacenamiento, carga la ISO de **Oracle Linux 8**.

-----

## FASE 2: Instalación del Sistema Operativo (Modo Texto)

Arranca la VM. Sigue estos pasos en el instalador:

1.  **Idioma:** English (recomendado para servidores) o Español.
2.  **Selección de Software (EL PASO CLAVE):**
      * Pincha en "Software Selection".
      * Selecciona: **"Server"** (en la columna izquierda).
      * *NO marques "Server with GUI"*.
      * Asegúrate de que en la derecha NO hay nada marcado
3.  **Red (Network & Hostname):**
      * Activa el interruptor **Ethernet (ON)**. *Si no haces esto, no tendrás internet para el wget.*
      * Ponle nombre al host: `oracle-server`.
4.  **Destino de instalación:** Automático (Entrar y salir).
5.  **Usuario:**
      * Password de **Root**: `oracle`
      * Crear usuario: `alumno` / Password: `alumno`.
      * *Marca la casilla: "Make this user administrator" (para poder usar sudo).*
6.  **Instalar y Reiniciar.**

-----

## FASE 3: Instalación de Oracle Database 21c XE

Al reiniciar, verás una pantalla negra pidiendo login.

  * **Login:** `alumno`
  * **Password:** `alumno`

Ahora trabajaremos todo por línea de comandos.

### 1\. Instalar dependencias y herramientas

Oracle Linux trae un paquete que configura el kernel, usuarios y grupos automáticamente. También aseguramos tener `wget`.

```bash
# Instalar pre-configuración de Oracle y wget
sudo dnf install -y oracle-database-preinstall-21c wget
```

### 2\. Descargar Oracle 21c XE

Descargamos el instalador directamente al servidor usando el enlace directo.

```bash
# Ir a carpeta temporal o de usuario
cd ~

# Descargar el RPM (usamos --no-check-certificate por si acaso)
wget https://download.oracle.com/otn-pub/otn_software/db-express/oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm
```

*(Esto tardará dependiendo de la conexión a internet de la clase).*

### 3\. Instalación del binario

Una vez descargado (puedes comprobarlo con `ls -lh`), instalamos el RPM:

```bash
sudo dnf localinstall -y oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm
```

### 4\. Borrar el instalador (Limpieza)

Como ya está instalado, borramos el archivo descargado para recuperar 2GB de espacio en disco.

```bash
rm oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm
```

### 5\. Configuración de la Instancia

Ahora creamos la base de datos real.

```bash
sudo /etc/init.d/oracle-xe-21c configure
```

Te pedirá:

1.  **Password para SYS, SYSTEM y PDBADMIN:** Escribe `oracle` (no se verá nada al escribir).
2.  **Autostart:** Escribe `y` para que arranque sola al encender la VM.

*Tiempo de espera: 5 - 15 minutos. Paciencia. Debe terminar diciendo "Database created successfully".*

-----

## FASE 4: Configuración Post-Instalación (SysAdmin)

### 1\. Abrir el Firewall

Por defecto Linux rechaza conexiones externas. Abrimos el puerto 1521.

```bash
sudo firewall-cmd --permanent --add-port=1521/tcp
sudo firewall-cmd --reload
```

### 2\. Configurar Variables de Entorno

Para poder usar comandos como `sqlplus` cómodamente, configuramos el perfil del usuario `alumno` (y root si se quiere).

```bash
# Añadimos las variables al fichero de perfil del usuario
echo 'export ORACLE_SID=XE' >> ~/.bash_profile
echo 'export ORACLE_HOME=/opt/oracle/product/21c/dbhomeXE' >> ~/.bash_profile
echo 'export PATH=$PATH:$ORACLE_HOME/bin' >> ~/.bash_profile

# Recargamos el perfil para que tenga efecto ya
source ~/.bash_profile
```

### 3\. Prueba local

Comprueba que la base de datos funciona antes de irte a Windows:

```bash
sqlplus system/oracle
```

*Si entras al prompt `SQL>`, felicidades, tu servidor está operativo. Escribe `exit` para salir.*

-----

## FASE 5: Conexión Remota (SQL Developer)

Ahora minimiza la máquina virtual (no la cierres). Abre **SQL Developer** en tu sistema anfitrión (Windows/Mac).

Crea una nueva conexión con estos datos **EXACTOS**:

| Parámetro | Valor |
| :--- | :--- |
| **Name** | `Clase_Oracle_21c` |
| **Username** | `system` |
| **Password** | `oracle` |
| **Hostname** | `localhost` |
| **Port** | `1521` |
| **SID** | `xe` |
| **Service Name** | (Dejar vacío si usas SID) |

**Nota Importante sobre 21c:**

  * Conectando por **SID: xe** entras a la base de datos raíz (CDB).
  * Si en el futuro necesitamos conectar a la Pluggable Database (donde se suelen crear los datos de usuario), usaremos **Service Name: `XEPDB1`**. De momento, usad SID `xe`.

<!-- end list -->

1.  Pulsa **Probar (Test)**. Debe dar "Estado: Correcto".
2.  Pulsa **Guardar** y **Conectar**.

## FASE 6: Configuración del Auto-arranque (Autostart)

A veces, el asistente de configuración omite la pregunta de si queréis que la base de datos arranque sola al encender la máquina. Para asegurarnos de que no tenéis que arrancarla manualmente cada día, vamos a configurarlo a nivel de sistema operativo.

### 1\. Habilitar el servicio en Linux

Linux utiliza un gestor llamado `systemd` para controlar qué programas se inician al arrancar. Vamos a decirle que Oracle es prioritario.

Ejecutad estos comandos en la terminal:

```bash
# Recargar la configuración del gestor de servicios
sudo systemctl daemon-reload

# Habilitar el servicio para que arranque en el próximo reinicio
sudo systemctl enable oracle-xe-21c
```

Podéis verificar si se ha activado correctamente escribiendo:

```bash
sudo systemctl is-enabled oracle-xe-21c
```

*(Debe responder: `enabled`)*

### 2\. Verificar el fichero "oratab"

Existe un fichero interno de Oracle que funciona como un interruptor maestro. Si está apagado, la base de datos no subirá aunque Linux se lo pida. Vamos a revisarlo.

Mostrad el contenido del fichero:

```bash
cat /etc/oratab
```

Buscad la última línea. Debe ser parecida a esta:
`XE:/opt/oracle/product/21c/dbhomeXE:Y`

  * Si al final veis una **`Y`** (Yes), ¡perfecto\! No tenéis que hacer nada más.
  * Si veis una **`N`** (No), tenéis que cambiarla.
    1.  Editad el fichero: `sudo nano /etc/oratab`
    2.  Moveos con las flechas hasta la **N** y cambiadla por una **Y**.
    3.  Guardad pulsando `Ctrl + O` (Enter) y salid con `Ctrl + X`.

### 3\. La prueba final

Para estar 100% seguros, reiniciad la máquina virtual:

```bash
sudo reboot
```

Esperad unos 2 minutos a que arranque de nuevo (recordad que no veréis nada gráfico, solo el login). Sin tocar nada en la consola de Linux, intentad conectaros desde **SQL Developer** en vuestro Windows/Mac.

## FASE 7: Creación de la Base de Datos "DAVANTE"

En el mundo profesional de Oracle, no trabajamos directamente sobre la base de datos del sistema. Vamos a crear una **Base de Datos Enchufable (PDB)** específica para el curso llamada `DAVANTE`.

### 1\. Acceder a SQLPlus como Administrador

Desde la terminal de Linux (logueados como usuario `alumno`), entramos a la consola de gestión de la base de datos:

```bash
sqlplus sys/oracle as sysdba
```

*(El prompt cambiará a `SQL>`).*

### 2\. Crear la Pluggable Database (PDB)

Ejecutad el siguiente comando con cuidado (respetad los puntos y comas). Esto creará el contenedor `davante` y un usuario administrador para el mismo.

```sql
-- 1. Configuramos la ruta por defecto para crear archivos de datos
ALTER SYSTEM SET db_create_file_dest = '/opt/oracle/oradata' SCOPE=BOTH;

-- 2. Ahora sí, ejecutamos de nuevo la creación de la PDB
CREATE PLUGGABLE DATABASE davante 
ADMIN USER administrador IDENTIFIED BY administrador 
ROLES=(DBA);
```

*Debe responder: `Pluggable database created.`*

### 3\. Abrir la base de datos y guardarla

Por defecto, las nuevas bases de datos nacen "cerradas". Tenemos que abrirlas y decirle a Oracle que queremos que se queden abiertas para siempre (incluso tras reiniciar).

```sql
-- Abrir la base de datos DAVANTE
ALTER PLUGGABLE DATABASE davante OPEN;

-- Guardar el estado para que arranque automáticamente en el futuro
ALTER PLUGGABLE DATABASE davante SAVE STATE;
```

### 4\. Comprobar que existe

Podéis ver vuestras bases de datos con este comando:

```sql
show pdbs;
```

Deberíais ver algo así:

```text
    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 XEPDB1                         READ WRITE NO
         4 DAVANTE                        READ WRITE NO  <-- ¡Aquí está!
```

### 5\. Crear el usuario de trabajo (Opcional pero recomendado)

Hasta ahora hemos creado la "caja" (la base de datos) y su dueño (`administrador`). Para trabajar en clase día a día, es buena práctica crear un usuario normal dentro de esa base de datos.

Primero, nos cambiamos a la base de datos DAVANTE:

```sql
ALTER SESSION SET CONTAINER = davante;
```

Ahora creamos el usuario (por ejemplo, `alumno`):

```sql
-- Crear usuario
CREATE USER alumno IDENTIFIED BY alumno;

-- Darle permisos para todo (es entorno de desarrollo)
GRANT CONNECT, RESOURCE, DBA TO alumno;
```

Escribid `exit` para salir de SQLPlus.

-----

### 6\. Conectar a DAVANTE desde SQL Developer

Ahora volved a vuestro Windows/Mac. Vamos a crear la conexión definitiva que usaréis todo el curso.

1.  **SQL Developer** \> **Nueva Conexión**.
2.  Datos de configuración:

| Campo | Valor |
| :--- | :--- |
| **Name (Nombre)** | `DAVANTE_Curso` |
| **Username** | `alumno` (o `administrador`) |
| **Password** | `alumno` (o `administrador`) |
| **Hostname** | `localhost` |
| **Port** | `1521` |
| **Service Name** | `davante` |

**¡OJO IMPORTANTE\!**
Fijaos que ahora **NO** usamos "SID". Tenéis que cambiar el selector (radio button) a **Service Name** y escribir `davante` (en minúsculas suele funcionar mejor si se creó así, aunque Oracle es case-insensitive para esto, mejor prevenir).

3.  Clic en **Probar**. (Debe dar Correcto).
4.  Clic en **Guardar** y **Conectar**.

-----

**¡ENHORABUENA\!**
Ya tenéis el entorno completo: Servidor Linux optimizado y una base de datos `DAVANTE` lista para recibir tablas y consultas.

-----

### Anexo: Chuleta de comandos útiles

Si apagas la VM y al volver a encenderla la base de datos no responde, entra en la consola negra y usa:

  * **Ver estado:** `sudo /etc/init.d/oracle-xe-21c status`
  * **Arrancar:** `sudo /etc/init.d/oracle-xe-21c start`
  * **Parar:** `sudo /etc/init.d/oracle-xe-21c stop`